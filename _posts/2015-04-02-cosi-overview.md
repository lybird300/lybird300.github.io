---
layout: post
title: "COSI Overview"
date: 2015-04-02
---

The Cosi program (Schaffner et al., 2005) implements a coalescent model similar to the MS program (Hudson, 1990) but
allowing for complex demographic histories and variable recombination rates. Both MS and Cosi implement the standard coalescent approach that simulates genealogical events backward in time. Simulated events typically include the coalescence of two sequences into a single ancestral lineage, recombination within a sequence, or migration between populations. Since all these events are typically rare, coalescent simulators assume that they never occur simultaneously and assume many generations pass between consecutive events. Time between events is explicitly modeled and used to skip over generations with no genealogical events of interest. The algorithm proceeds until all sequences coalesce to their most recent common ancestor (MRCA) and the resulting genealogy is used to place mutation events along the various sequences. 

<h2>Assumptions/Theoretical foundations</h2>
Mutations are simulated assuming an infinite-sites model.Conditional on the genealogical tree, mutations are placed on the branches. The number of mutations on each branch follows a Poisson distribution with mean equal to the product of the mutation rate and the branch length. 

<h1>Capabilities/Limiations</h1>
The algorithm simulates the genealogy of a sample of sequences, conditional on parameters such as the population size, the recombination rate, and rates of migration between subpopulations.
When multiple sub-populations are simulated, the program allows for migration among subpopulations.
allow for user specified demographic events such as population bottlenecks 
allow recombination rates to vary so as to mimic the pattern of hotspots along the genome
It cannot generate chromosomal length regions
allow the number of mutations to be fixed so that the probability that a mutation occurs on a particular branch is proportional to its length.

Performance
Cosi is suitable for short genomic segments or gene sequences (<2-3Mb) but become very slow for larger regions (> 100Mb). As sequences get longer, many more coalescent, recombination and migration events occur and the time intervals between them diminish. For longer sequences and large sample sizes, little computational efficiency is gained by skipping over uninteresting generations and substantial computational effort is expended tracking recombination events and their positions, and allocating memory to track the many ancestral fragments of each sequence as they repeatedly recombine and coalesce with each other. As genome-wide studies become a reality, efficient tools for simulating large sequences are essential. Thus, it is necessary to improve cosi's performance in this aspect.

An improved algorithm -- <a href="http://csg.sph.umich.edu/liang/genome/">GENOME</a> (While in the standard coalescent algorithm, each coalescence event involves exactly two sequences that coalesce to a common ancestor, in GENOME, multiple sequences can coalesce to a common ancestor simultaneously. The interested readers are also refered to the doctoral dissertation of the author -- "Efficient methods for analysis of genome scale data")


The bestfit model

This model has been calibrated using genome-wide human population data for different populations. It uses the large-scale variation in recombination rates obtained from the deCODE genetic map along with fine-scale variation in recombination rates. The default parameters of this model are listed in Table 1 of the paper describing the method (Schaffner et al., 2005). The program generates data for four populations, each with its own demographic scenario.

