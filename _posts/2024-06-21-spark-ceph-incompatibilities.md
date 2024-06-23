---
layout: post
title:  "Incompatibilities between Spark 3 and Ceph 18"
date:   2024-06-21 12:00:00 +0000
categories: 
    - kubernetes
    - big_data
    - spark
    - ceph
---


After upgrading Ceph from 17.x to 18.x our Spark jobs started to fail, they were unable to write their output back to S3 buckets hosted on Ceph.

The stacktrace threw up this exception
```
com.amazonaws.services.s3.model.AmazonS3Exception: XAmzContentSHA256Mismatch
```

This error only happened when trying to save a dateframe, Spark to happily save its logs to S3.

Turning debug on and watching the logs showed Spark trying putting a zero byte file on ceph to create a new directory, directories don't exist in S3 until there's a file present so this makes sense, but Ceph is rejecting the SHA256 checksum Spark is calculating for the zero byte file.

Eventually this bug report was tracked down [https://tracker.ceph.com/issues/64090#change-253530](https://tracker.ceph.com/issues/64090#change-253530) which indicates a regression in how Ceph calculates checksums. Until the fix is makes it into a release branch, setting this Spark option works around the issue:

```
spark.hadoop.fs.s3a.signing-algorithm=S3SignerType
```