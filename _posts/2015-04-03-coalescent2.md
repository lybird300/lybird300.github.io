---
layout: post
title: "Coalescent Theory (2) -- population events"
date: 2015-04-03
---
<h2>Variable population size</h2>
Now we will relax the WF (Wright-Fisher) model assumption that population size is constant. Real populations vary in size over time. From a modeling perspective, the changes can be deterministic (i.e., we know exactly what they are) or stochastic (there are additional random deviations caused by environmental or other factors). Here we assume the changes are deterministic without random deviations. Given a constant population size (N), the probability that two genes find a common ancestor (also the probability that lineages coalesce by 1) remains as p = 1/2N (see above). Given a changeable N, N(t), this probability is also changing, p(t) = 1/2N(t). That means lineages coalesce more quickly (as we go back in time) when the population is smaller. One way to take the <b>variable coalescent rate</b> into account is to generate genealogies following the constant size coalescent process and then to stretch or compress local time p(t) by comparing it with p(0). For example, if p(t) is smaller than p(0) by a factor of two, then local time N(t) should be stretched by a factor of two to accommodate this change.

COSI (see my next post) can handle exponential population growth and user specified population bottlenecks. Exponential population growth (in the coalescent model as exponential decline in population size back in time) is the simplest and most natural pattern of population growth. It assumes N(t) = N*exp(-βt), where β = 2Nb is the scaled growth rate. 

<b>Parameters</b>
N(t) | : the population size at time t. Under the deterministic assumption, N(t) is a function of t only. N(0) = N. Since t is continuous time, we also allow N(t) to not be an integer.

A population bottleneck is a sudden reduction in effective population size. It is normally associated with external catastrophes such as an ice age pr severe disease, but can also be associated with the colonization of a new habitat. In studying bottlenecks, we often assume the population sizes are piecewise constant. As shown in the figure below, the current population size is N1, at some point in the past it is reduced to fN1 for an amount of time tB, measured in units of 2N1 generations, and then returns to an ancestral population size of N2.
<img alt="bottleNeck" src="https://cloud.githubusercontent.com/assets/5496192/7304150/541c6cbc-e9c3-11e4-82e2-461ff43bdb53.PNG" />
Cosi and CoJava apply a slightly more complicated model that violates the WF model's random mating assumption by considering inbreeding (i.e., mating with relatives) during tB.
<b>Parameters</b>
Ne | effective population size: the number of genes required in the Wright-Fisher model for it to behave like a real population.
f | inbreeding coefficient: the probability that two alleles at a randomly chosen locus are identical by descent (IBD, IBD = copies of same alleles from common ancestor). The intuition is that inbreeding decreases the number of heterozygotes in the population (each generation of selfing decreases the number of heterozygotes by 1/2). 

<h2>Population structure</h2>
Migration/isolation models (structured coalescent)

When multiple sub-populations are simulated, the program allows for migration among sub-populations.

<h2>Selective Sweep</h2>
<b>Selective sweep</b> is used loosely to mean the fixation of a positively selected allele or the attendant reduction in variation. Th

<h2>Putting all things together</h2>
<b>Primary CoJava functions</b> 
Calculate the waiting time: /Random/poisson.java/poissonGetNext(double rate)
This function generates a random variable that is exponentially distributed (with the given rate), and The process is a poisson process. Take a look at <a href="http://www.stats.ox.ac.uk/~didelot/popgen/Chapter2.pdf">Chapter 2</a> of John Wakeley's "Coalescent theory: an introduction" too see how exponential and poisson distributions are related to the coalescent theory in different ways. 
	 *  

