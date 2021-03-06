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

A population bottleneck is a sudden reduction in <b>effective population size</b> (the number of individuals in a population who contribute offspring to the next generation). It is normally associated with external catastrophes such as an ice age pr severe disease, but can also be associated with the colonization of a new habitat. In studying bottlenecks, we often assume the effective population sizes are piecewise constant. As shown in the following example, the current population size is N1. At some point in the past it is reduced to fN1 for an amount of time tB (measured in units of 2N1 generations) and then returns to an ancestral population size of N2.
<img alt="bottleNeck" src="https://cloud.githubusercontent.com/assets/5496192/7304150/541c6cbc-e9c3-11e4-82e2-461ff43bdb53.PNG" /><br/>
Cosi and CoJava apply a slightly more complicated model, which infers effective population size from inbreeding coefficient (as an input parameter), although logically the latter depends on the former. Inbreeding means mating with relatives and is a violation of the WF model's random mating assumption.
<b>Parameters</b>
Ne | effectiveN: effective population size
f | coeff: inbreeding coefficient, the probability that two alleles at a randomly chosen locus are identical by descent (IBD, IBD = copies of same alleles from common ancestor). The intuition is that inbreeding decreases the number of heterozygotes in the population (each generation of selfing decreases the number of heterozygotes by 1/2). 

<h2>Population structure</h2>
Migration/isolation models (structured coalescent)

When multiple sub-populations are simulated, the program allows for migration among sub-populations.

<h2>Selective Sweep</h2>
The standard coalescent model assumes neutral mutations, but recent empirical findings have motivated the incorporation of so-called "selective sweeps".
the effects of positive selective sweeps on genetic variation Positive natural selection for adaptive traits has been the primary source of
excitement among workers studying the genomic effects of selection. In addition to
estimating the overall prevalence of positive selection, effort has focused on identifying
recently selected loci. This is possible because the fixation of an advantageous allele at
one locus affects loci nearby, in a phenomenon is known as genetic ‘hitch-hiking’ The primary signal of this is a
reduction in variation around the site of selection, but a number of subtler effects occur as
well (Nielsen, 2005). The term ‘selective sweep’ is used loosely to mean the fixation of a
positively selected allele or the attendant reduction in variation.

The fitness of an individual is the number of offspring it leaves. The
fitness of a gene is the number of copies it leaves. The fitness of an allele is the average fitness of genes of that allelic type.



 the case of alleles that have a fitness advantage or disadvantage, as
parameterized by a selection coefficient s.

A new mutation that has a positive effect will replace some of the variability
present in the population. All linked sites will suffer a drop in effective
population size.
A new mutation that has a negative effect and will be most likely removed ,
also resulting in a reduction of variability (and population size) 


<b>Selective sweep</b> is used loosely to mean the fixation of a positively selected allele or the attendant reduction in variation. In other words, a favorable mutation appears, rises in frequency, and eventually takes over the population. As the prevalence of the new mutation increases, neutral and nearly neutral genetic variation linked to it will also become more prevalent. This phenomenon is called <b>genetic hitchhiking</b> because alleles present on the chromosome on which the original mutation occurred will tend to increase in frequency and fix as well. A strong selective sweep results in a chromosome region of the genome where the positively selected haplotype (the mutated allele and its neighbors) is eventually the only one that exists, thus reducing genetic variations in that region. Selective sweeps are expected to affect
a bigger region given lower recombination rate.

Consider a population that is currently not polymorphic, but in which a
selective sweep recently took place. During the sweep, there were two allelic
classes which changed in size over time. In particular, the class corresponding
to the allele that is currently fixed in the population will shrink rapidly back in
time. The genealogy of the selected locus itself (in the “point” sense used above) will therefore behave as if it were part of a population that has expanded from a very small size. Indeed, unlike “real” populations, the allelic
class will have grown from a size of one. A linked point must have grown in
the same way, unless recombination took place between the
point and the selected locus. Whether this happens or not will depend on how
quickly the new allele increased. Typically, it depends on the ratio r/s, where s
is the selective advantage of the new allele, and r is the relevant recombination
probability.

Close to the selected site, coalescence times will have a tendency to be short. Shorter coalescence times mean less time for mutations to occur, so a local
reduction in variability is expected.

The basis of the Wright-Fisher model is that during reproduction each individual produces an essentially infinite number of offspring and it is from these offspring that the new generation is sampled. The proportion of types in the effectively infinite pool of offspring in the neutral world is exactly the same as in the parental generation, but in the selective world, if there are i a alleles and (N −i) A alleles in the parental generation then the pool of potential offspring will have a proportion (1 + s)i/((1 + s)i + N − i) of a alleles. We say that a, A have relative fitness 1 + s : 1; s is called selection coefficient. If s > 0 : a is said to be beneficial; if s < 0 : a is said to be deteriorate.

In silico analysis of disease-association mapping strategies using the coalescent process and incorporating ascertainment and selection
The Kimura and Takahata (1983) procedure for
simulation of the sample path under neutral drift can be
modified to incorporate selection. The allele frequency
at the next generation, given the current frequency, is
normally distributed, with expectation and variance determined
by the diffusion model. The average change of
allele frequency per generation under selection (with selection
coefficient s 10) is M p sp(1  p), in accor- dp
dance with a diffusion approximation (with no dominance).
If population size (N) is constant, the variance
of the change in allele frequency ( 2 ) that results from jdp
random drift is p(1  p)/(2N). If we assume that the
population has grown exponentially with rate r to the
current population size (N0), then, at generation t in the
past, 2 rt . Given the age and initial j p p(1  p)/(2N e ) dp 0
frequency, , of the disease mutation, the  1/(2N erT ) 0
change of frequency is simulated by generating a uniform
random variable with mean M and variance j2 , con- dp dp
ditional on nonextinction. If fewer than four copies of
the disease allele are present in the population, the diffusion
approximation is no longer accurate, and the
number of alleles in the next generation is instead simulated
as a Poisson random variable (with parameter
per) by use of the branching-process approximation for
a rare allele (see Ewens 1979). The method can be extended
to other selection models by simply replacing the
average rate of change of allele frequency under a diffusion
model.

<b>Parameter</b>
<ul>
<li>s | : the a alleles and A alleles have relative fitnesses 1 + s : 1. </li>
</ul>

<b>Algorithm</b>

<h2>Putting things together -- what to include?</h2>
Given the goal of our research project, what kind of coalescent model should we construct to test CHAT?
<ol>
<li>Selection: empirical studies show that rare variants under negative selection pressure may be relatively more important than common variants under balancing selection in the genetic architecture of neurodegenerative disease such as Parkinson's disease,</li>
<li>LD maps are sensitive to demographic factors and especially to time since the last major bottleneck.</li>
</ol>

<b>Primary CoJava functions</b> 
Calculate the waiting time: /Random/poisson.java/poissonGetNext(double rate)
This function generates a random variable that is exponentially distributed (with the given rate), and the process is a poisson process. Take a look at <a href="http://www.stats.ox.ac.uk/~didelot/popgen/Chapter2.pdf">Chapter 2</a> of John Wakeley's "Coalescent theory: an introduction" too see how exponential and poisson distributions are related to the coalescent theory in different ways. 

<h2>References</h2>
<ul>
<li>Wakeley, John. "Natural selection and coalescent theory." Evolution since Darwin: the first 150 (2010): 119-149.</li>
</ul>
