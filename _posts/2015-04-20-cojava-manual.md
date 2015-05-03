---
layout: post
title: "CoJava Manual"
date: 2015-04-20
---
<h2>Initiate CoJava</h2>
The program must be invoked with the following command-line arguments
<pre class="prettyprint pre-scrollable"><code>
"-p" (REQUIRED) specifies the parameter file, which is a text file that describes the demographic model to be simulated.
"-o" (REQUIRED) specify the base name for output files.  Output consists of a pair of files for each sampled population, one containing a list of all variant sites (with position and allele frequencies), the other containing the haplotypes for that population.
"-l" specify the log file that
"-s" specify the seg file that
"-proc" (REQUIRED) specify 
</code></pre>

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

# length &lt;sequence length in bp (base pair)&gt;.
length 200

# mutation_rate &lt;mutation rate per bp per generation&gt;
mutation_rate 1.5e-8

# recomb_file specifies the file describing the genetic map to be used (<a href="http://lybird300.github.io/2015/04/21/gene-maps.html">a brief introduction of genetic map</a>). The file has two columns separated by whitespace:
# &lt;position (kb)&gt; &lt;recomb prob per bp per generation&gt;
# The first column gives a base-pair position; the second column sets the crossover recombination rate, per generation, from that point until either the end of the sequence region or the position specified by the next line.
#The basepair positions in the first column must be in strictly increasing order. #The first line of the genetic map file also specifies the recombination rate from the beginning of the region to the base-pair position of that line.
recomb_file model.test

#Gene conversion is specified by the following parameters:
#gene_conversion_rate &lt;rate of initiation per bp per generation&gt;
gene_conversion_rate 4.5e-9
#gene_conversion_relative_rate &lt;rate of gene conversion initiation relative to crossover recombination rate per generation&gt;
#gene_conversion_mean_tract_length &lt;the mean length of gene conversion tract in bp&gt;
#Default value is 500 bp

# rare event rates 
inversion_rate 1.0e-7
insertion_rate 1.0e-7
deletion_rate 1.0e-7

# Population info
# Any population that appears in the simulation, either as a source of samples or in the history of those samples, must be defined in the file; at least one sampled population is required.  
# The syntax for defining a population is:
# pop_define &lt;pop id&gt; &lt;label&gt;
# <i>pop id</i> is an integer ID of the population, used to refer to the population when specifying demographic events.
# <i>label</i> is human-readable name for the population (a string with no spaces and not put in quotes).
# pop_size &lt;pop id&gt; &lt;size&gt;
# <i>pop_size</i> indicates the effective present-day population size
# sample_size &lt;pop id&gt; &lt;n sample&gt;
# <i>sample_size</i> indicates the number of sampled 
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
#pop_event migration_rate &lt;label&gt; &lt;source pop id&gt; &lt;target pop id&gt; &lt;T&gt; &lt;probability per chrom per gen&gt;
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
# pop_event change_size &lt;label&gt; &lt;pop id&gt; &lt;T&gt; &lt;size for time prior to T&gt;
pop_event change_size "agriculture - african" 5 200 24000
pop_event change_size "agriculture - european" 1 350 7700
pop_event change_size "agriculture - asian" 4 400 7700
pop_event change_size "african pop size" 5 17000 12500

# exp_change_size is an exponential change
#pop_event exp_change_size &lt;label&gt; &lt;pop id&gt; &lt;Tend&gt; &lt;Tstart&gt; &lt;final size&gt; &lt;start size&gt;
pop_event exp_change_size "expansion" 1 50 500 10000 1000
#The line above specifies an exponential population increase in population 1 that started 500 generations ago and ended 50 generations ago, increasing
from 1000 to 10000.  Prior to 500 generations, the size remains at 1000 (unless changed by another pop_event parameter); more recently than 50 generations ago, the population size is whatever was set by the pop_size command.

#pop_event bottleneck &lt;label&gt; &lt;pop id&gt; &lt;T&gt; &lt;inbreeding coefficient&gt; 
pop_event bottleneck "african bottleneck" 5 1997 .008
pop_event bottleneck "asian bottleneck" 4 1998 .067
pop_event bottleneck "european bottleneck" 1 1999 .02
pop_event bottleneck "OoA bottleneck" 1 3499 .085

#pop_event admix &lt;label&gt; &lt;admixed pop id&gt; &lt;source pop id&gt; &lt;T&gt; &lt;fraction of 
    admixed chroms from source&gt;
pop_event admix "african american admix" 3 1 5. .2

#pop_event split &lt;label&gt; &lt;source pop id&gt; &lt;new pop id&gt; &lt;T&gt;
pop_event split "african to aa" 5 3 7.0
pop_event split "asian and european split" 1 4 2000
pop_event split "out of Africa" 5 1 3500

#pop_event sweep &lt;label&gt; &lt;pop id&gt; &lt;end time&gt; &lt;selection coefficient&gt; &lt;position of selected allele (as fraction)&gt; &lt;final frequency&gt;
pop_event sweep "selective sweep" 5 10000 .02 .5 .4

</code></pre>

<h2>Output files</h2>
cosi provides two output files for each simulated population, named out.hap-? and out.pos-? (replace the '?' with the desired population index --  1 european, 3 african-american, 4 asian, 5 african) which contain the haplotypes and the information for each segregating site respectively.

The out.hap-? file contains samples formatted as below:
<table border="0">
  <tr>
    <td>Chrom #</td>
    <td>Pop # (same as ?)</td> 
    <td></td>
  </tr>
  <tr>
    <td>0</td>
    <td>1</td> 
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
  </tr>
    <tr>
    <td>1</td>
    <td>1</td> 
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
  </tr>
    <tr>
    <td>2</td>
    <td>1</td> 
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>1</td>
    <td>2</td>
    <td>1</td>
  </tr>
    <tr>
    <td>3</td>
    <td>1</td> 
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
  </tr>
    <tr>
    <td>4</td>
    <td>1</td> 
    <td>1</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>1</td>
    <td>2</td>
  </tr>
    <tr>
    <td>5</td>
    <td>1</td> 
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>1</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
    <td>1</td>
    <td>2</td>
    <td>2</td>
  </tr>
</table>
 and afterwards come
the segregating sites, each position separated by a blank space.
The out.pos-? file containing the information of each segregating site (i.e., SNP). It contains SNP #, the chromosome number
, the position of the site, and the frequency of each allele:
<table border="0">
  <tr>
    <td>SNP #</td>
    <td>Chrom #</td> 
    <td>Chrom pos</td>
    <td>Allele 1</td>
    <td>Freq 1</td>
    <td>Allele 2</td>
    <td>Freq 2</td>
  </tr>
  <tr>
    <td>1</td>
    <td>1</td>
    <td>127.3788</td>
    <td>1</td>
    <td>0.1154</td>
    <td>2</td>
    <td>0.8846</td>
  </tr>
    <tr>
    <td>2</td>
    <td>1</td>
    <td>215.9448</td>
    <td>1</td>
    <td>0.0000</td>
    <td>2</td>
    <td>1.0000</td>
  </tr>
    <tr>
    <td>3</td>
    <td>1</td>
    <td>229.8352</td>
    <td>1</td>
    <td>0.0000</td>
    <td>2</td>
    <td>1.0000</td>
  </tr>
    <tr>
    <td>4</td>
    <td>1</td>
    <td>623.0247</td>
    <td>1</td>
    <td>0.4231</td>
    <td>2</td>
    <td>0.5769</td>
  </tr>
    <tr>
    <td>5</td>
    <td>1</td>
    <td>463.2629</td>
    <td>1</td>
    <td>0.1538</td>
    <td>2</td>
    <td>0.8462</td>
  </tr>
</table>
When using infiles in a cosi output format, the user must introduce in the program the two files
provided for each population included in the analysis. The name of the haplotype files must contain a
label followed by a dash, a number indicating the population code and a point:
TC‐1.testCosi.1
TC‐2.testCosi.1
Information files must have the same label followed by a dot, the Word “pos”, a dash and the number
indicating the population code:
TC.pos‐1.testCosi.1
TC.pos‐2.testCosi.1

<a href="">Java Doc</a>
