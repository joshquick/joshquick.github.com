---
layout: post
title: "Downloading from ENA with Aspera"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

> Although a third party application and a browser plugin, not sounding very good for the command line enthusiast so far, Aspera is the fastest way of downloading large files from the ENA, in a quick test it was 5x faster than using FTP. Here's how you do it:

After installing the Aspera client for Linux you can access the command line function from your home directory:

``` bash
/home/.aspera/connect/bin/ascp -QT -l 300m -i /home/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/ERR000/ERR000000/ERR000000_1.fastq.gz
```

If you want to download files for all the samples in a particular study, the easiest way is to use the Download: TEXT link to download the search results in text form. This contains the addresses for each of the files and you can also use GNU parallel to speed up the download. For example to download the read one fastq files the study 'PRJEB1508', use the following command:

``` bash
parallel --jobs 8 /home/.aspera/connect/bin/ascp -QT -l 300m -i /home/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:{} sra/ :::: <(cut -f 10 PRJEB1508.text | cut -f 1 -d ';' | cut -d '/' -f 2-)
```
