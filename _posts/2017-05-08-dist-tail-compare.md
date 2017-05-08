---
layout: post
title: "Compare the tail of two empirical distributions"
date: 2015-05-08
---
<h2>The problem and relevant issues</h2>
By constructing a threshold, saying lambda, we can test equality of two means or variances of the two distributions restricted on the tail region (\lambda, infinity) based on two data sets of observations falling in this tail region. Now the problem would be how to compare the mean or variance. The two sample t-test or F-test may be OK but not be poweful since random variable restricted on this tail region is not normal even the original ones are. <b>A tail may have practically any distribution. </b>
The Chi Square test (Goodness-of-Fit test) may not be appropriate for identifying differences in tails. The Chi Square test is structured to compare two distributions by buckets of values (graphically represented by a histogram). And, the tails will consist in the far most buckets. If the tails are covered by many bins, then--because they are tails!--there may be few data in any of the bins, invalidating the chi-squared approximation (The chi-squared test is based on a Normal-theory approximation to the true distribution of the chi-squared statistic. Typically this approximation gets poor when bin populations drop below 5). If the tails are covered by few bins, then you lose almost all power to discriminate their shapes, and what you do manage to discriminate might not be relevant or useful.
Some Notes
<ul>
<li>(In case you, like me, errorously thought that extreme value distributions had some magic power on this issue...) Extreme value theory actually says little about the tails: it focuses on the distribution of the maxima (or minima) of iid samples</li>
</ul>

<h2>Possible solution 1</h2>

<h2>Possible solution 2</h2>
See (Gao et al., 2008) in the reference
The Wang-Allison tests (Wang et al., 2004) test the following null hypothesis:
H0,A : P (Y > τ|X = 1) = P(Y > τ|X = 0).
A problem with the Wang-Allison tests is that, hypothetically, P (Y > τ|X = 1) may equal to P (Y > τ|X = 0) and yet the average magnitude by which Y exceed τ when X = 1 may be radically different than when X = 0. This is exemplified in the hypothetical frequency distributions depicted below.
<img src="https://cloud.githubusercontent.com/assets/5496192/25804501/13916cee-33ca-11e7-92ea-4eed45e55661.png" /> 
<h2>Reference</h2>
<ul>
<li>Wang C, Li Q, Redden DT, Weindruch R, Allison DB: Statistical methods for testing effects on "maximum lifespan". Mech Ageing Dev 2004, 125(9):629-32. Erratum in: Mech Ageing Dev 2006, 127(7):652</li>
<li>Gao, Guimin, et al. "Testing for differences in distribution tails to test for differences in'maximum'lifespan." BMC medical research methodology 8.1 (2008): 49.</li>
</ul>
