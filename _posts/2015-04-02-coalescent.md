---
layout: post
title: "Overview of the Coalescent Theory"
date: 2015-04-02
---
Population genetics is the study of the forces that create and maintain genetic variation. At the heart of it is the use of gene genealogy, a tree structure describes the evolutionary history (e.g., divergence) of a particular set of DNA sequences and their relatedness. Since the shape of the genealogy depends on population history, selection, etc, geneticsts have been using it to infer or predict DNA sequence variation. Due to the uncertainty elements of reproduction, the central part of genealogical data analysis is a stochastic characterisation of the genealogies that relate the sequences.

A geneology is completely summarized by the entire topology (who relates to whom) and the length of each branch (intervals between two subsequent convergence/divergence events). The coalescent approach generates the genealogy backwards, instead of forwards, for a sample of sequences (rather than the entire population). It traces the ancestral lineages, which are the series of genetic ancestors of the samples at a locus, back through time. The history of a sample of size <b>n</b> comprises n − 1 coalescent events. Each coalescent event decreases the number of ancestral lineages by one. This takes the sample from the present day when there are n lineages through a series of steps in which the number of lineages decreases from n to n − 1, then from n − 1 to n − 2, etc., then finally from two to one. At each coalescent event, two of the lineages fuse into one common-ancestral lineage. The result is a bifurcating tree. This approach introduces computational and analytical convenience because the history of the entire population includes sequences that are extinct or we have not sampled. 

This post will briefly introduce the coalescent theory, including its history, the basic model, and important extensions.

<h2>History</h2>
Gustave Malécot (in the 1940’s) introduced the idea of following a pair of gene copies back to their common ancestor and the notion of <b>identity by descent (IDB)</b>: If we pick two genes from a <b>Wright-Fisher (WF)</b> population, how long ago on average did the two genes share their <b>most recent common ancestor (MRCA)</b>? Genealogical approaches to samples larger than two appeared in response to the first direct measurements of molecular variation (Harris 1966; Lewontin and Hubby 1966). Since then there have been some seminal work:
<ul>
<li>Ewens (1972): Derived the distribution of allele counts in a sample under the infinite-alleles model of selectively neutral mutation</li>
<li>Watterson (1975): gave an explicitly genealogical derivation of the <b>number of segregating sites</b>, or polymorphic sites, in a sample of sequences under <b>the infinite-sites model of mutation without recombination</b>.</li>
<li>Kingman (1982a; 1982b; 1982c): proved the existence of the coalescent process. Showed that the ‘n-coalescent’ holds for a wide range of populations with different breeding structures.</li>
<li>Tajima (1983): Derived the expectation of the average number of pairwise differences in a sample. As it turns out, this is an estimate of the composite parameter: θ = 4Neμ</li>
<li>Hudson (1990) wrote a wonderful review of coalescent theory and made available an <b>algorithm</b> to simulate data under different population models. Other reviews include: Fu and Li (1999) and Nordborg (2001). Reviews are the place to start reading if you want to delve deeper into this subject.</li>
</ul>

<h2>Basics</h2>
A widely used model that describes reproduction in a population, which gives rise to the genealogies of that population, is the Wright-Fisher model. This model makes the following assumptions on population evolution:
<ul>
<li>Nonoverlapping generations: all of the individuals in the population die each generation and are replaced by offspring.</li>
<li>Constant population size through time: the population size <b>N</b> (note: population size is different from sample size) is assumed to be constant over time and finite. When the individuals that constitute the population are haploid organisms (they have only one copy of genetic materials), the population will consist of N copies of the <b>genome</b> (the genome of an organism is its whole hereditary information and is encoded in the DNA or RNA). In case of diploid organisms (e.g., humans, with two copies of genetic materials), there will be 2N copies.</li>
<li>Random mating (no structure): next generation is drawn randomly from a large gamete pool. Statistically, the next generation is formed from the current generation by uniformly sampling with replacement</li>
<li>Neutral mutation: </li>
</ul>
The pure Wright-Fisher model results in a decay of genetic variation. Since the population is finite in size and reproduction is a random process, some individuals may not contribute any offspring to the next generation. This random loss of genetic lineages forward in time is called <b>genetic drift</b>, which reduces the diversity of the population diversity. One measure of population diversity is <b>heterozygosity</b>, defined as the probability that two genes chosen at random from the population have different alleles. Assuming a gene has two allelic states (denoted A and a), genetic drift eventually leads to either A or a being lost from the population. When this happens, the surviving allele is said to be fixed in the population. The effect of genetic drift is compensated by mutation, a process by which the allelic state of a gene occasionally changes from one to another (e.g., from A to a).
<div class="title">Genetic locus</div>

The number of genes required in the Wright-Fisher model for it to behave like a real population (under aforementioned assumptions) is called the <b>effective population size</b> (Ne) of that population.

Algorithm
The algorithm consists of two steps: First, model reproduction in the population which leads to a probabilistic description of the genealogical relationship of the sampled data. Second, generate the data by combining the genealogy with a model of the mutation process (mutation is .

<pre class="prettyprint pre-scrollable"><code>hihi</code></pre>

<h2>Extensions</h2>

WF model with recombination
When diploid individuals reproduce, there are two parents, each of which contributes one of each of its pairs of
chromosomes. One parent’s contribution is a combination of its two <a href="http://www.phschool.com/science/biology_place/labbench/lab3/homologs.html">homologous chromosomes</a> when they undergo recombination.
<h3></h3>

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
    <td>pop_define</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
   <tr>
    <td>pop_size</td>
    <td>effective population size</td>
    <td></td>
    <td></td>
  </tr>
    <tr>
    <td>sample_size</td>
    <td>the number of sampled chromosomes</td>
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





<h2>References</h2>
<ul>
<li>Jotun H, Schierup MH, Wiuf C: Gene Genealogies, Variation and Evolution A Primer in Coalescent Theory. Oxford University Press, Oxford; 2005. OpenURL</li>
<li>Hudson, R. R. (1990). Gene genealogies and the coalescent process. Oxford surveys in evolutionary biology, 7(1), 44.</li>
<li>Nordborg, M. (2001). <a href="http://www.cs.berkeley.edu/~jordan/sail/readings/nordborg.pdf">Coalescent theory</a>. In Handbook of statistical genetics. (A computer scientist's review on population genetics models based on the coalescent theory)</li>
<li>Durrett, Richard. Probability models for DNA sequence evolution. Springer Science & Business Media, 2008</li>
</ul>

