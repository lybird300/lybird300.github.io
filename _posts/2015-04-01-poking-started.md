---
layout: post
title: "The Very First Day"
date: 2015-04-01
---

Today is the April Fool's Day in 2015 (lol) and the first day that I start poking into computer simulations for population and evolutionary genetics. The person who's bold enough to hire me for this work, <a href="http://www.med.unc.edu/genetics/faculty/wilhelmsen">Dr. Kirk Wilhelmsen</a>, stopped by after lunch and talked with me for a while. "Whatever you don't know, I will teach you," he said. "Wow...a world-known geneticist gives you a 1-hour exclusive class every Friday," I said to myself, "you'd better not screw up this opportunity."

<a href="http://www.nature.com/nrg/journal/v13/n2/full/nrg3130.html">A recent review on this area</a> from a geneticist point of view. This article also points out several other review articles, which, according to the author, focus more on technical aspects.

The two main reasons for wanting to simulate genetic data are, first, to gain insight into the effects that underlying demographic and mutational parameters may have on the genetic data one sees, and, secondly, to create test datasets for assessing the power of alternative genetic analysis methods.

The key challenges that all simulation algorithms face are: (1) speed — typically one wants to do lots of simulations, so they need to be fast; (2) scalability — with the advent of genome-wide genotyping and large-scale sequencing, there is a need for simulation programs to match; and (3) flexibility — can the program cope with different demographic histories, population structure, recombination, selection, mutation models and disease models?

As for future research interests, the simulation of <b>copy number variation</b> and/or microsatellite data at larger genomic scales, and of more complex disease models allowing covariates and linked loci, remain areas that deserve further exploration. (Note:  the performance of any methods trying to tackle the challenges of complex disease mapping should be evaluated by large scale simulation studies)

References
Liu, Youfang, Georgios Athanasiadis, and Michael E. Weale. "A survey of genetic simulation software for population and epidemiological studies." Hum Genomics 3.1 (2008): 79-86.
Hoban, Sean, Giorgio Bertorelle, and Oscar E. Gaggiotti. "Computer simulations: tools for population and evolutionary genetics." Nature Reviews Genetics 13.2 (2012): 110-122.
