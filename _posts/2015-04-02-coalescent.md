---
layout: post
title: "Overview of the Coalescent Theory"
date: 2015-04-02
---
Population genetics is the study of the forces that create and maintain genetic variation. At the heart of it is the use of gene genealogy, a tree structure describes the evolutionary history (e.g., divergence) of a particular set of DNA sequences and their relatedness. Since the shape of the genealogy depends on population history, selection, etc, geneticsts have been using it to infer or predict DNA sequence variation. Due to the uncertainty elements of reproduction, the central part of genealogical data analysis is a stochastic characterisation of the genealogies that relate the sequences.

A geneology is completely summarized by the entire topology (who relates to whom) and the length of each branch (intervals between two subsequent convergence/divergence events). The coalescent approach generates the genealogy backwards, instead of forwards, for a sample of sequences (rather than the entire population). It traces the ancestral lineages, which are the series of genetic ancestors of the samples at a locus, back through time. The history of a sample of size <b>n</b> comprises n − 1 coalescent events. Each coalescent event decreases the number of ancestral lineages by one. This takes the sample from the present day when there are n lineages through a series of steps in which the number of lineages decreases from n to n − 1, then from n − 1 to n − 2, etc., then finally from two to one. At each coalescent event, two of the lineages fuse into one common-ancestral lineage. The result is a bifurcating tree. This approach introduces computational and analytical convenience because the history of the entire population includes sequences that are extinct or we have not sampled. 

This post will briefly introduce the coalescent theory, including its history, the basic model, and important extensions. For details such as theorems and their proofs, please refer to the resources provided at the end of this page. 

<h2>History</h2>
Gustave Malécot (in the 1940’s) introduced the idea of following a pair of gene copies back to their common ancestor and the notion of <b>identity by descent (IDB)</b>: If we pick two genes from a <b>Wright-Fisher (WF)</b> population, how long ago on average did the two genes share their <b>most recent common ancestor (MRCA)</b>? Genealogical approaches to samples larger than two appeared in response to the first direct measurements of molecular variation (Harris 1966; Lewontin and Hubby 1966). Since then there have been some seminal work:
<ul>
<li>Ewens (1972): Derived the distribution of allele counts in a sample under the infinite-alleles model of selectively neutral mutation</li>
<li>Watterson (1975): gave an explicitly genealogical derivation of the <b>number of segregating sites</b>, or polymorphic sites, in a sample of sequences under <b>the infinite-sites model of mutation without recombination</b>.</li>
<li>Kingman (1982a; 1982b; 1982c): proved the existence of the coalescent process. Showed that the ‘n-coalescent’ holds for a wide range of populations with different breeding structures.</li>
<li>Tajima (1983): Derived the expectation of the average number of pairwise differences in a sample. As it turns out, this is an estimate of the composite parameter: θ = 4Neμ</li>
<li>Hudson (1990) wrote a wonderful review of coalescent theory and made available an <b>algorithm</b> to simulate data under different population models.</li>
</ul>

<h2>Basics</h2>
A widely used model that describes reproduction in a population, which gives rise to the genealogies of that population, is the Wright-Fisher model. This model makes the following assumptions on population evolution:
<ul>
<li>Discrete and nonoverlapping generations: all of the individuals in the population die each generation and are replaced by offspring.</li>
<li>Constant population size through time: the population size <b>N</b> (different from sample size) is assumed to be constant over time and finite. When the individuals that constitute the population are haploid organisms (they have only one copy of genetic materials), the population will consist of N copies of the <b>genome</b> (the genome of an organism is its whole hereditary information and is encoded in the DNA or RNA). In case of diploid organisms (e.g., humans, with two copies of genetic materials), there will be 2N copies.</li>
<li>Random mating (no structure): next generation is drawn randomly from a large gamete pool. Statistically, the next generation is formed from the current generation by uniformly sampling with replacement</li>
<li>Neutral mutation: the mutation changes the DNA sequence but not an individual’s ability to survive and to produce offspring</li>
</ul>
The basic/standard Wright-Fisher model results in a decay of genetic variation. Since the population is finite in size and reproduction is a random process, some individuals may not contribute any offspring to the next generation. This random loss of genetic lineages forward in time is called <b>genetic drift</b>, which reduces the diversity of the population diversity. One measure of population diversity is <b>heterozygosity</b>, defined as the probability that two genes chosen at random from the population have different alleles. Alleles are different versions of the genetic information encoded at a location in the genome of an organism (aka, genetic locus). A common example of genetic locus is the sequence of nucleotides that makes up a gene. Thus, two sequences of the same gene are different alleles if they are not identical.Assuming a gene has two allelic states (denoted A and a), genetic drift eventually leads to either A or a being lost from the population. When this happens, the surviving allele is said to be fixed in the population. The effect of genetic drift is compensated by mutation, a process by which the allelic state of a gene occasionally changes from one to another (e.g., from A to a).

<div id="term">
<ul>
<li>SNP: single nucleotide polymorphism</li>
<li>Haplotype: a set of DNA variations, or polymorphisms, that tend to be inherited together. A haplotype can refer to a combination of alleles or to a set of SNPs found on the same chromosome</li>
</ul>
</div>

The number of genes required in the Wright-Fisher model for it to behave like a real population (under aforementioned assumptions) is called the <b>effective population size</b> (Ne) of that population.

Parameters (as denoted in the literature/as the input of <a href="http://lybird300.github.io/2015/04/04/CoJava.html">Cosi or CoJava</a> if applicable)
<ul>
<li>n/sample_size: the number of DNA sequences (or genes/chromosomes/haplotypes depending on the context) being sampled</li>
<li>N/pop_size: the number of individuals in the population; thus, diploid populations each has 2N sequences</li>
<li>k/numnodes: the number of sequences at one generation in the genealogy, or the number of corresponding nodes in the subgraph of an <a href="http://www.math.canterbury.ac.nz/~r.sainudiin/recomb/ima.pdf">ARG (Ancestral Recombination Graph)</a></li>
<li>T: continuous time measured in units of 2N generations (2N generations are the average time for two genes to find a common ancestor). Modeling time in this way makes the coalescent independent of population sizen N</li>
<li>λ/rate: When using an exponential distribution to approximate time to next coalescent event, λ is the decay rate of the exponential distribution</li>
</ul>
<pre class="prettyprint pre-scrollable"><code>
<b>Algorithm 1</b>: the basic coalescent (a stochastic process that generates genealogies for n DNA sequences)
<ol>
<li>Start with k = n sequences</li>
<li>Simulate the waiting time to next coalescent event, <font color="red">T(k) ~ Exp(-k(k-1)/2)(?)</font></li>
<li>Choose a random pair (i, j) with 1 ≤ i < j ≤ k uniformly among k(k-1)/2 possible pairs</li>
<li>Merge i and j into one gene and decrease the sample size by one, k--</li>
<li>If k > 1 go to Step 2, otherwise stop</li>
</ol>
</code></pre>
Note: Step 2 utilizes the property that when n is much smaller than N, the probability of a coalescence event in a given generation with k sequences (i.e., for k genes to have k-1 ancesters in the previous generation) is approximately k(k-1)/(4N). Thus, the amount of waiting time (measured in 2N-generation units) during which there are k lineages, T(k), has approximately an exponential distribution with mean 2/(k(k-1)). The decay rate λ = k(k-1)/2
Related functions in CoJava: /geneticEvents/coalesce.java/coalesceGetRate(), 

<h2>Adding mutations</h2>
Strictly neutral mutations (i.e., mutations that have not and will not affect fitness) should not affect the geneologies of random samples, because they, by definition, have no effect on the number of offsprings or the tendency to migrate of individuals bearing these mutations. This property has two consequences.The first consequence is an efficient computer algorithm. Since the neutral mutation process can be separated from the genealogical process, the coalescent process may be modeled by first generating the random genealogy of the individuals backward in time, and then superimposing mutations forward in time. Secondly, the statistical properties of geneologies are independent of the specific mutation model (e.g., infinite-allele, infinite-site, or finite-site model). The discussion below will base on the infinite sites model, which assumes that mutations always occur at distinct sites (i.e., no recurrent mutations). Thus, all mutations are distinguishable.
 represents  Mutations are randomly placed on branches proportional to their length. We can compute that total length of the genealogy, T, as by :
Conditional on the genealogical tree, mutations are placed on the branches. The number of mutations on each branch follows a Poisson distribution with mean equal to the product of the mutation rate and the branch length.
allow the number of mutations to be fixed so that the probability that a mutation occurs on a particular branch is proportional to its length.
Parameters
<ul>
<li>Ttot/: total evolutionary time available, represented by the total branch lengths of a genealogy. We can compute it by summing over the product of each coalescent interval, T(i), and the number of lineages sharing that interval, i. In other words, E[Ttot] = <img src=""/></li>
<li>S: the number of segregating sites, i.e., the number of DNA sequence positions where some pair of sequences (in the sample) differ; we can think of it as the total number of mutations along the entire geneology</li>
<li>μ/: mutation rate per sequence per generation (sometimes mutation rate is provided as per base pair (bp), which is a common measure of sequence length)</li>
<li></li>
</ul>
 
Under the infinite-sites model, the expected number of segregating sites, E[S], is:
TNSEμ=][
Infinite sites Model
Every mutation occurs at a different site

<h2>Extensions</h2>

Considering recombination
When diploid individuals reproduce, there are two parents, each of which contributes one of each of its pairs of
chromosomes. One parent’s contribution is a combination of its two <a href="http://www.phschool.com/science/biology_place/labbench/lab3/homologs.html">homologous chromosomes</a> when they undergo recombination.
<h3></h3>


<h2>Assumptions/Theoretical foundations</h2>

Bifurcate tree approximation
Mutations are simulated assuming an infinite-sites model.

Mutation: sequence, When multiple sub-populations are simulated, the program allows for migration among subpopulations.
allow for user specified demographic events such as population bottlenecks 
Recombination: V, allow recombination rates to vary by defining a genetic map or <a href="http://en.wikipedia.org/wiki/Recombination_hotspot">hotspots</a> along the genome
It cannot generate chromosomal length regions

Migration: User-defined matrix
Mating system: Random Mating
<a href="http://en.wikipedia.org/wiki/Fecundity">Fecundity</a>: Random Distribution
Life cycle: Not applicable
Population growth: Exponential growth
Events allowed: change in population size, colonization or extinction (change in number of populations), migration matrix
Selection: Single biallelic site

cannot distinguish...since they have the same total scaled mutation rate. For example, (1) simulate 1,000 sequences from a population of 20,000. Each sequence consists of 2,000 independent loci each of lkb long. Mutation rate is 10-8 per base pair per generation, (2) simulate 1,000 sequences from a population of 1,000. Each sequence consists of 40,000 independent loci each of lkb long with the same mutation rate in (1).







<h2>References</h2>
<ul>
<li>Jotun H, Schierup MH, Wiuf C: Gene Genealogies, Variation and Evolution A Primer in Coalescent Theory. Oxford University Press, Oxford; 2005. OpenURL</li>
<li>Hudson, R. R. (1990). Gene genealogies and the coalescent process. Oxford surveys in evolutionary biology, 7(1), 44.</li>
<li>Nordborg, M. (2001). <a href="http://www.cs.berkeley.edu/~jordan/sail/readings/nordborg.pdf">Coalescent theory</a>. In Handbook of statistical genetics. (A computer scientist's review on population genetics models based on the coalescent theory)</li>
<li>Durrett, Richard. Probability models for DNA sequence evolution. Springer Science & Business Media, 2008</li>
<li><a href="http://genome.sph.umich.edu/wiki/Biostatistics_666:_Main_Page">Biostatistics 666</a> Lecture notes, University of Michigan</li>
<li><a href="http://bio.classes.ucsc.edu/bio107/">Bio 107</a> Lecture notes, University of California at Santa Cruz</li>
</ul>

