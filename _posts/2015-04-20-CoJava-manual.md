---
layout: post
title: "CoJava Manual"
date: 2015-04-20
---
<h2>Initiate CoJava</h2>
The program must be invoked with the following command-line arguments
<ul><li>"-p" (REQUIRED) specifies the parameter file, which is a text file that describes the demographic model to be simulated.</li>
<li>"-o" (REQUIRED) specify the base name for output files. Output consists of a pair of files for each sampled population, one containing a list of all variant sites (with position and allele frequencies), the other containing the haplotypes for that population.</li><li>"-l" specify the log file that</li><li>"-s" specify the seg file that</li><li>"-proc" specify the number of processors used in the fork/join framework of Java 8 (it is 1 by default)</li></ul>

<h2>Set up parameters</h2>
The parameter file defines the population structure and other input parameters, using keywords. In this file, comments are indicated by "#" at the beginning of a line. Newlines don't matter. The file includes the following options, which you can comment out by adding "#" to the beginning of each line.

<pre><code>infinite_sites yes</code></pre>
The default is a finite sites simulation, in that mutations occur at discrete sites; if multiple mutations occur at a single site, only the first one is retained. Setting "infinite_sites" to yes converts the output positions to floating point, with all mutations retained.

<pre><code>number_mutation_sites 2000</code></pre> 
Use this option when you prefer a fixed number of mutation sites

<pre><code>random_seed 183122717</code></pre>   
This option specifies a particular random number seedUseful for debugging or recreating a previous run. If a seed of zero is supplied, or the keyword is not found, a random seed will be generated from the time and process id of the job. 

<pre><code># length &lt;sequence length in bp (base pair)&gt;
length 200</code></pre>

<pre><code># mutation_rate &lt;mutation rate per bp per generation&gt;
mutation_rate 1.5e-8</code></pre>

<pre><code># recomb_file &lt;file_name&gt;
recomb_file model.test</code></pre>
This option specifies the file describing the genetic map to be used (<a href="http://lybird300.github.io/2015/04/21/gene-maps.html">a brief introduction of genetic map</a>). The file has two columns separated by whitespace:
<pre><code>&lt;position (kb)&gt; &lt;recomb prob per bp per generation&gt;</code></pre>
The first column gives a base-pair position; the second column sets the crossover recombination rate, per generation, from that point until either the end of the sequence region or the position specified by the next line. The basepair positions in the first column must be in strictly increasing order. #The first line of the genetic map file also specifies the recombination rate from the beginning of the region to the base-pair position of that line.

Gene conversion is specified by the following parameters:
<pre><code>#gene_conversion_rate &lt;rate of initiation per bp per generation&gt;
gene_conversion_rate 4.5e-9</code></pre>
You can also use <code>gene_conversion_relative_rate</code> to indicate the rate of gene conversion initiation relative to crossover recombination rate per generation, and <code>gene_conversion_mean_tract_length</code> to indicate the mean length of gene conversion tract in bp (the default value is 500 bp).

Define rare event rates: 
<pre><code>inversion_rate 1.0e-7
insertion_rate 1.0e-7
deletion_rate 1.0e-7</code></pre>

Any population that appears in the simulation, either as a source of samples or in the history of those samples, must be defined in the file; at least one sampled population is required. The syntax for defining a population is:
<pre><code>pop_define &lt;pop id&gt; &lt;label&gt;
pop_size &lt;pop id&gt; &lt;size&gt;
sample_size &lt;pop id&gt; &lt;n sample&gt;</code></pre>
<code>pop id</code> is an integer ID of the population, used to refer to the population when specifying demographic events. <code>label</code> is human-readable name for the population (a string with no spaces and not put in quotes). <code>pop_size</code> indicates the effective present-day population size. <code>sample_size</code> indicates the number of sampled individuals. For example,
<pre><code>pop_define 1 european
pop_define 3 african-american
pop_define 4 asian
pop_define 5 african

# European
pop_size 1 100000
sample_size 1 10

# African American
pop_size 3 100000
sample_size 3 10

# Asian
pop_size 4 100000
sample_size 4 10

# African
pop_size 5 100000
sample_size 5 10</code></pre>

Parameters that define the demographic history of the populations are as follows and they can be supplied in any order.
<pre><code>#pop_event migration_rate &lt;label&gt; &lt;source pop id&gt; &lt;target pop id&gt; &lt;T&gt; &lt;probability per chrom per gen&gt;
pop_event migration_rate "afr->eur migration" 5 1 0. .000032
pop_event migration_rate "eur->afr migration" 1 5 0 .000032
pop_event migration_rate "afr->as migration" 5 4 0. .000008
pop_event migration_rate "as->afr migration" 4 5 0 .000008
pop_event migration_rate "afr->eur migration" 5 1 1996 0
pop_event migration_rate "eur->afr migration" 1 5 1995 0
pop_event migration_rate "afr->as migration" 5 4 1994 0
pop_event migration_rate "as->afr migration" 4 5 1993 0</code></pre>
Time <code>T</code> is measured in generations and increases going into the past (present = 0); It can be fractional so is defined as a double value. Labels are used only to provide human readable output.

You can also use<code>change_size</code> to set the population size for all times prior to <code>T</code>
<pre><code>pop_event change_size &lt;label&gt; &lt;pop id&gt; &lt;T&gt; &lt;size for time prior to T&gt;
pop_event change_size "agriculture - african" 5 200 24000
pop_event change_size "agriculture - european" 1 350 7700
pop_event change_size "agriculture - asian" 4 400 7700
pop_event change_size "african pop size" 5 17000 12500</code></pre>

<code>exp_change_size</code> is used to indicate an exponential change. The following example specifies an exponential population increase in population 1 that started 500 generations ago and ended 50 generations ago, increasing from 1000 to 10000. Prior to 500 generations, the size remains at 1000 (unless changed by another pop_event parameter); more recently than 50 generations ago, the population size is whatever was set by the pop_size command.
<pre><code># pop_event exp_change_size &lt;label&gt; &lt;pop id&gt; &lt;Tend&gt; &lt;Tstart&gt; &lt;final size&gt; &lt;start size&gt;
pop_event exp_change_size "expansion" 1 50 500 10000 1000</code></pre>

<pre><code># pop_event bottleneck &lt;label&gt; &lt;pop id&gt; &lt;T&gt; &lt;inbreeding coefficient&gt; 
pop_event bottleneck "african bottleneck" 5 1997 .008
pop_event bottleneck "asian bottleneck" 4 1998 .067
pop_event bottleneck "european bottleneck" 1 1999 .02
pop_event bottleneck "OoA bottleneck" 1 3499 .085</code></pre>

<pre><code># pop_event admix &lt;label&gt; &lt;admixed pop id&gt; &lt;source pop id&gt; &lt;T&gt; &lt;fraction of admixed chroms from source&gt;
pop_event admix "african american admix" 3 1 5. .2</code></pre>

<pre><code># pop_event split &lt;label&gt; &lt;source pop id&gt; &lt;new pop id&gt; &lt;T&gt;
pop_event split "african to aa" 5 3 7.0
pop_event split "asian and european split" 1 4 2000
pop_event split "out of Africa" 5 1 3500</code></pre>

<pre><code># pop_event sweep &lt;label&gt; &lt;pop id&gt; &lt;end time&gt; &lt;selection coefficient&gt; &lt;position of<br/> selected allele (as fraction)&gt; &lt;final frequency&gt;
pop_event sweep "selective sweep" 5 10000 .02 .5 .4</code></pre>

<h2><a name="output">Output files</a></h2>
<p>cosi provides two output files for each simulated population, named out.hap-? and out.pos-? (replace the '?' with a specific population index --  1 european, 3 african-american, 4 asian, 5 african).</p>
<p>The out.hap-? file contains the simulated samples (chromosomes/haplotypes). Each line corresponds to a sample, including ID of the chromosome (sample ID), label of the population this chromosome belongs to, and then the simulated sequence (sites separated by a blank space). The original state and the mutation are represented by "2" and "1" respectively. The file is formatted as below:</p>
<pre><table border="0">
  <tr>
    <th>Chrom #</th>
    <th>Pop #</th> 
    <th colspan="11">Simulated sequence</th>
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
</table></pre>
<p>The out.pos-? file contains the information for each segregating site (i.e., SNP), includling its ID (starting from 0), ID of the chromosome (i.e., sample ID) the SNP is on, chromosome position of the SNP, and the frequency of each allelic state of the SNP. The file has the following format:</p>
<pre><table border="0">
  <tr>
    <th>SNP #</th>
    <th>Chrom #</th> 
    <th>Chrom pos</th>
    <th>Allele 1</th>
    <th>Freq of A1</th>
    <th>Allele 2</th>
    <th>Freq of A2</th>
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
</table></pre>

<a href="">Java Doc</a>
