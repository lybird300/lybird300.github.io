---
layout: post
title: "Detection of identity-by-descent (IBD) segments in population data"
date: 2015-08-31
---
An identity-by-descent (IBD) segment is a continuous segment that delinearates IBD haplotypes, i.e., haplotypes shared by inheritance from a recent common ancestor. The neutral coalescent theory in population genetics implies that all individuals have common ancestry in the distant past. Any given pair of individuals is related through many common ancestors. IBD segment detection intends to find out the most recent, single common ancestor from whom an IBD segment is inherited. Thus, when detecting an IBD segment in population data, the IBD segment must have sufficient length to provide confidence that the segment is not a fusion of multiple short IBD segments from different distant/ancient common ancestors (although some error is often allowed in identifying the segment endpoints). This length constraint is defined as the resolution of IBD segment detection for a data set and it may vary with specific data or detection algorithms.

The length of the shared IBD tract depends on the number of meioses that have occurred since the common ancestor. Therefore, closely related individuals have a high proportion of IBD, and their IBD is arranged in long continuous segments. For example, half siblings have, on average, IBD sharing across one half of the genome, and the average IBD segment length is 50 centiMorgans (cM) or approximately 50 megabases (Mb). As the relationship becomes more distant, the average proportion of the genome shared identical by descent decreases exponentially, but the average length of an IBD segment, when IBD sharing do occur, remains relatively long. For a pair of individuals separated by m meioses, the average proportion of the genome shared identical by descent (the sum of lengths of IBD segments divided by the length of the genome) owing to that shared ancestry is 1/(2^(m−1)). In contrast, the lengths of IBD segments owing to the shared ancestry are approximately exponentially
distributed with a mean of 100/m cM. In other words, individuals who have shared ancestry through a certain common ancestor 25 generations ago (with 50 meioses of separation) almost always share none of their genome identical by descent through that ancestor, but if they do have an IBD segment through that ancestor it will have a mean length of 2 cM (∼2 Mb). Further, although any given pair of unrelated individuals has very little IBD sharing, the total amount of IBD sharing in a big data set with many unrelated individuals (say N, then there will be N(N-1)/2 pairs of individuals), and the total amount per individual, can be large.

The key idea behind IBD segment detection is haplotype frequency. <b>The underlying principle for all types of detection algorithms is that shared haplotypes of very low frequency are rarely observed in multiple independently sampled individuals and therefore likely to be IBD</b>. This criterion can be applied in several ways. The first is through the length of sharing, which is a proxy for frequency. If two densely genotyped haplotypes are identical at all or most (allowing for some genotyping error) assayed alleles over a very large segment of a chromosome, then the two haplotypes are likely to be identical by descent across the whole segment. The second is a direct use of haplotype frequency: Shared haplotypes with estimated frequency below some threshold are determined to be identical by descent. The third makes use of a population genetics model to infer probability of IBD. Given the frequency of the shared haplotype and a probability model for the IBD process along the chromosome, one can estimate the probability that the individuals are identical by descent at any position on the segment. Unfortunately, empirical data are usually not at the haplotype level but unphased genotypes and we have to estimate haplotypes from genotype data (called haplotyping or phasing). Although most methods allow for phasing errors (i.e., allow some discordance to exist within shared IBD chunks), such errors significantly reduce the power to detect an IBD segment. Therefore, stringent data quality control should be applied beforehand to remove lower-quality variants.

<h2>Specific Methods</h2>
There are several types of methods for detecting IBD segments, including length-based methods, direct frequency-based methods, and probabilistic methods that determine a posterior probability of IBD. Perhaps the simplest approach to IBD segment detection is to look at the genotypes of a pair of (or more) individuals and find long shared segments that are consistent with IBD. Genotypes are consistent with IBD if they have at least one allele in common. For biallelic SNP markers, a pair of genotypes is consistent with IBD unless they are discordant homozygotes. For example, the genotype pair AA/AT is consistent with IBD, whereas the genotype pair AA/TT is not. If the data are phased, then we simply look for long regions at which two haplotypes are (almost) identical. 

The detected IBD segment may be false-positive due to the presence of strong linkage disequilibrium (LD) in the same region. For example, when two markers are in perfect LD (r2 = 1), concordant homozygotes at one position are always accompanied by concordant homozygotes at the other position. <b>Thus, the fundamental question is whether the regions shared between individuals are indeed IBD, i.e., inherited from a common ancestor, or just appear so by chance or because of LD</b>. (Note, however, LD is the reason why we typically consider IBD in chunks/segments rather than in alleles.) Arguably, as mentioned in <a href="http://lybird300.github.io/2015/04/29/IBDvsIBS.html">one of my other posts</a>, LD exists because of common ancestry too. However, researchers are mostly interested in detecting recent IBD segments resulting from shared ancestry within the past 25 generations or so (for humans), as these segments are fairly large (approximately 1.5-2 cM) and thus detectable with a dense SNP array or sequence data. More ancient IBD segments (below 1-1.5 cM) is difficult, if ever possible, to detect in light of the large number of mutations that have aggregated over such time scales and the larger number of recombination events that have disrupted any shared IBD tracts. More ancient segments are typically referred to as IBS instead of IBD. Thus, an important parameter of any haplotype-length-based method is the length threshold beyond which IBS haplotypes are considered IBD, i.e., the length of IBD-consistent SNPs required to confidently infer recent IBD (as opposed to random chance and ancient IBD). The specific length threshold often depends on (a) how well the method adjusts for LD and (b) the desired tradeoff between false-negative and false-positive results.

A representative length-based method is GERMLINE (). GERMLINE uses a sliding window with a hash table to find matching haplotypes. The width of the window is inversely proportional to the phase and genotyping errors (in other words, the mismatches) that the method is allowed to tolerate. For one mismatch per window the width will be approximately 100 SNPs. GERMLINE is very computationally ecient, yet it is a heuristic approach and more successful approaches have used it as a starting method before using probabilistic models (e.g., refined IBD). Price et al. [2011] exploit the long-range phased data from the Icelandic population in order to search for IBD
segments. They split the genome in 2cM chunks and call the segments IBD if
they are identical at >95% of sites (from a 4-way comparison of haplotypes of
two individuals), non-IBD if they match at <85% and unknown otherwise.

For common SNPs, the frequency of incompatible
homozygotes is rare and therefore a very large number of SNPs in IBS are required
for IBD to be called condently. This is further enhanced by LD, which increases the probability of seeing segments that are consistently IBS or consistently
not in IBS. The heuristic threshold for length therefore depends on the
trade-o between sensitivity and specicity to IBD tracts: an equal weight is
equivalent to about 3cM (Miyazawa et al. [2007]), while 10cM will achieve high
specicity rates (Kong et al. [2008]).

Haplotype-frequency-based methods look for shared haplotypes that have frequency below some predetermined threshold, because, again, this is unlikely to occur by chance. Similarly to imposing length thresholds, it is equivalent to consider a threshold
on haplotype frequency: when rare haplotypes are in IBS it is unlikely that they
are shared by chance and may therefore be determined to be IBD. One method
following this approach is BEAGLE fastIBD (Browning and Browning [2011a]),
which uses the BEAGLE LD structure and is an extension of the probabilistic
method BEAGLE IBD. FastIBD uses a sliding window of hash tables, using
multiple estimates of an individual's haplotypes in order to accommodate the
possibility of phasing errors. The threshold for the haplotype frequency for calling
IBD is user-specied.
Both of these approaches for calling IBD are heuristic and will thus suer
with identifying short IBD segments (<2cM). It is therefore preferable to consider
probabilistic approaches that are model-based.

Probabilistic approaches directly calculate the probability that two haplotypes are in IBD. They have the greatest potential to yield high power with low false-positive rates because they can account for all aspects of the underlying data generation processes. However, it is difficult to incorporate LD into these methods without causing slow computation times.

When comparing methods for IBD segment detection, it is important to consider power to detect IBD segments, false-positive rates, and computational time for a range of sample sizes. Comparison of methods in terms of power without controlling false-positive rate is meaningless, as false-positive rates can differ substantially between methods. Many methods have tuning parameters that allow a trade-off between false-positive rate and power. Computational time is also important when we need to handle large sample size. Existing methods that can handle large samples are PLINK (), GERMLINE (), and RefineIBD (Browning and Browning, 2013)

 

<h2>Applications</h2>


<h2>Future issues</h2>
The field of IBD segment detection has developed rapidly in response to increased density of genetic data along chromosomes. Full sequence data will soon be readily available, and much greater resolution of IBD detection is to be expected in such data. IBD detection in full sequence data will benefit from new methods that make use of rare variants (they are highly informative for IBD and should be utilized) and account for sequencing error rates (sequence data has higher rates of error than SNP array data, which only shows SNPs, so these errors will need to be accommodated) and very recent mutations (they disrupt sequence identity in individuals with recent common ancestry).

In addition, most existing methods for IBD detection consider only pairs of individuals. Existing methods for considering multiple individuals simultaneously are either too slow for large data sets or are fairly ad hoc with less than optimal characteristics.

<h2>References</h2>
<ul>
<li>Browning SR, Thompson EA. 2012. Detecting rare variant associations by identity-by-descent mapping
in case-control studies. Genetics 190:1521–31</li>
<li>Gusev A, Kenny EE, Lowe JK, Salit J, Saxena R, et al. 2011. DASH: a method for identical-by-descent
haplotype mapping uncovers association with recent variation. Am. J. Hum. Genet. 88:706–17</li>
</ul>
