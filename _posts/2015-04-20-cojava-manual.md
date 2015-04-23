---
layout: post
title: "CoJava Manual"
date: 2015-04-20
---
<h2>Initiate CoJava</h2>
The program must be invoked with the following command-line arguments
<ul>
<li>"-p" specify the parameter file that controls its behavior (required)</li>
<li>"-o" specify the base name for output files.  Output consists of a pair of files for each sampled population, one containing a list of all variant sites (with position and allele frequencies), the other containing the haplotypes for that population.</li>
<li>"-l" specify the log file that</li>
<li>"-s" specify the seg file that</li>
<li>"-proc" specify </li>
</ul>

<h2>Set up parameters</h2>
<pre class="prettyprint pre-scrollable"><code>
# The parameter file defines the population structure and other input parameters, using keywords.  
# Comments are indicated by "#" at the beginning of a line.
# newlines don't matter.

#-- options that could be uncommented
#infinite_sites yes
#The default is a finite sites simulation, in that mutations occur at discrete sites; if multiple mutations occur at a single site, only the first one is retained.  Setting "infinite_sites" to yes converts the output positions to floating point, with all mutations retained.

#number_mutation_sites 2000  # Fixed number of mutation sites

#random_seed 183122717   # Specifies a particular random number seed
# Useful for debugging or recreating a previous run.  If a seed of zero is supplied, or the keyword is not found, a random seed will be generated from the 
time and process id of the job. 

# length <sequence length in bp (base pair)>.
length 200

# mutation_rate <mutation rate per bp per generation>
mutation_rate 1.5e-8

# recomb_file identifies the recombination map to be used, with the format
# <position (kb)> <recomb prob per bp per generation>
# Each line specifies the recombination rate that will be used from that position until the next specified position, or the end of the sequence.
recomb_file model.test

#gene_conversion_rate <rate of initiation per bp per generation>
#The length of gene conversion tract is fixed at 500 bp.
gene_conversion_rate 4.5e-9

# rare event rates 
inversion_rate 1.0e-7
insertion_rate 1.0e-7
deletion_rate 1.0e-7

# Population info
# Any population that appears in the simulation, either as a source of samples or in the history of those samples, must be defined in the file; at least one sampled population is required.  
# The syntax for defining a population is:
# pop_define <pop id> <label>
# pop_size <pop id> <size>
# sample_size <pop id> <n sample>

pop_define 1 european
pop_define 3 african-american
pop_define 4 asian
pop_define 5 african

#european
pop_size 1 100000
sample_size 1 10

#african american
pop_size 3 100000
sample_size 3 10

#asian
pop_size 4 100000
sample_size 4 10

#african
pop_size 5 100000
sample_size 5 10

#Parameters that define the demographic history of the populations are as follows. They can be supplied in any order.
#pop_event migration_rate <label> <source pop id> <target pop id> <T> <probability per chrom per gen>
# time T is measured in generations and increases going into the past (present = 0); It can be fractional so is defined as a double value
# Labels are used only to provide human readable output.
pop_event migration_rate "afr->eur migration" 5 1 0. .000032
pop_event migration_rate "eur->afr migration" 1 5 0 .000032
pop_event migration_rate "afr->as migration" 5 4 0. .000008
pop_event migration_rate "as->afr migration" 4 5 0 .000008
pop_event migration_rate "afr->eur migration" 5 1 1996 0
pop_event migration_rate "eur->afr migration" 1 5 1995 0
pop_event migration_rate "afr->as migration" 5 4 1994 0
pop_event migration_rate "as->afr migration" 4 5 1993 0

# change_size sets the size for all times prior to T
# pop_event change_size <label> <pop id> <T> <size for time prior to T>
pop_event change_size "agriculture - african" 5 200 24000
pop_event change_size "agriculture - european" 1 350 7700
pop_event change_size "agriculture - asian" 4 400 7700
pop_event change_size "african pop size" 5 17000 12500

# exp_change_size is an exponential change
#pop_event exp_change_size <label> <pop id> <Tend> <Tstart> <final size> <start size>
pop_event exp_change_size "expansion" 1 50 500 10000 1000
#The line above specifies an exponential population increase in population 1 that started 500 generations ago and ended 50 generations ago, increasing
from 1000 to 10000.  Prior to 500 generations, the size remains at 1000 (unless changed by another pop_event parameter); more recently than 50 generations ago, the population size is whatever was set by the pop_size command.

#pop_event bottleneck <label> <pop id> <T> <inbreeding coefficient> 
pop_event bottleneck "african bottleneck" 5 1997 .008
pop_event bottleneck "asian bottleneck" 4 1998 .067
pop_event bottleneck "european bottleneck" 1 1999 .02
pop_event bottleneck "OoA bottleneck" 1 3499 .085

#pop_event admix <label> <admixed pop id> <source pop id> <T> <fraction of 
    admixed chroms from source>
pop_event admix "african american admix" 3 1 5. .2

#pop_event split <label> <source pop id> <new pop id> <T>
pop_event split "african to aa" 5 3 7.0
pop_event split "asian and european split" 1 4 2000
pop_event split "out of Africa" 5 1 3500

</code></pre>

<a href="">Java Doc</a>
