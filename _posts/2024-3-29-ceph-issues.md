---
layout: post
title:  "Rook Ceph Crashes"
date:   2024-03-29 12:00:00 +0000
categories: 
    - ceph
    - rook
    - kubernetes
    - debugging
---


We were getting a crashing error on Rook-Ceph's operator, that caused it to go into a crashloop. This prevented new disks to be provisioned in Ceph, a major blocker as we're expanding out cluster. No configuration had been changed in the cluster recently, which caused some confusion!

The error was a go panic:

```
panic: assignment to entry in nil map

github.com/rook/rook/pkg/operator/ceph/disruption/clusterdisruption.resetPDBConfig(...)
/home/runner/work/rook/rook/pkg/operator/ceph/disruption/clusterdisruption/osd.go:689
```

Digging into the code brought up this function:

From: [rook/pkg/operator/ceph/disruption/clusterdisruption/osd.go](https://github.com/rook/rook/blob/master/pkg/operator/ceph/disruption/clusterdisruption/osd.go#L658C1-L662C2)

```go
func resetPDBConfig(pdbStateMap *corev1.ConfigMap) {
	pdbStateMap.Data[drainingFailureDomainKey] = ""
	delete(pdbStateMap.Data, drainingFailureDomainDurationKey)
	delete(pdbStateMap.Data, pgHealthCheckDurationKey)
}
```

This seems to be reading a configmap & re-setting values. Looking at the configmaps deployed on our cluster brought up a match - rook-ceph-pdbstatemap, which was strangely empty. This would explain the  'assignment to entry in nil map', the rook operator was expecting a key 'drainingFailureDomainKey' which didn't exist.

I span up a 1 node instance of ceph on minikube to compare the contents of the rook-ceph-pdbstatemap:

```yaml
draining-failure-domain: ''
set-no-out: ''
```

This seemed promising, I added these to the operational ceph cluster & restarted the rook operator - no crashes & the operator was back.

I'm unsure why this config map was empty, possible a bug in the operator caused it to be overwrote or etcd lost the data somehow? One to monitor in the future. 