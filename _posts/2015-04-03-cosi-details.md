---
layout: post
title: "COSI (More details)"
date: 2015-04-03
---
the parameter file for coalescent is "params".  It
sets things like the mutation rate, gene conversion rate and sequence
length.  (With this model, sequences up to ~1 Mb generally run in a
reasonable time, provided the extreme tail of the recombination rate
is truncated -- see program documentation.) It also defines the sampled
populations, their effective population size (in the present) and the
number of sampled chromosomes.  The rest of the file defines the
demographic history of the popualations -- in this case, an
Out-of-Africa model of an ancestral population that splits into
Africans and non-Africans, and then into Europeans and Asians; African
Americans are modeled as a recent admixture of the African and
European populations.
