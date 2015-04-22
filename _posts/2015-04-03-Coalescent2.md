---
layout: post
title: "Coalescent Theory (2) -- population events"
date: 2015-04-03
---

The number of genes required in the Wright-Fisher model for it to behave like a real population (under aforementioned assumptions) is called the <b>effective population size</b> (Ne) of that population.

<h2>Variable population size</h2>
Now we will relax the WF (Wright-Fisher) model assumption that population size is constant. Real populations vary in size over time. From a modeling perspective, the changes can be deterministic (i.e., we know exactly what they are) or stochastic (there are additional random deviations caused by environmental or other factors). Here we assume the changes are deterministic without random deviations. Given a constant population size (N), the probability that two genes find a common ancestor (also the probability that lineages coalesce by 1) remains as p = 1/2N (see above). Given a changeable N, N(t), this probability is also changing, p(t) = 1/2N(t). That means lineages coalesce more quickly (as we go back in time) when the population is smaller. One way to take the <b>variable coalescent rate</b> into account is to generate genealogies following the constant size coalescent process and then to stretch or compress local time p(t) by comparing it with p(0). For example, if p(t) is smaller than p(0) by a factor of two, then local time N(t) should be stretched by a factor of two to accommodate this change.

COSI (see my next post) can handle exponential population growth and user specified population bottlenecks. Exponential population growth (in the coalescent model as exponential decline in population size back in time) is the simplest and most natural pattern of population growth. It assumes N(t) = N*exp(-βt), where β = 2Nb is the scaled growth rate. 


Popultion bottlenecks 

<b>Parameters</b>
N(t) | : the population size at time t. Under the deterministic assumption, N(t) is a function of t only. N(0) = N. Since t is continuous time, we also allow N(t) to not be an integer.

<h2>Population structure</h2>
Migration/isolation models (structured coalescent)

When multiple sub-populations are simulated, the program allows for migration among sub-populations.

<h2>Selective Sweep</h2>
<b>Selective sweep</b> is used loosely to mean the fixation of a positively selected allele or the attendant reduction in variation. Th

<h2>A general framework</h2>
p(G|parameter) =
II
All time intervals uj
f(waiting time) Prob (event happens)

