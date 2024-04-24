---
layout: post
title:  "Investigating Spark Operator issues"
date:   2024-04-24 12:00:00 +0000
categories: 
    - kubernetes
    - big_data
    - spark
    - operators
---
I’ve been setting up Spark to run on our Kubernetes cluster & hit an issue with the [spark-operator](https://github.com/kubeflow/spark-operator/) to run spark jobs on Kubernetes. I need to mount a configmap into the spark container when it executes a job, this is [supported](https://github.com/kubeflow/spark-operator/blob/master/docs/user-guide.md#writing-a-sparkapplication-spec) in the [SparkApplication yaml](https://github.com/kubeflow/spark-operator/blob/master/examples/spark-pi-configmap.yaml) used to submit jobs, but when executing the job, the containers were not mounting the configMap.

Searching around, I found [this issue](https://github.com/kubeflow/spark-operator/issues/946) which seemed to match the problems I was encountering & seemed to point to the operator requiring webhook to be configured to modify the pod spec dynamically. Checking my config, the webhook was installed & available, so that was a bit of a dead end... However reviewing the spark-operator logs I did see something odd:

```
http: TLS handshake error from x.x.x.x:yyyyy: remote error: tls: bad certificate
```

Searching for this, brought up this [issue](https://github.com/kubeflow/spark-operator/issues/1004), which indicated restarting the operator would fix it. The supposed problem in this issue was fixed in 2020, but I'm using a later version of the operator & restarting repeatably didn't seem a great option. 

A [temporary fix was suggested in another issue](https://github.com/kubeflow/spark-operator/issues/1168), adding a liveness check to kill the container when the certificates are out of sync, but that's just patching round the problem, not idea for a production system.


```yaml
livenessProbe:
  initialDelaySeconds: 1
  periodSeconds: 1
  failureThreshold: 1
  exec:
    command:
      - sh
      - -c
      - |
        set -e
        curl -iks -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
          https://kubernetes.default.svc/apis/admissionregistration.k8s.io/v1/mutatingwebhookconfigurations/{{ include "spark-operator.fullname" . }}-webhook-config \
          | grep -o '"caBundle": "[^"]*"' \
          | awk -F'"' '{print $4}' \
          | base64 -d > /tmp/expected_ca_bundle.crt
        expected_ca_bundle=$(cat /etc/webhook-certs/ca-cert.pem)
        actual_ca_bundle=$(cat /tmp/expected_ca_bundle.crt)
        if [ "$expected_ca_bundle" != "$actual_ca_bundle" ]; then
          exit 1
        fi
```

But, why was the webhook failing at all, why was it only for some people & why were only (some) helm chart users affected?

Excellent [detective work by jgeores](https://github.com/kubeflow/spark-operator/issues/1179#issuecomment-788796683) finally made the problem make sense to me - the webhooks certificates are created by an init container when the spark-operator is installed by the helm chart. When a helm upgrade occurs, the spark-operator init container can be restarted, recreating the certificates, but the operator pod itself won't be restarted - leading to it holding old certificates. It then fails to access the webhook. 

But I hadn't upgraded the helm chart since installing it... but I had installed it using [Fleet](https://fleet.rancher.io/) CI which would use helm upgrades to keep the operator up to date with is deployment config. Doing things in a reproducable, infrastructure as code way had been my downfall!

Removing spark-operator from Fleet isn't great, we are using it to allow us to use CI/CD to keep all our Kubernetes applications deployed and up to date, but it's the quick 'fix' for now. Now, do I go down the rabbit hole of fixing the helm chart...