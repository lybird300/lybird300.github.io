---
layout: post
title: "Compare the tail of two empirical distributions"
date: 2015-05-08
---
<h2>General issues</h2>
<ol>
<li>A tail may have practically any distribution</li>
<li>(In case you, like me, mistakenly thought extreme value distributions have magic power on this problem) Extreme value theory actually says little about the tails: it focuses on the distribution of the maxima (or minima) of iid samples</li>
<li></li>
</ol>

<h2>Possible solution 1</h2>

<h2>Possible solution 2</h2>
Constructing a threshold, saying lambda, we can test equality of two means or variances of the two distributions restricted on the tail region (\lambda, infinity) based on two data sets of observations falling in this tail region. Now the problem would be how to compare the mean or variance. The two sample t-test or F-test may be OK but not be poweful since random variable restricted on this tail region is not normal even the original ones are (see general issue 1).

The Wang-Allison tests (Wang et al., 2004) test the following null hypothesis:
H0,A : P (Y > τ|X = 1) = P(Y > τ|X = 0).
A problem with the Wang-Allison tests is that, hypothetically, P (Y > τ|X = 1) may equal P (Y > τ|X = 0) and yet the average magnitude by which lifespans exceed τ when X = 1 may be radically different than when X = 0. This is exemplified in the hypothetical frequency distributions depicted in Figure 1.

<h2>Reference</h2>
Wang C, Li Q, Redden DT, Weindruch R, Allison DB: Statistical methods for testing effects on "maximum lifespan". Mech Ageing Dev 2004, 125(9):629-32. Erratum in: Mech Ageing Dev 2006, 127(7):652
