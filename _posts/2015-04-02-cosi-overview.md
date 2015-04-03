---
layout: post
title: "COSI Overview"
date: 2015-04-02
---

The Cosi program (Schaffner et al., 2005) implements a coalescent model similar to the widely used <a href="http://home.uchicago.edu/rhudson1/source/mksamples.html">MS program</a> (Hudson, 1990) but allows for complex demographic histories and variable recombination rates. Both MS and Cosi implement the standard coalescent approach that simulates genealogical events backward in time. Simulated events typically include the coalescence of two DNA sequences into a single ancestral lineage, recombination within a sequence, or migration between populations. Since all these events are typically rare, coalescent simulators assume that they never occur simultaneously and assume many generations pass between consecutive events. Time between events is explicitly modeled and used to skip over generations with no genealogical events of interest. The algorithm proceeds until all sequences coalesce to their most recent common ancestor (MRCA) and the resulting genealogy is used to place mutation events along the various sequences.

Cosi has been calibrated (i.e., figure out the values of model parameters by fitting the model to some empirical data) using genome-wide human population data for different populations (deCODE genetic map). The resultant model is referred to as the "bestfit" model and the obtained parameter values (default) are listed in Table 1 in <a href="http://www.ncbi.nlm.nih.gov/pmc/articles/PMC1310645/">Schaffner et al's paper</a>.

The table below lists. Parameter names are as they are in the "param" file.


<h2>Assumptions/Theoretical foundations</h2>
The time to an event is continuous
Population size is much larger than sample size.
Bifurcate tree approximation
Mutations are simulated assuming an infinite-sites model.Conditional on the genealogical tree, mutations are placed on the branches. The number of mutations on each branch follows a Poisson distribution with mean equal to the product of the mutation rate and the branch length.


<h2>Capabilities/Limiations</h2>
The algorithm simulates the genealogy of a sample of sequences, conditional on parameters such as the population size, the recombination rate, and rates of migration between subpopulations. Below is a list of 
<table>
  <tr>
    <th>Parameter</th>
    <th>Description</th>
    <th>Value</th>
    <th>Reference</th>
  </tr>
  <tr>
    <td>length </td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>mutation_rate</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>gene_conversion_rate</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
   <tr>
    <td>pop_size</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
    <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
    <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
    <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
    <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>
Mutation: sequence, When multiple sub-populations are simulated, the program allows for migration among subpopulations.
allow for user specified demographic events such as population bottlenecks 
Recombination: V, allow recombination rates to vary by defining a genetic map or <a href="http://en.wikipedia.org/wiki/Recombination_hotspot">hotspots</a> along the genome
It cannot generate chromosomal length regions
allow the number of mutations to be fixed so that the probability that a mutation occurs on a particular branch is proportional to its length.
Migration: User-defined matrix
Mating system: Random Mating
<a href="http://en.wikipedia.org/wiki/Fecundity">Fecundity</a>: Random Distribution
Life cycle: Not applicable
Population growth: Exponential growth
Events allowed: change in population size, colonization or extinction (change in number of populations), migration matrix
Selection: Single biallelic site

cannot distinguish...since they have the same total scaled mutation rate. For example, (1) simulate 1,000 sequences from a population of 20,000. Each sequence consists of 2,000 independent loci each of lkb long. Mutation rate is 10-8 per base pair per generation, (2) simulate 1,000 sequences from a population of 1,000. Each sequence consists of 40,000 independent loci each of lkb long with the same mutation rate in (1).

The parameters of cosi are predefined and stored in a separate file named "params". Cosi(v1.2) supports parameters such as mutation rate, gene conversion rate and sequence length. It also allows users to define the sampled populations, their effective population size (in the present) and demographic history, as well as the number of sampled chromosomes.

<pre class="prettyprint pre-scrollable"><code>hihi</code></pre>

<h2>Performance</h2>
Cosi is suitable for short genomic segments or gene sequences (<2-3Mb) but become very slow for larger regions (> 100Mb). As sequences get longer, many more coalescent, recombination and migration events occur and the time intervals between them diminish. For longer sequences and large sample sizes, little computational efficiency is gained by skipping over uninteresting generations and substantial computational effort is expended tracking recombination events and their positions, and allocating memory to track the many ancestral fragments of each sequence as they repeatedly recombine and coalesce with each other. As genome-wide studies become a reality, efficient tools for simulating large sequences are essential. Thus, it is necessary to improve cosi's performance in this aspect.

An improved algorithm -- <a href="http://csg.sph.umich.edu/liang/genome/">GENOME</a> (While in the standard coalescent algorithm, each coalescence event involves exactly two sequences that coalesce to a common ancestor, in GENOME, multiple sequences can coalesce to a common ancestor simultaneously. The interested readers are also refered to the author's doctoral dissertation -- "Efficient methods for analysis of genome scale data")

