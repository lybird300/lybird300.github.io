---
layout: post
title: "Coalescent models: Basics and Extensions"
date: 2015-04-02
---

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
At the heart of population genetics is the use of gene genealogy, a tree structure describes the evolutionary history (e.g., divergence) of a particular set of DNA sequences and their relatedness. Since the shape of the genealogy depends on population history, selection, etc, geneticsts have been using it to infer or predict DNA sequence variation. Due to the uncertainty elements of reproduction, the central part of genealogical data analysis is a stochastic characterisation of the genealogies that relate the sequences. Evaluating the probability of a given data set then consists of two steps: First, model reproduction in the population which leads to a probabilistic description of the genealogical relationship of the sampled data. Second, each genealogy will generate the data with a specific probability when combined with a model of the mutation process.

A geneology is completely summarized by the entire topology (who relates to whom) and the length of each branch (intervals between two subsequent convergence/divergence events). The coalescent approach generates the genealogy backwards, instead of forwards, for a sample of sequences (rather than the entire population. This approach introduces computational and analytical convenience because the history of the entire population includes sequences that are extinct or we have not sampled.

The reproduction in the population giving rise to the genealogical relationship among genes is often described following the Wright-Fisher model, which assumes:
1. Nonoverlapping generations
2. Random reproduction (Poisson distributed number of offspring)
3. Constant population size of N individuals
4. Random mating (no structure)


<h2>Extensions</h2>

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

<pre class="prettyprint pre-scrollable"><code>hihi</code></pre>



<h2>References</h2>
<ul>
<li>Jotun H, Schierup MH, Wiuf C: Gene Genealogies, Variation and Evolution A Primer in Coalescent Theory. Oxford University Press, Oxford; 2005. OpenURL</li>
<li>Hudson, R. R. (1990). Gene genealogies and the coalescent process. Oxford surveys in evolutionary biology, 7(1), 44.</li>
<li>Nordborg, M. (2001). <a href="http://www.cs.berkeley.edu/~jordan/sail/readings/nordborg.pdf">Coalescent theory</a>. In Handbook of statistical genetics. (A computer scientist's review on population genetics models based on the coalescent theory)</li>

</ul>

