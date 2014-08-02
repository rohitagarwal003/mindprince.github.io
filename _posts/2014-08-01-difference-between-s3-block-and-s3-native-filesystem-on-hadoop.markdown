---
layout: post
title: "Difference between S3 block filesystem and S3 native filesystem on Hadoop"
date: 2014-08-01 13:20
comments: true
tags: hadoop aws s3
description: "This post describes the difference between the S3 block filesystem and the S3 native filesystem on Hadoop. It also discusses which URIs (s3:// and s3n://) map to which filesystem."
---

Recently, I tried to use Amazon S3 to store input/output files for hadoop jobs
and got confused by the different S3 filesystems available on top of Apache
Hadoop.  Here are some notes which helped me:

The first S3-backed Hadoop filesystem was introduced in Hadoop 0.10.0
([HADOOP-574](https://issues.apache.org/jira/browse/HADOOP-574)). It was called
the **S3 block fileystem** and it was assigned the URI scheme `s3://`. In this
implementation, files are stored as blocks, just like they are in HDFS. The
files stored by this filesystem are not interoperable with other S3 tools -
what this means is that if you go to the AWS console and try to look for
files written by this filesystem, you won't find them - instead you would find
files named something like `block_-1212312341234512345` etc. Similarly, this
filesystem won't be able to read pre-existing files on S3 since it assumes a
block-based format.

To overcome these limitations, another S3-backed filesystem was introduced in
Hadoop 0.18.0 ([HADOOP-930](https://issues.apache.org/jira/browse/HADOOP-930)).
It was called the **S3 native filesystem** and it was assigned the URI scheme
`s3n://`. This filesystem lets you access files on S3 that were written with
other tools. Conversely, other tools can access files written using Hadoop.
When this filesystem was introduced, S3 had a filesize limit of 5GB and hence
this filesystem could only operate with files less than 5GB. In late 2010,
Amazon S3 [introduced the Multipart Upload API](http://aws.amazon.com/blogs/aws/amazon-s3-multipart-upload/)
and [raised the file size limit from 5GB to 5TB](http://aws.amazon.com/blogs/aws/amazon-s3-object-size-limit/).
A lot of Hadoop distributions and HaaS providers added support for this quite
early and it landed in Apache Hadoop in version 2.4.0
([HADOOP-9454](https://issues.apache.org/jira/browse/HADOOP-9454))

Using the S3 block file system is no longer recommended. Various
Hadoop-as-a-service providers like [Qubole](http://www.qubole.com) and
[Amazon EMR](http://aws.amazon.com/elasticmapreduce/) go as far as mapping both
the `s3://` and the `s3n://` URIs to the S3 native filesystem to ensure this.
