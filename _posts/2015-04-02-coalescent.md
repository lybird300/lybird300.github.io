---
layout: post
title: "Coalescent Theory (1) -- genetic events"
date: 2015-04-02
---
<blockquote>"All models are wrong. We make tentative assumptions about the real world which we know are false but which we believe may be useful." -- Box</blockquote>

A genealogy is completely summarized by the entire topology (who relates to whom) and the length of each branch (intervals between two subsequent convergence/divergence events). The coalescent approach generates the genealogy backwards, instead of forwards, for a sample of sequences (rather than the entire population). It traces the ancestral lineages, which are the series of genetic ancestors of the samples at a locus, back through time. The history of a sample of size <b>n</b> comprises n − 1 coalescent events. Each coalescent event decreases the number of ancestral lineages by one. This takes the sample from the present day when there are n lineages through a series of steps in which the number of lineages decreases from n to n − 1, then from n − 1 to n − 2, etc., then finally from two to one. At each coalescent event, two of the lineages fuse into one common-ancestral lineage. The result is a bifurcating tree. This approach introduces computational and analytical convenience because the history of the entire population includes sequences that are extinct or we have not sampled. 

Gustave Malécot (in the 1940’s) introduced the idea of following a pair of gene copies back to their common ancestor and the notion of <b>identity by descent (IDB)</b>: If we pick two genes from a population whose evolution is described by a <b>Wright-Fisher</b> model (discussed below), how long ago on average did the two genes share their <b>most recent common ancestor (MRCA)</b>? Genealogical approaches to samples larger than two appeared in response to the first direct measurements of molecular variation (Harris 1966; Lewontin and Hubby 1966). Since then there have been some seminal work:
<ul>
<li>Ewens (1972): Derived the distribution of allele counts in a sample under the infinite-alleles model of selectively neutral mutation</li>
<li>Watterson (1975): gave an explicitly genealogical derivation of the <b>number of segregating sites</b>, or polymorphic sites, in a sample of sequences under <b>the infinite-sites model of mutation without recombination</b>.</li>
<li>Kingman (1982a; 1982b; 1982c): proved the existence of the coalescent process. Showed that the ‘n-coalescent’ holds for a wide range of populations with different breeding structures.</li>
<li>Tajima (1983): Derived the expectation of the average number of pairwise differences in a sample. As it turns out, this is an estimate of the composite parameter: θ = 4Neμ</li>
<li>Hudson (1990) wrote a wonderful review of coalescent theory and made available an <b>algorithm</b> to simulate data under different population models.</li>
</ul>

In the rest of this post, I will briefly introduce the coalescent approach, including basic components and important extensions. I will first discuss how to implement the coalescent events that relates the sample of DNA sequences via their common ancestry, assuming that the process follows the Wright-Fisher model. Then the assumptions of this model are relaxed or changed one by one to fit our goal of studying haplotype polymorphism. For example, mutations must be incorporated into the model as they are the sources of polymorphism. Recombination is another indispensable element because it creates the boundaries of haplotypes (by breaking down the associations between SNPs) and linkage disequalibrium. I will also add some other genetic or population events. <b><TBC></b> These events consists of a stochastic process that produces not only a set of simulated DNA sequences but also their genealogical relationship and evolutionary histories.

This process is assumed to have the so-called Markov property -- the state of a specific next step in the process only depends on the present state (i.e., memory-less). In genetics it is natural to assumes that the probability of a future event depends only on the current situation. When time is continuously measured (as a non-negative real value), the above process is called a continuous-time Markov chain/process and is closely related with the exponential distribution (the only continuous distribution that is memory-less). If a random variable U follows an exponential distribution, denoted U ~ Exp(λ), then Prob(U <= t) = 1 - exp(-λ*t), where λ > 0 is the parameter of the distribution called the rate parameter. Let X1, ..., Xn be independent exponentially distributed random variables with rate parameters λ1, ..., λn. A property of exponential distributions is that min{X1, ..., Xn} is also exponentially distributed, with parameter λ = λ1 + ... + λn. The index of the variable which achieves the minimum is distributed according to the law Prob(Xk=min{X1, ..., Xn}) = λk/(λ1 + ... + λn). As will be more clear below, this property is important for constructing a unified model incorporating aforementioned various evolutionary events, which are often assumed to be independent. Generally, there are multiple types of events (see below) that are independent and compete to occur during the coalescent process. The time interval between each type of events has an exponential distribution parameterized by the rate of this specific type of events(scaled on population size N). The time interval between two subsequent events (not necessarily the same type) is the minimum of all exponential distributions, which itself is also an exponential distribution with a parameter given by summation. For more information, please refer to the resources provided at the end of this post.

<h2>The Wright-Fisher model</h2>
The Wright-Fisher model provides a dynamic description of the evolution of an idealized population and the transmission of genes from one generation to the next. This basic model of reproduction makes the following assumptions:
<ul>
<li>Discrete and non-overlapping generations: all of the individuals in the population die each generation and are replaced by offspring.</li>
<li>Constant population size through time: the population size <b>N</b> (different from sample size) is assumed to be constant over time and finite. When the individuals that constitute the population are haploid organisms (they have only one copy of genetic materials), the population will consist of N copies of the <b>genome</b> (the genome of an organism is its whole hereditary information and is encoded in the DNA or RNA). In case of diploid organisms (e.g., humans, with two copies of genetic materials), there will be 2N copies.</li>
<li>Random mating (no structure): next generation is drawn randomly from a large gamete pool. Statistically, the next generation is formed from the current generation by uniformly sampling with replacement. Thus, the evolutionary process can be traced back as the sequences in every generation randomly picking their parents in the previous generation.</li>
<li>Neutral mutation: the mutation changes the DNA sequence but not an individual’s ability to survive and to produce offspring</li>
<li>Population size N is large: most results concerning this model will be approximate (referred to as diffusion approximation), ignoring terms of O(1/N^2) relative to O(1/N). For example, one example following this assumption is that we do not consider the simultaneous coalescence of more than two DNA sequences (thus the generative genealogical tree is a bifurcate tree.</li>
<li>No recombination: only true for single bases and perhaps short regions</li>
</ul>
A reproduction process following the Wright-Fisher model results in a decay of genetic variation. Since the population is finite in size and reproduction is a random process, some individuals may not contribute any offspring to the next generation. This random loss of genetic lineages forward in time is called <b>genetic drift</b>, which reduces the diversity of the population diversity. One measure of population diversity is <b>heterozygosity</b>, defined as the probability that two genes chosen at random from the population have different alleles. Alleles are different versions of the genetic information encoded at a location in the genome of an organism (aka, genetic locus). A common example of genetic locus is the sequence of nucleotides that makes up a gene. Thus, two sequences of the same gene are different alleles if they are not identical. Assuming a gene has two allelic states (denoted A and a), genetic drift eventually leads to either A or a being lost from the population. When this happens, the surviving allele is said to be fixed in the population. The effect of genetic drift is compensated by mutation, a process by which the allelic state of a gene occasionally changes from one to another (e.g., from A to a). 

<h2>Coalescent Events</h2>
<b>Parameters</b> (as denoted in the literature/as the input of <a href="http://lybird300.github.io/2015/04/04/CoJava.html">Cosi or CoJava</a> if applicable)
<ul>
<li>n | sample_size: the number of DNA sequences (or genes/chromosomes/haplotypes depending on the context) being sampled</li>
<li>N | pop_size: the number of individuals in the population; thus, diploid populations each has 2N sequences</li>
<li>k | numnodes: the number of sequences at one generation in the genealogy, or the number of corresponding nodes in the subgraph of an <a href="http://www.math.canterbury.ac.nz/~r.sainudiin/recomb/ima.pdf">ARG (Ancestral Recombination Graph)</a></li>
<li>T | gen: continuous time measured in units of 2N generations (2N generations are the average time for two genes to find a common ancestor). Modeling time in this way makes the coalescent independent of population sizen N</li>
<li>λ | rate: When using an exponential distribution to approximate the waiting time to next coalescent event, λ is the defining param of the exponential distribution</li>
</ul>
<b>Algorithm 1</b>
<ol>
<li>Start with k = n sequences</li>
<li>Simulate the waiting time to next coalescent event, T(k) ~ exp((k(k-1))/2), that is, Prob(T(k) <= t) = 1 - exp(-λ*t) where λ = k(k-1)/2 </li>
<li>Choose a random pair (i, j) with 1 ≤ i < j ≤ k uniformly among k(k-1)/2 possible pairs</li>
<li>Merge i and j into one gene and decrease the sample size by one, k--</li>
<li>If k > 1 go to Step 2, otherwise stop</li>
</ol>
<b>Note</b>:
Step 2 utilizes the property that when n is much smaller than N, the probability of a coalescence event in a given generation with k sequences (i.e., for k genes to have k-1 ancestors in the previous generation) is approximately k(k-1)/(4N). Thus, the amount of waiting time (measured in 2N-generation units) during which there are k lineages, T(k), has approximately an exponential distribution with mean k(k-1)/2. The mean of an exponential distribution is equal to the decay rate of the same distribution (i.e., λ).

<b>Primary CoJava functions</b> 
<br/>
Update the ARG: /populationStructures/demography.java/coalesceByName()

<h2>Mutation events</h2>
<img alt="mutation" src="https://cloud.githubusercontent.com/assets/5496192/7108656/3cdaa94e-e15b-11e4-81de-4807091b966b.PNG" />
<p>We consider strictly neutral mutations that will not affect an individual's fitness (the individual's ability to survive and to produce offspring). Such mutations should not affect the simulated genealogies, because they have no effect on the number of offspring or individuals' tendency to migrate. This has two consequences.The first consequence is an efficient computer algorithm, in which the coalescent process is modeled by separating the neutral mutation process from the the genealogical process. We can first generate the random genealogy of the individuals backward in time, and then superimpose mutations forward in time. The second consequence is that we can choose from various mutation models (e.g., infinite-allele, infinite-site, or finite-site model) without influencing the statistical properties of resultant genealogies.</p>

<p>The <b>infinite sites model</b> assumes that a mutation will always happen in a new site/position (thus "infinite sites"), so all mutations are distinguishable (i.e., no recurrent or reverse mutations). Moreover, there will always be one or two states in a position, never more, because each position mutates at most once. Thus, alleles are often labelled as 0 or 1(and a sequence as a string of zeros and ones) regardless their specific meanings. The infinite sites model can be interpreted as describing the evolution of very long DNA sequences with low mutation rate at each position. In contrast, the <b>finite sites model</b> admits that a DNA sequence has a fixed length. Both models assume that positions evolve (via mutation) independently of each other, i.e., a mutation in one position does not influence the chance of a mutation in another position. In principle a mutation model should also describe chromosome-level <a href="http://moodleshare.org/mod/book/view.php?id=1697&chapterid=142">mutations</a> (e.g., insertions, deletions, etc.), but these events occur so rarely that they normally can be ignored. For the infinite sites model, we can set up an overall rate to comprise both the rates of nucleotide-level and chromosome-level mutations. For the finite sites model, however, usually only nucleotide substitutions are modeled.</p>

<b>Parameters</b>
<ul>
<li>Ttot | : total evolutionary time available, represented by the total branch lengths of a genealogy. We can compute it by summing over the product of each coalescent interval T(k) (see above) and the number of lineages sharing that interval k: <br/><img alt="ETtot" src="https://cloud.githubusercontent.com/assets/5496192/7070529/9819a4a0-dead-11e4-8dad-3fe8d0803b9d.png"/></li>
<li>μ | theta: mutation rate per sequence per generation. It is the product of mutation rate per base pair (bp) and sequence length (in terms of bp). The latter two are both input parameters of CoJava.</li>
<li>S | : the number of segregating sites, i.e., the number of DNA sequence positions where some pair of sample sequences differ. We can think of it as the total number of mutations to be imposed on the entire genealogy. In the infinite-sites model, the expected number of segregating sites for a diploid sample is (θ is often referred to as the SCALED mutation rate):<br/><img alt="ES" src="https://cloud.githubusercontent.com/assets/5496192/7075088/5e9e7faa-decd-11e4-8143-8c6a864beaee.png"/> </li>
<li> t | : the length of a branch in the genealogy, calculated as difference between the scaled time at the ancestral node (i.e., in the unit of 2N) and that at the descendant node. The number of mutations on each branch follows a <a href="http://en.wikipedia.org/wiki/Poisson_distribution">Poisson distribution</a> with arrival intensity tθ/2. </li>
</ul>
<b>Algorithm 2</b>
<ol>
<li>Execute Algorithm 1 to simulate the genealogy of n sequences</li>
<li>For each branch draw a number, Mt, from a Poisson distribution with intensity tθ/2, where t is the length of the branch. Later there will be Mt mutation events added to this branch. </li>
<li>Starting at the root, move forward in time and modify the sequences produced in Step 1. <b>For infinite sites models</b>, add Mt mutations to the descendant sequence of each branch; the position of a mutation is randomly chosen along the sequence.</li>
</ol>

<b>Primary CoJava classes or functions</b>
/geneticEvent/mutations.java
/coalSimulator/sim.java/simMutate()
By default, Cosi and CoJava is a finite-sites simulation in that mutations occur at discrete sites and if multiple mutations occur at a single site, only the first one is retained. However, setting the parameter "infinite_sites" to yes (see the exemplary param file in <a href="http://lybird300.github.io/2015/04/20/cojava-manual.html">this post</a>) converts the output positions to floating point, with all mutations retained. Moreover, both programs allow users to set up the (fixed) number of mutations.

<h2>Recombination and gene conversion events</h2>
When diploid individuals reproduce, there are two parents, each of which contributes one of its two <a href="http://www.phschool.com/science/biology_place/labbench/lab3/homologs.html">homologous chromosomes</a>, or a combination of both when they undergo recombination. As shown in the figure below, an individual obtains a chromosome from a parent and the chromosome has two different ancestor sequences, each from one grandparent.
<br/><img alt="cross-over" src="https://cloud.githubusercontent.com/assets/5496192/7258041/eb9ec8a2-e827-11e4-96b0-7b2cb6d1db0f.png" />
As in the standard WF model, we ignore the existence of individuals and focus on DNA sequences. Since a recombination event splits the genetic material of a sampled sequence onto two different ancestors (but each single point on the sequence has exactly one thread connecting all its ancestors), it is formulated as opposite and competing to an coalescent event, which combines two sample sequences into one ancestor. (Recall that different types of events are treated as competing in the general coalescent framework.)
<img src="https://cloud.githubusercontent.com/assets/5496192/7117355/14e99006-e1c6-11e4-9808-74ae386ebced.PNG" />
Assuming lineages recombine independently, the total rate of recombination when there are k lineages is kρ/2. Thus, given only coalescence and recombination events, lineages increase at a linear rate and decreases at a quadratic rate (the total rate of coalescence whenever there are k lineages is k(k − 1)/2). As a result, the number of lineages is guaranteed to stay finite and will eventually hit one. Notably, we also assume that a sequence is unlikely to get involved in a recombination event and a coalescent event (or two recombination or coalescence events) at the same time, due to the low probability of these events in a large population (big N). For example, the co-occurrence of recombination and coalescence (as illustrated below) has a probability of 1/2N * r = ρ/(8N^2) when r = ρ/(4N), which is negligible for large N and fixed ρ.

<b>Parameters</b>
<li>r: recombination rate as the probability of one recombination per generation (<b>not the sample size n</b>). Analogous to the definition of the scaled mutation rate, we often define the scaled recombination rate as ρ = 4Nr. </li>
<b>Algorithm </b>: a WF model with recombination
<ol>
<li>Start with k = n genes.</li>
<li>For k sequences with ancestral material, draw a random number from the exponential distribution with parameter k(k-1)/2 + kρ/2. This is the time to the next event.</li>
<li>With probability (k − 1)/(k − 1 + ρ) the event is a coalescence event,
otherwise it is a recombination event.</li>
<li>If it is recombination, draw a random sequence and a random point on
the sequence. Create an ancestor sequence with the ancestral material
to the left of the chosen point and a second ancestor with the ancestral
material to the right of the recombination point. Increase the number of
ancestral sequences k by one and go to 1.</li>
<li>If it is a coalescence event choose two sequences among ancestral
sequences at random and merge them into one sequence inheriting the
ancestral material to both of the sequences. Decrease k by one. If k = 1
end the process, otherwise go to 1.</li>
</ol>
<b>Primary CoJava functions</b> 
<br/>
Find the recombination location on the sequence: geneticEvents/RecombWorker.java/recombExecute()
Update the ARG: /populationStructures/demography.java/recombineByIndex()
Create ancestral sequences: /populationStructures/nodeWorker.java/nodeRecombine()
Cosi and CoJava allow recombination rates to vary according to a predefined recombination file (see <a href="http://lybird300.github.io/2015/04/20/cojava-manual.html">this post</a>), which defines a genetic map or <a href="http://en.wikipedia.org/wiki/Recombination_hotspot">hotspots</a> along the genome


<b>Generating (artificial) sequences</b>





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

