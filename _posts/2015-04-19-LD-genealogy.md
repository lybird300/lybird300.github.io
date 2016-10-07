---
layout: post
title: "Linkage Disequlibrium (LD) and genealogy"
date: 2015-04-19
---
Two loci are said to be in linkage equilibrium (LE) if their inheritance is an independent event. If the alleles at those loci are non-randomly inherited then we say that they are at linkage disequilibrium (LD). LD is most commonly caused by physical linkage of genes. When two genes are inherited on the same chromosome, depending on their distance and the likelihood of recombination between the loci they can be at high LD. However LD can be also observed due to functional interactions where even genes from different chromosomes can jointly confer evolutionary selected phenotype or can affect the viability of potential offspring. In families LD is highest because of the lowest numbers of recombination events (fewest number of meiosis events). This is especially true between inbred lines. In populations LD exists because of selection, physical closeness of the genes that causes low recombination rates or due to recent crossing or migration. On a population level, processes that influence linkage disequilibrium include genetic linkage, epistatic natural selection, rate of recombination, mutation, genetic drift, random mating, genetic hitchhiking and gene flow.

Gene variations that cause human diseases are the result of mutations. All existing copies of a unique mutation can be traced back to the most-recent common ancestor (MRCA) of that mutation. The descendants of the individual with that chromosome may inherit the chromosome or different segments of the chromosome, depending on the history of recombination. But we know that descendants who have the same disease must have inherited a segment containing the disease mutation. Other loci within the region covered by these pieces of chromosome will be in LD with the mutation and alleles at these loci may become polymorphic markers of the disease-causing mutation.

Recombination is the major process that breaks down the associations between SNPs. It is unclear whether haplotype block boundaries are due to recombination hotspots, or are simply the result of recombination events that happened to occur there. If the blocks are due to hotspots, then perhaps they will be common across populations. If the blocks are due to regular recombination events, then populations may or may not share them, depending on how long ago the recombination events occurred. When large chromosomal regions are examined, the regions with high association have less recombination and less genetic variation. 

Traditional linkage analysis relies on well-characterized pedigrees to locate the mutation. Often the pedigree information we can obtain only trace back a few generations, and the number of meiosis observed in pedigree is not very big. We know that the more meiosis, the more opportunities for recombination to take place, and the shorter the co-inherited region surrounding the mutation. Limited meiosis means relatively low recombination rate in nearby regions of the mutation. Thus, traditional linkage analysis has relatively small resolution. It is likely to narrow down to chromosomes regions that cover several Mb of DNA sequence and contain several hundred genes. 

In contrast, association studies focus on unrelated individuals with the same disease. These individuals are unrelated in the sense that they are genetically distant after many generations, so there will be much more meiosis and much more opportunities for recombination to take place. Thus, association analysis allows for finer-scale localization/mapping of human disease loci than linkage analysis. Association studies can find surrounding segments measured in KBs.

So in both linkage and association studies, what actually matters is the underlying genealogy. LD mapping essentially utilizes the genealogical distance between unrelated individuals, and it assumes the distance resulted from pure genetic events such as mutation and recombination. However, when we talk about population-wide gene evolution over tens of hundreds of generations, some other influential factors come into the picture, for example, natural selection, population structure, historical events such as migration and bottleneck. These factors jointly produce background expectations about human genome sequence variations that needed to be distinguished from common disease mutations.

In exploring some quick and dirty ways to understand/determine patterns of LD across the genome, here’s a simple tutorial for plotting LD in R (with a little help from <a href="https://www.cog-genomics.org/plink2/ld">PLINK linkage disequilibrium analysis</a>). To calculate LD statistics r2, we can use PLINK at the Unix command line as follows:
<pre><code>
plink --file DataMerge_testcase_commVar --r2 --allow-no-sex --out DataMerge_testcase_commVar
You can also use long format files
plink --lfile DataMerge_testcase_Set2_noErr --r2 --allow-no-sex --out DataMerge_testcase_Set2
</code></pre>
This commands create a file called DataMerge_testcase_commVar.ld that contains pairwise LD estimates (in terms of R-squared values) among SNPs (by default pairs with r2 values less than 0.2 are normally filtered out of the report; this threshold can be adjusted using --ld-window-r2).
Then we can plot the values in plink.ld using R
<pre><code>
sim<-read.table("DataMerge_testcase_commVar.ld",header=TRUE)
sim<-sim[order(sim$BP_B-sim$BP_A),]
hist(sim$R2, xlab="r2 among 43880 common snps", xlim=c(0.2,1))
plot(sim$BP_B-sim$BP_A,sim$R2,type="l",col="red",ylim=c(0,max(sim$R2)),lwd=2,xlab="Distance between SNPs (bp)", ylab="LD statistic R2")
</code></pre>
Haploview seems to be a widely used LD visualization tool, but it is terribly slow!!! While Haploview accepts plink ped/map files, it requires the following:
<pre><code>
Output files from PLINK can be loaded into Haploview using the PLINK tab on the initial loading screen. PLINK files must contain a header and at least one column header must be titled "SNP" and contain the marker IDs for the results in the file. PLINK loading also requires a standard PLINK map or binary map file corresponding to the markers in the output file. The map file can be either three or four headerless columns (the Morgan distance column is optional). The map file can also be embedded in the results file as the first few columns of the file using the "Integrated Map Info" checkbox.
</code></pre>
It is actually easier to convert plink files to haploview "linkage format" inputs as follows.
<pre><code>
#Suppose you have test.bim, test.bed, and test.fam for PLINK. You need to convert them into generate ped/map files using:
plink --bfile test --recode --out test
#Then you can convert map file to contain only marker and position
cut -f 2,4 gene.map > gene.map2
#The generated gene.ped and gene.map2 can be input into Haploview as "Linkage format".
</code></pre>
You can also take a look at <a href="http://www.stat-gen.org/tut/tut_post.html">this tutorial about using the R package (LDheatMap)</a> (the "HeatMap" section).
