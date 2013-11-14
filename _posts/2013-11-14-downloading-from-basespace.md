---
layout: post
title: "Downloading from BaseSpace"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

> Downloading files from BaseSpace using your browser can be inconvient and it is often desirable to download files directly within linux.  Here is a method using wget to do this (HT Nick Loman).

First you save the cookies into a file, and post the login data:

``` bash
wget --keep-session-cookies --save-cookies cookies.txt --post-data="Username=XXXXX@bham.ac.uk&Password=XXXXX" "https://icom.illumina.com/login?service=basespace"
```

Then download the file:

``` bash
wget --load-cookies cookies.txt --keep-session-cookies "https://basespace.illumina.com/sample/2829878/files/tree/Ecoli_S1_L001_R1_001.fastq.gz?id=163164391"
```
