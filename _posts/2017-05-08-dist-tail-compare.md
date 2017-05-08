---
layout: post
title: "Compare the tail of two empirical distributions"
date: 2015-05-08
---
<h2>The problem and relevant issues</h2>
By constructing a threshold, saying lambda, we can test equality of two means or variances of the two distributions restricted on the tail region (\lambda, infinity) based on two data sets of observations falling in this tail region. One relevant issue would be using which tests to compare the mean or variance (elaborate below). Another issue would be how to define the threshold and therefore the "tail region". I suspect both issues would vary with specific contexts. If we are not sure, perhaps we should simulate several reasonable thresholds and try different tests when assessing every possible solutions (introduced below)<br/>
Some Notes:
<ul>
<li>(In case you, like me, errorously thought that extreme value distributions had some magic power on this issue...) Extreme value theory actually says little about the tails: it focuses on the distribution of the maxima (or minima) of iid samples</li>
<li>The two sample t-test or F-test may be OK but not be poweful since random variable restricted on this tail region is not normal even the original ones are. <b>A tail may have practically any distribution. </b></li>
<li>The Chi Square test (Goodness-of-Fit test) may not be appropriate for identifying differences in tails. The Chi Square test is structured to compare two distributions by buckets of values (graphically represented by a histogram). And, the tails will consist in the far most buckets. If the tails are covered by many bins, then--because they are tails!--there may be few data in any of the bins, invalidating the chi-squared approximation (The chi-squared test is based on a Normal-theory approximation to the true distribution of the chi-squared statistic. Typically this approximation gets poor when bin populations drop below 5). If the tails are covered by few bins, then you lose almost all power to discriminate their shapes, and what you do manage to discriminate might not be relevant or useful.</li>
</ul>

<h2>Possible solution 1: use a threshold to select data points and then only compare these data points</h2>
We may have to use non-parametric tests such as the Wilcoxon-Mann-Whitney (exact) test [11,12] or even a permutation test (with t-statistic) as described by Good [13] to test for differences in the values that exceed a certain threshold.

<h2>Possible solution 2: define a threshold but compare all data points</h2>
<p>See (Gao et al., 2008) in the reference. Important excerpt copied below</p>
The Wang-Allison tests (Wang et al., 2004) test the following null hypothesis:
H0,A : P (Y > τ|X = 1) = P(Y > τ|X = 0).
A problem with the Wang-Allison tests is that, hypothetically, P (Y > τ|X = 1) may equal to P (Y > τ|X = 0) and yet the average magnitude by which Y exceed τ when X = 1 may be radically different than when X = 0. This is exemplified in the hypothetical frequency distributions depicted below. In this figure, The left graph is the density for control group (X = 0), 0.9*Weibull(5.73, 106.6)*I(X ≤ 130) + 0.1*Weibull(5.40,100.06)*I(X > 130), and the right graph is the density for treatment group (X = 1), 0.9*Weibull(5.73, 106.6)*I(X ≤130) + 0.1*Weibull(5.45, 130.06)*I(X > 130), where P(Y > τ|X = 1) = P(Y > τ|X = 0) and yet the average magnitude by which Y exceeds τ when X = 1 is different than when X = 0. τ is 90th percentile of the all observations in treatment and control groups.
<img src="https://cloud.githubusercontent.com/assets/5496192/25804501/13916cee-33ca-11e7-92ea-4eed45e55661.png" /> 
An alternative to testing H0,A is to test the following conceptually related but mathematically distinct null hypothesis:
H0,B : μ (Y|Y > τ ∩ X = 1) = μ (Y|Y > τ ∩ X = 0),
where μ (•) denotes the population mean (or expectation) of (•). Though appealing, a problem with testing H0,B is that when P (Y > τ|X = 1) >> P (Y > τ|X = 0) or P (Y > τ|X = 1) << P (Y > τ|X = 0), for any finite sample with equal initial assignment to the two groups, E [n0] <<E [n1] or E [n0] >> E [n1], where E [n0] denotes the expected number of observations in the control group for which Y > τ, and E [n1] denotes the expected number of observations in the treatment group for which Y > τ. This imbalance between E [n0] and E [n1] will greatly reduce the power to reject H0,B. In fact, in the extreme, when either P (Y > τ|X = 1) or P (Y > τ|X = 0) equals to 0, there will be zero power to reject H0,B (actually, it is appropriate to say that H0,B is undefined in such cases). Such a situation is exemplified
in the hypothetical frequency distributions depicted below. In this figure, the left graph is the density for control group (X = 0), 0.9*Weibull(5.07, 93.52)*I(X ≤ 130) + 0.1*Weibull(5.40, 100.06)*I(X > 130), and the right graph for treatment group (X = 1), 0.6*Weibull(5.07, 93.52)*I(X ≤ 130) + 0.4*Weibull(5.40, 100.06)*I(X > 130), where P(Y > τ|X = 1) ≠ P(Y > τ|X = 0), μ (Y |Y > τ ∩ X = 1) = μ (Y |Y > τ ∩ X = 0), and μ (•) denotes the population mean of (•). τ is 90th percentile of the all observations in treatment and control groups.
<img src="https://cloud.githubusercontent.com/assets/5496192/25804879/6a6b2b8a-33cb-11e7-8f7d-2bc5d94aec37.png" />
(Gao et al., 2008) propose a single-step union-intersection test [8] of the following compound null hypothesis:
H0,C : [P(Y > τ|X = 1) = P(Y > τ|X = 0)] ∩ [μ(Y|Y > τ ∩ X = 1) = μ (Y|Y > τ ∩ X = 0)].
The test of H0,C can be constructed with the following simple procedure. Define a new variable Z such that Zi ≡ I(Yi >
τ)Yi, where I(•) denotes the indicator function taking on values of one if (•) is true and zero otherwise. One can then simply conduct an appropriate test (several candidates will be considered below) of whether the population mean of Z is significantly different between the treatment and control groups (X = 0 or 1). This approach has the desired property that when H0,C is true, it will only be rejected 100*α% of the time at the nominal α level even if f(Y|Y ≤ τ ∩ X = 1) ≠ f(Y|Y ≤ τ ∩ X = 0), where f(•) denotes the probability density function of (•) (i.e., the bulk part of the distribution is different)
Note that the expectation (or population mean) of Z, μ(Z) = P(Y > τ) μ(Y | Y > τ). Therefore the new test for H0,C is really testing whether P(Y > τ | X = 1) μ (Y | Y > τ ∩ X = 1) = P(Y > τ | X = 0) μ (Y | Y > τ ∩ X = 0), while the method for H0,B is testing whether μ (Y | Y > τ ∩ X = 1) = μ(Y | Y > τ ∩ X = 0) and the method for H0,A is testing whether P(Y > τ | X = 1) = P(Y > τ | X = 0). The mean difference of μ(Z) between two groups consists of two components: the difference between probabilities P(Y > τ | X = 1) and P(Y > τ | X = 0) and the difference between expectations μ (Y | Y > τ ∩ X = 1) and μ (Y | Y > τ ∩ X = 0). The test for H0,A focuses on the first component and the test for H0,B focuses on the second one, while the test for H0,C is related to both components.


<h2>Reference</h2>
<ul>
<li>Wang C, Li Q, Redden DT, Weindruch R, Allison DB: Statistical methods for testing effects on "maximum lifespan". Mech Ageing Dev 2004, 125(9):629-32. Erratum in: Mech Ageing Dev 2006, 127(7):652</li>
<li>Gao, Guimin, et al. "Testing for differences in distribution tails to test for differences in'maximum'lifespan." BMC medical research methodology 8.1 (2008): 49.</li>
</ul>
