---
layout: post
title: "Simulating DNA sequences with Rare Variants"
date: 2015-04-30
---

Simulated data sets
Ten replicates of a livestock data structure were simulated. The structure was designed to cover a spectrum of QTL distributions, relationship structures, and SNP chip densities and to mimic some of the scenarios in which genomic selection is applied. In each replicate sequence data for 4000 base haplotypes for each of 30 chromosomes was simulated using the MaCS (Chen et al. 2009). The 30 chromosomes were each 100 cM in length comprising approximately 108 base pairs and were simulated using a per site mutation rate of 2.5*10−8 and an effective population size (Ne) of 100 in the final generation of the sequence simulation. The reduction of Ne in the preceding generations was modeled with a Ne 1000 years ago of 1256, a Ne 10,000 years ago of 4350, and a Ne 100,000 years ago of 43,500 with linear changes in between. This reflects estimates by Villa-Angulo et al. (2009) for the Holstein population.

The total number of segregating sites across the resulting genome was approximately 1,670,000. A set of 9000 segregating sites were randomly selected from the sequence to be used as candidate QTL loci in two different ways, one a randomly sampled set and the other being a randomly sampled set with the restriction that their minor allele frequency could not exceed 0.30.

One option open to researchers who would like to increase power in the context of limited case series is just to increase the control collection. This strategy might include using cases for one disease as extra controls for another (assuming suitably different disease aetiologies and similar population history). We investigated the utility of such an approach by performing simulations with 1000 cases and an increasing number of control. the ability to reject the null hypothesis of no association increase considerably with the size of the control panel.

There is an open question as to whether rarer causal alleles might have larger effect sizes than common causal alleles. If this were though plausible, then in assessing power overall for a particular chip, one could focus in Figure 3 on particular ranges of effect sizes for common causative alleles and a different range of effect sizes for rarer causative alleles.

It is becoming clear that many loci harbouring common alleles affecting common diseases will have effect sizes in the range 1.1–1.2, and our simulations demonstrate that there is almost no power to detect these in studies of the size currently underway. As has already been shown empirically [19],[20] these loci can be found by meta-analyses and follow up in larger samples of GWA findings. Slightly larger relative risks do become detectable in large samples. For example the power to detect an effect of size 1.3 jumps from almost zero with 1000 cases and 1000 controls to over 50% in a study three times the size.

effect sizes (relative risk per allele)：Slightly larger relative risks do become detectable in large samples. For example the power to detect an effect of size 1.3 jumps from almost zero with 1000 cases and 1000 controls to over 50% in a study three times the size.　a sample size of 2000 cases and 2000 controls and a relative risk at the causal SNP of 1.3

case/control: In a Genome-Wide Association Study (GWAS) the genomes of a large group of individuals
are examined to establish the presence of a significant association between a disease and
particular genes. The group of individuals is divided in cases (people with the disease) and controls
(people without). Randomly pairing haplotypes to form diplotypes, we
prospectively generated case-control data sets


Increasing evidence has suggested that rare and generally deleterious genetic variants might have a strong impact on the risk of not only rare Mendelian diseases, but also many common human diseases and related traits. According to the Common Disease/Many Rare Variants hypothesis, a common disease might be caused by multiple rare variants that are under relatively strong selection pressures. However, identifying such rare variants underlying the diseases remains to be challenging and is still an active research area. While genomewide association studies (GWAS) are incapable of identifying rare variants due to usually insufficient sample size, new methods keep emerging. Since simulation is widely used to evaluate, refine, and validate new methods, it is necessary to generate simulated data sets that can reflect realistic and critical characteristics of rare variants.

In addition to the genetic variations of human populations, other characteristics that are important for simulation include allele frequency and linkage disequilibrium (LD) patterns of genetic markers. The power of a statistical test to detect a risk locus relies heavily on the allelic spectrum (numbers and frequencies of alleles) and on the LD structure around the locus. Specifically, it is desirable for simulated data to possess both local and long range LD patterns and to maintain allele frequencies similar to the real data for which the methods are expected to be applied. 

One important part is the integration of selection effects.

<h2>References</h2>
<ul>
<li>Peng, Bo, and Xiaoming Liu. "Simulating sequences of the human genome with rare variants." Human heredity 70.4 (2010): 287.</li>
</ul>
