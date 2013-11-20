---
layout: post
title: "Diagnosing phasing problems"
description: "Diagnosing phasing problems"
category: ""
tags: [illumina, phasing, pre-phasing]
---
{% include JB/setup %}

> If you do Illumina sequencing you probably hear the words 'phasing' and 'pre-phasing' pretty regularly, but what does it mean exsactly and why is it important? Well, with MiSeq read lengths now at 300 and HiSeq HT soon to be 125, keeping phasing and pre-phasing levels under control will become increasingly important. Nothing can bring down a long read like high phasing or pre-phasing, and throwing higher densities into the mix only makes the problem worse. Here is a quick guide to troubleshooting high phasing and pre-phasing issues.


## What is phasing? ##
<br>
In sequencing-by-synthesis chemistry like Illumina (sorry, Solexa!) phasing is the rate at which single moleules within a cluster loose sync with each other. Phasing is falling behind, pre-phasing is going ahead and together they describe how well the chemistry is performing.

Low numbers are better! Values of 0.10/0.10 mean 0.10% of the molecules in your cluster are both falling behing AND 0.10% are running ahead at EACH cycle. In other words 0.20% of the true signal is lost each cycle and will therefore contribute to noise. Another example, 0.20/0.20 means that 0.4% of the true signal is lost per cycle, so after 250 cycles (without correction) your noise would be equal to your signal.

The reason it is calculated is so RTA can apply the correct level of *phasing correction*, which is why you can sequence for 250 bases without making random basecalls! This works by artifically *pushing* signal in or out of each channel based on basecalls before or after it and is an essential process in the Illumina basecaller.

Historically, the phasing and pre-phasing were estimated over the first 12 cycles of each read and then applied to all subsequent cycles. However with MCS 2.4 on the MiSeq we see a new algorithm called [empiricle phasing correction](http://res.illumina.com/documents/products/technotes/technote_low_diversity_rta.pdf) which optimises the phasing correction at every cycle by trying a range of corrections and selecting the one which results in the highest chastity (signal purity). This has major benefits as it means that the correction no longer assumes a linear phasing correction for the whole read, and does not rely on an accurate estimate over the first 12 cycles (better for [low diversity](http://pathogenomics.bham.ac.uk/blog/2012/08/sequencing-low-diversity-libraries-on-illumina-miseq/) samples). The only cost of this computational which is why it is not yet available for HiSeq. The new algorithm stores a new text file under 

```
D:\Illumina\MiSeqAnalysis\131118_M00875_0072_000000000-A6B08\Data\Intensities\BaseCalls\Phasing\EmpiricalPhasingCorrection_1_1_1101.txt
```

Plotting this can help diagnose problems, shown below is a good run and a bad run - can you tell which is which?! In the bottom run the pre-phasing was so bad it actually reached the maximum allowable pre-phasing value of 0.6%.

<img src="/images/emp_phasing2.png" alt="Empirical phasing 2" width="575">
<img src="/images/emp_phasing1.png" alt="Empirical phasing 1" width="575">


## How to recognise high phasing/pre-phasing ##
<br>
It's hard to say what phasing values you 'should have' because it depends on many variables so how do you recognise if you have a problem? Here are a few questions you might ask yourself: 

+ Were the phasing/pre-phasing values higher than usual?
+ Do the quality scores look low?
+ Have you run this sample before without issue?
+ Did the instrument complete without error?
+ Do the thumbnail images look normal?
+ Do the intensity and %base plots look normal?
+ Is there an excessive phasing/pre-phasing gradient down the lane visible on the heatmap?

If the answer to most of these questions is 'Yes!' then you may suspect a phasing/pre-phasing problem. So how do know which it is? A simplified explanation is that phasing is caused by enzyme kinetics while pre-phasing is caused by either inadequate flushing of the flowcell or inadvertant reagent mixing. Here is a representitive (but by no means exhaustive list):


|Cause of phasing         |Comment									
|:------------------------|:----------------------------------------------------------------------------|
|High GC content          |Extreme GC should result in quite high phasing, this is normal		|
|Bad lot number           |Reagents were manufactured inncorrectly					|
|Peltier calibrated low   |Even one or two degrees can effect the enzyme kinetics			|
|Chiller calibrated high  |Chiller temperature should not exceed 6&#176;C				|
|Fludics problem          |Reagents were under-delivered						|
|Shipping problem         |Reagents should not thaw until use, double Mylar wrapping should be unbroken |
|Improper storage         |Reagents should be stored at -20&#176;C			     		|
|Improper handling        |Reagents should be thawed in lukewarm water and used immediately		|


|Cause of pre-phasing   |Comment
|:----------------------|:----------------------------------------------------------------------|
|Fludics problem	|Worn valve, PR2 was under-delivered					|
|Bad lot number		|Reagents were manufactured inncorrectly				|
|Common line or manifold|Common cause of pre-phasing problems					|
|Instrument not washed	|Wash instrument with 0.5% TWEEN in DI water immediately following run 	|
|Shipping problem	|As above								|
|Improper storage	|As above								|
|Improper handling	|As above								|


One last point, if you are running amplicons or other low diversity sample in which the phasing estimation is innacurate the PhiX error rate can sometimes be useful for dianosing problems.

We are very lucky to have a fantastic FAS, Helen who keeps our instruments running very smoothly but if we do have a problem we tend to send her all the information we can on the problem to save time. Hopefully this will help you do the same.




