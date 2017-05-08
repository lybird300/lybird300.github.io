---
layout: post
title: "Compare the tail of two empirical distributions"
date: 2015-05-08
---
<img src="https://cloud.githubusercontent.com/assets/5496192/25811920/b94568f8-33e2-11e7-8022-35c5663aa2f2.png" />
<h2>Motivation</h2>
We know when considering a suject's IBD haplotype sharing with two other subjects (k=2), using empirically fitted subject-specific distribution of maxSumWtSmor is better than fitting a general distribution of pair sharing (i.e., collect the ). But what about when k=3? Will S = 0, 1, 2 lead CHAT to have different performances in sensitivity and specificity? If so, how and to what extent? In this experiment, we try to answer these questions using non-carrier subjects. Our hypothesis is distributions are not subject specific when K = 3, i.e., they are the same across different subjects. If this hypothesis is valid, then we would no longer have to fit subject-specific distributions when k = 3; otherwise we probably have to live with k=2 because fitting subject-specific distributions would be very time consuming when k = 3 (s can be 1 or 2).									
									
									
									
									
									
									
									
									

<h2>The problem and relevant issues</h2>
By constructing a threshold, saying lambda, we can test equality of two means or variances of the two distributions restricted on the tail region (\lambda, infinity) based on two data sets of observations falling in this tail region. One relevant issue would be using which tests to compare the mean or variance (elaborate below). Another issue would be how to define the threshold and therefore the "tail region". I suspect both issues would vary with specific contexts. If we are not sure, perhaps we should simulate several reasonable thresholds and try different tests when assessing every possible solutions (introduced below)<br/>
<ul>
<li>The two sample t-test or F-test may be OK but not be poweful since random variable restricted on this tail region is not normal even the original ones are. <b>A tail may have practically any distribution. </b></li>
<li>The Chi Square test (Goodness-of-Fit test) may not be appropriate for identifying differences in tails. The Chi Square test is structured to compare two distributions by buckets of values (graphically represented by a histogram). And, the tails will consist in the far most buckets. If the tails are covered by many bins, then--because they are tails!--there may be few data in any of the bins, invalidating the chi-squared approximation (The chi-squared test is based on a Normal-theory approximation to the true distribution of the chi-squared statistic. Typically this approximation gets poor when bin populations drop below 5). If the tails are covered by few bins, then you lose almost all power to discriminate their shapes, and what you do manage to discriminate might not be relevant or useful.</li>
<li>Since <b>A tail may have practically any distribution</b> (yes, important things deserve repeating), we may have to use non-parametric tests such as the Wilcoxon-Mann-Whitney (exact) test or even a permutation test (with t-statistic) as described by Good [13] to test for differences in the values that exceed a certain threshold. The Mann-Whitney test is a non-parametric test that utilizes ranks in order to test the null hypothesis that two samples come from the same population against an alternative hypothesis that one population consistently has higher values. In order to utilize the Mann-Whitney test one must first sort observations from both samples <b>jointly</b> in an ascending order and assign a rank to each observation. </li>
</ul>

<h2>Possible solutions</h2>
<h3>Base on sample kurtosis</h3>
(This solution is not suitable for our case as the distribution to be compared with is supposed to be normal/Gaussian.)
According to <a href="">the wikipedia page of kurtosis</a>, kurtosis is a measure of the "tailedness" of the probability distribution of a real-valued random variable relative to a normal distribution. That is, data sets with high kurtosis tend to have heavy tails, or outliers. Data sets with low kurtosis tend to have light tails, or lack of outliers. The drawback of using kurtosis is that it allows to draw conclusions only jointly for both of the tails of the distribution and is best suited for symmetrical distribution.
If you have a single tailed empirical distribution, you may want to consider the following. Suppose the distribution has a right tail, you can discard all of the observations below the mean, transform all of the remaining observations symmetrically with respect to the mean and create a perfectly symmetrical data sample by combining the non-discarded observations and their symmetrical transformations. Then you can calculate the Kurtosis of this "new" distribution.
<h3>Work on a predefined tail area (or threshold)</h3>
You can use a threshold to select data points (observations) and then only compare the empirical distributions of these data points, or
Use a statistic that considers a threshold but allows in all data points (good for permutation tests). In either case, MW or permutation test can be used to compare different tail-region distributions.
<p>(Gao et al., 2008) design a statistic Z such that Zi ≡ I (Yi > τ)Yi for each observation and then applying Wilcoxon-Mann-Whitney test or better still a permutation test to Z, the new methods achieve far better performance when considered across a broad range of circumstances in terms of both Type-1 error rates and power. One could <b>extend this method to more than two groups</b>. For example, one could use the Kruskal-Wallis Test to replace the Wilcoxon-Mann-Whitney test, or use permutation testing for multiple groups to replace that for two groups. Important excerpt copied below</p>
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

<h3>Apply the extreme value theory</h3>
The second theorem in Extreme Value Theory can be applied to the values above a certain (high) threshold. It states that, for an unknown distribution function F(x) of random variables x, the distribution of the variable x above a threshold s can be defined as:
F_s(Y) = P(x-s≤y|x>s)=[F(y+s)-F(s)]/[1-F(s)]
Pickands-Balkema-de Haan theorem [Balkema, de Haan, 1974; Pickands, 1975] states that for a large class of underlying functions F and for s approaches to infinity the distribution F_s is well approximated by the Generalized Pareto Distribution (GPD). 


<h2>Reference</h2>
<ul>
<li>Wang C, Li Q, Redden DT, Weindruch R, Allison DB: Statistical methods for testing effects on "maximum lifespan". Mech Ageing Dev 2004, 125(9):629-32. Erratum in: Mech Ageing Dev 2006, 127(7):652</li>
<li>Gao, Guimin, et al. "Testing for differences in distribution tails to test for differences in'maximum'lifespan." BMC medical research methodology 8.1 (2008): 49.</li>
</ul>
