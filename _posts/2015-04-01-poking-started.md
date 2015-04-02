---
layout: post
title: "The Very First Day"
date: 2015-04-01
---

Today is the April Fool's Day in 2015 and the first day that I start poking into computer simulations for population and evolutionary genetics. The person bold enough to hire me for this job, <a href="http://www.med.unc.edu/genetics/faculty/wilhelmsen">Dr. Kirk Wilhelmsen</a>, stopped by after lunch. "Whatever you don't know, I will teach you," he said when he left. "A world-known geneticist gives you a 1-hour exclusive class every Friday," I said to myself, "You'd better not screw up, Yuan."

The two main reasons for wanting to simulate genetic data are, first, to gain insight into the effects that underlying demographic and mutational parameters may have on the genetic data one sees, and, secondly, to create test datasets for assessing the power of alternative genetic analysis methods. There is <a href="http://www.nature.com/nrg/journal/v13/n2/full/nrg3130.html">a recent review on this area</a> from a geneticist point of view. The article also points out several other review articles, which, according to the author, focus more on technical aspects.

The key challenges that all simulation algorithms face are: (1) speed — typically one wants to do lots of simulations, so they need to be fast; (2) scalability — with the advent of genome-wide genotyping and large-scale sequencing, there is a need for simulation programs to match; and (3) flexibility — can the program cope with different demographic histories, population structure, recombination, selection, mutation models and disease models?

There are three main approaches to dealing with these challenges, here termed ‘backwards’, ‘forwards’ and ‘sideways’. The ‘backwards’ (or coalescent) approach (Kingman 1982, Hudson 1983 & 1990, Donnelly and Tavare 1995) is an efficient way to sample sequences from a theoretical population that follows the Wright-Fisher neutral model (Ewens 1979). It starts with the sample of individuals that will form your simulated dataset, then work backwards in time to construct the ancestral tree or graph of genealogical relationships that connects them all. Neutral mutations can subsequently be placed on this structure to create the simulated dataset. ‘Forwards’ simulations start with the entire population of individuals — typically, many thousands — and then follow how all the genetic data in question are passed on from one generation to the next. One usually needs to simulate over many thousands of generations in order to arrive at an equilibrium in which the genetic characteristics of the population are independent of the original starting conditions. By restricting attention just to the genealogical structure relevant to the sample in question, the coalescent approach is more efficient than the ‘forwards’ approach. Also, this approach usually employs a continuous-time approximation to effectively skip over the intermediate generations between important tree generating events, which makes it even more efficient.

Major statistics for evaluating the synthetic data and calibrationg the simulator
Linkage Disequilibrium (LD) patterns
Allele Frequency spectrum

As for future research interests, the simulation of <b>copy number variation</b> and/or microsatellite data at larger genomic scales, and of more complex disease models allowing covariates and linked loci, remain areas that deserve further exploration. (Note:  the performance of any methods trying to tackle the challenges of complex disease mapping should be evaluated by large scale simulation studies)

References
Liu, Youfang, Georgios Athanasiadis, and Michael E. Weale. "A survey of genetic simulation software for population and epidemiological studies." Hum Genomics 3.1 (2008): 79-86.
Hoban, Sean, Giorgio Bertorelle, and Oscar E. Gaggiotti. "Computer simulations: tools for population and evolutionary genetics." Nature Reviews Genetics 13.2 (2012): 110-122.
