---
layout: post
title: "Coalescent Theory"
date: 2015-04-02
---
A genealogy is completely summarized by the entire topology (who relates to whom) and the length of each branch (intervals between two subsequent convergence/divergence events). The coalescent approach generates the genealogy backwards, instead of forwards, for a sample of sequences (rather than the entire population). It traces the ancestral lineages, which are the series of genetic ancestors of the samples at a locus, back through time. The history of a sample of size <b>n</b> comprises n − 1 coalescent events. Each coalescent event decreases the number of ancestral lineages by one. This takes the sample from the present day when there are n lineages through a series of steps in which the number of lineages decreases from n to n − 1, then from n − 1 to n − 2, etc., then finally from two to one. At each coalescent event, two of the lineages fuse into one common-ancestral lineage. The result is a bifurcating tree. This approach introduces computational and analytical convenience because the history of the entire population includes sequences that are extinct or we have not sampled. 

This post will briefly introduce the coalescent theory, including its history, the basic model, and important extensions. For details such as theorems and their proofs, please refer to the resources provided at the end of this page.

<blockquote>All models are wrong. We make tentative assumptions about the real world which we know are false but which we believe may be useful.(Box 1976)</blockquote>

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
<li>Discrete and non-overlapping generations: all of the individuals in the population die each generation and are replaced by offspring.</li>
<li>Constant population size through time: the population size <b>N</b> (different from sample size) is assumed to be constant over time and finite. When the individuals that constitute the population are haploid organisms (they have only one copy of genetic materials), the population will consist of N copies of the <b>genome</b> (the genome of an organism is its whole hereditary information and is encoded in the DNA or RNA). In case of diploid organisms (e.g., humans, with two copies of genetic materials), there will be 2N copies.</li>
<li>Random mating (no structure): next generation is drawn randomly from a large gamete pool. Statistically, the next generation is formed from the current generation by uniformly sampling with replacement</li>
<li>Neutral mutation: the mutation changes the DNA sequence but not an individual’s ability to survive and to produce offspring</li>
<li>Population size N is large: most results concerning this model will be approximate (referred to as diffusion approximation), ignoring terms of O(1/N^2) relative to O(1/N). For example, one example following this assumption is that we do not consider the simultaneous coalescence of more than two DNA sequences (thus the generative genealogical tree is a bifurcate tree.</li>
<li>No recombination: true for single bases and perhaps short regions</li>
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
<li>n | sample_size: the number of DNA sequences (or genes/chromosomes/haplotypes depending on the context) being sampled</li>
<li>N | pop_size: the number of individuals in the population; thus, diploid populations each has 2N sequences</li>
<li>k | numnodes: the number of sequences at one generation in the genealogy, or the number of corresponding nodes in the subgraph of an <a href="http://www.math.canterbury.ac.nz/~r.sainudiin/recomb/ima.pdf">ARG (Ancestral Recombination Graph)</a></li>
<li>T | gen: continuous time measured in units of 2N generations (2N generations are the average time for two genes to find a common ancestor). Modeling time in this way makes the coalescent independent of population sizen N</li>
<li>λ | rate: When using an exponential distribution to approximate time to next coalescent event, λ is the defining param of the exponential distribution</li>
</ul>
<b>Algorithm 1</b>: the basic coalescent (a stochastic process that generates genealogies for n DNA sequences)
<ol>
<li>Start with k = n sequences</li>
<li>Simulate the waiting time to next coalescent event, <font color="red">T(k) ~ Exp(2/(k(k-1)))</font></li>
<li>Choose a random pair (i, j) with 1 ≤ i < j ≤ k uniformly among k(k-1)/2 possible pairs</li>
<li>Merge i and j into one gene and decrease the sample size by one, k--</li>
<li>If k > 1 go to Step 2, otherwise stop</li>
</ol>
<p>Note: Step 2 utilizes the property that when n is much smaller than N, the probability of a coalescence event in a given generation with k sequences (i.e., for k genes to have k-1 ancestors in the previous generation) is approximately k(k-1)/(4N). Thus, the amount of waiting time (measured in 2N-generation units) during which there are k lineages, T(k), has approximately an exponential distribution with mean 2/(k(k-1)). The decay rate of the exponential distribution λ = k(k-1)/2.</p>
<b>Primary CoJava functions</b> 
Update the ARG: /populationStructures/demography.java/coalesceByName()

<h2>Adding mutations</h2>
<img alt="mutation" src="https://cloud.githubusercontent.com/assets/5496192/7108656/3cdaa94e-e15b-11e4-81de-4807091b966b.PNG" />
<p>We consider strictly neutral mutations that will not affect an individual's fitness (the individual's ability to survive and to produce offspring). Such mutations should not affect the simulated genealogies, because they have no effect on the number of offspring or individuals' tendency to migrate. This property has two consequences.The first consequence is an efficient computer algorithm, in which the coalescent process is modeled by separating the neutral mutation process from the the genealogical process. We can first generate the random genealogy of the individuals backward in time, and then superimpose mutations forward in time. The second consequence is that we can choose from various mutation models (e.g., infinite-allele, infinite-site, or finite-site model) without influencing the statistical properties of resultant genealogies. Here we focus on the infinite sites model, which assumes that mutations always occur at distinct sites. Therefore, all descendants must carry the mutated value, and all others must carry the ancestral value. All mutations are distinguishable (i.e., no recurrent or reverse mutations). </p>
Parameters
<ul>
<li>Ttot | : total evolutionary time available, represented by the total branch lengths of a genealogy. We can compute it by summing over the product of each coalescent interval T(k) (see above) and the number of lineages sharing that interval k: <br/><img alt="ETtot" src="https://cloud.githubusercontent.com/assets/5496192/7070529/9819a4a0-dead-11e4-8dad-3fe8d0803b9d.png"/></li>
<li>μ | mutation_rate (input param)*length(input param): mutation rate per sequence per generation (sometimes mutation rate is provided as per base pair (bp), which is a common measure of sequence length)</li>
<li>S | : the number of segregating sites, i.e., the number of DNA sequence positions where some pair of sequences (in the sample) differ. We can think of it as the total number of mutations imposed on the entire genealogy. In the infinite-sites model, the expected number of segregating sites for a diploid sample is:<br/><img alt="ES" src="https://cloud.githubusercontent.com/assets/5496192/7075088/5e9e7faa-decd-11e4-8143-8c6a864beaee.png"/></li>
</ul>
<b>Algorithm 2</b>: adding mutations to the generated genealogy
<p>Conditional on the genealogical tree, mutations are randomly placed on the branches. The number of mutations on each branch follows a Poisson distribution with mean equal to the product of mutation rate per base pair, length of DNA sequence, and the length of genealogy tree branch (in the unit of 2N). (Note: the mean of <a href="http://en.wikipedia.org/wiki/Poisson_distribution">Poisson distribution</a> is equal to its defining param λ)</p>
<b>Primary CoJava classes or functions</b>
/coalSimulator/sim.java/simMutate()
create mutations and put them in a list: /geneticEvent/mutations.java

<h2>Considering recombination and gene conversion</h2>
When diploid individuals reproduce, there are two parents, each of which contributes one of each of its pairs of
chromosomes. One parent’s contribution is a combination of its two <a href="http://www.phschool.com/science/biology_place/labbench/lab3/homologs.html">homologous chromosomes</a> when they undergo recombination.

Each recombination event increases the number of lineages by one, and because lineages recombine independently, the total rate of recombination when there are k lineages is kρ/2. Each coalescence event decreases the number of lineages by one, and the total rate of coalescence when there are k lineages is k(k − 1)/2, as we have seen previously. Since lineages are “born” at a linear rate, and “die” at a quadratic rate, the number of lineages is guaranteed to stay finite and will even hit one, occasionally (there will then temporarily be a single ancestral chromosome again

Population bottleneck. If, as we work backwards in time, there is a sudden
decrease in the population size, then the coalescence rate will become large.
Situations that can cause this are the founding of a new population by a
small number of migrants or, what is essentially the same, improving a
crop species by choosing a few individuals with desirable properties.
• Selective sweep. This term refers to the appearance of a favorable mutation
that rises in frequency until it takes over the population. In the absence
of recombination, this is a severe population bottleneck because the entire
population will trace its ancestry to the individual with the favorable
mutation.
• Population growth. In the human population, which has experienced a
period of exponential growth, then the coalescence rate will be initially
small, and the genealogical tree will have tips that are longer than usual.
Population subdivision. Imagine two isolated populations that exchange
migrants infrequently. If we sample 10 individuals from each population,
then the two subsamples will coalesce as usual and then eventually the
their two ancestral lineages will coalesce.

1. Fluctuations in population size
2. Migration/isolation models (structured coalescent)
3. Recombination (recombination graph)
4. Selection (ancestral selection graph)
5. Metapopulations (extinction/recolonization)





Mutation: sequence, When multiple sub-populations are simulated, the program allows for migration among sub-populations.
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

