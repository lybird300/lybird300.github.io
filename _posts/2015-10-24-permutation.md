---
layout: post
title: "Permutation tests in genome-wide asscociation studies"
date: 2015-10-24
---
A contentious theme in conducting whole genome
linkage or association studies is the assessment of statistical
significance [Curtis, 1996; Lander and Kruglyak, 1995;
Storey and Tibshirani, 2003; Witte et al., 1996]. On the one
hand, the rate of false positives needs to be controlled due
to the inherently multiple testing nature of whole genome
linkage and association analyses. On the other hand, whole
genome linkage and association signals are expected to be
small and are therefore easily overlooked (false negatives).
A simple Bonferroni correction, for example, is too
conservative if multiple tests are not independent. As
map density increases, the number of tests goes to infinity
and a Bonferroni correction is not appropriate. In addition,
experimental factors that influence the amount of information
extracted from the data set such as pedigree structure,
the completeness and accuracy of genotype data, and
marker density are expected to have substantial effects,
both on the power of a study to detect true effects and on
the significance of any finding. Several alternatives have
been developed to determine the statistical significance of
whole genome linkage or association results. These methods
include locus counting methods [Wiltshire et al., 2002],
Monte Carlo procedures [Lin and Zou, 2004], false
discovery rates [Benjamini and Hochberg, 1995], bootstrap
methods [Efron, 1979], and simulation procedures
[Cheverud, 2001]. Although these methods are elegant
and computationally efficient, they are often dependent on
certain model assumptions and cannot incorporate experimental-specific
characteristics (such as the pattern of
missing data, genetic informativeness, multivariate traits,
and structure of the pedigree) all of which can influence the
distribution of the test statistics. Therefore, it is commonly
agreed that accurate estimates of genome-wide statistical
significance are best obtained empirically through permutation
tests that condition on the observed data [Churchill and Doerge, 1994; Douglas et al., 2000; Gordon et al., 2000;
Hirschhorn et al., 2001; Ott, 1999; Sawcer et al., 1997].

Permutation testing is often described as the gold-standard for determining statistical significance when performing multiple correlated tests for genetic association.  

The aim of a permutation test is to retain the
design of the experiment, including any heterogeneity of
variance, and to simulate y-values under the null hypothesis

In testing a null hypothesis we need a test statistic that will have different values under the null hypothesis and the alternatives we care about (eg a relative risk of diabetes). We then need to compute the sampling distribution of the test statistic when the null hypothesis is true. For some test statistics and some null hypotheses this can be done analytically. The p-value is the probability that the test statistic would be at least as extreme as we observed, if the null hypothesis is true. A permutation test gives a simple way to compute the sampling distribution for any test statistic, under the strong null hypothesis that a set of genetic variants has absolutely no effect on the outcome. It is useful when we do not know how to compute the distribution of a test statistic.

To estimate the sampling distribution of the test statistic we need many samples generated under the strong null hypothesis. If the null hypothesis is true, changing the exposure would have no effect on the outcome. By randomly shuffling the exposures we can make up as many data sets as we like. If the null hypothesis is true the shuffled data sets should look like the real data, otherwise they should look different from the real data. The ranking of the real test statistic among the shuffled test statistics gives a p-value

Permutation testing is computationally demanding for
large-scale genetic association studies and requires an
optimized software implementation.

Permutation provides a type-I error rate that asymptotically approaches the chosen significance level when the units being permuted are exchangeable under the null hypothesis. However, permutation is computationally intensive especially for high-throughput analyses or simulations.

One further option is to use extreme tail theory to explicitly calculate the probability of detecting a test statistic as large as or larger than the observed maximum test statistic.

Permutation testing, when performed appropriately, provides
an unbiased test of the null hypothesis and is widely
considered the gold standard with which other estimators
and tests can be compared. Its main disadvantages are the
time and computational resources required to obtain precise
P value estimates, so alternative tests that provide
similar results with less computational burden can be
quite attractive, particularly when a large number of tests
is involved or when data are frequently reanalyzed in light
of new samples or genotypes.
Whereas conventional distribution-based statistical tests
typically require minimal computational resources, permutation
tests are often employed when the asymptotic distribution
of the statistic is unknown or difficult to model.
However, for many of the tests commonly used in GWA
studies, the asymptotic joint distribution of the test statistics
is known, which makes analytical methods possible.
As we show above, the asymptotic distribution of test
statistics from association tests between correlated traits,
markers, and models is often multivariate normal with
known covariance matrix. However, the most significant
test statistic from a group of multivariate normal test statistics
has a distribution function that, although known,
cannot be computed analytically because of the lack of a
closed-form solution to the multivariate normal integral.



