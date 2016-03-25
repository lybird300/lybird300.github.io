---
layout: post
title: "Using other applications on Centros Linux (as a cluster user who cannot do sudo)"
date: 2016-03-25
---
Before we start, it is worth introducing some commands related with the linux module system, which uses environment modules to set up software applications based on a particular user's computational needs. When you first login to the cluster, you're presented with a very basic software environment. You then make apps available by loading the modules for them. The modules update shell environment variables so that the system can find and use the apps of your choice. There are different implementation of environment modules. The following commands have been tested by me on RENCI cluster.
To see what apps are available to load, run the command:
<pre><code>module avail</code></pre>
This only shows what is immediately available to load. Upon loading some modules, more modules may become available. Specifically, loading a compiler implementation or MPI implementation will make available all the apps built with/against those apps.
To see a description of any particular MODULE, use the help or whatis command (here you can use TAB to finish the complete module name):
<pre><code>module whatis MODULE
e.g., module whatis python/2.7.7
</code></pre>
To load a module named MODULE, run the command:
<pre><code>module load MODULE</code></pre>
Note that most modules names are of the form NAME/VERSION. Specifying just NAME (without the /VERSION), will automatically load the latest version. Loading a module may automatically load other modules it depends on.
To see what modules you have loaded, run:
<pre><code>module list</code></pre>
To unload a module, run:
<pre><code>module unload MODULE</code></pre>
To unload all modules currently loaded, run:
<pre><code>module purge</code></pre>

<h2>Working with R</h2>
Although R is pretty amazing, ever since I started working on Renci clusters I've been avoiding it, because according to some of my colleagues, the installation process is painful and because the last several months I was working on really large data sets and R is not very good at dealing with them. But today I think I'm gonna give it a try.
<h3>Install R</h3>
It is said that we can install R on Redhat Linux and other Redhat like distribution spins (RENCI Hatteras uses Centros) using the "yum" command. The below command will install R along with all its prerequisites:
<pre><code>$ sudo yum install R</code></pre>
Unfortunately, I cannot use this fully automatic installation choice but have to install from R source code, because as a cluster user, I don't have root privileges to install new software on the system. I downloaded the latest source code package (looks like R-3.2.2.tar.gz) and uploaded to my home directory. Then I navigate to where I put the file and decompress it:
<pre><code>tar xzf R-3.2.2.tar.gz</code></pre>
A new directory named "R-3.2.2" is created. Navigate to this directory and execute the pre-compilation script "configure":
<pre><code>$ cd R-3.2.2
$ ./configure</code></pre>
With the "configure" script you can supply a various flags to adjust the compilation to your environment. If you do not have any special requirements you can start compilation with:
<pre><code>$ make</code></pre>
<h3>Running R</h3>
To run R under Linux from the command line. You can simply go to wherever you would like to run it (for example, where you put data files) and type "R" at the command line and press enter. Wolla, you are now in an R environment and you can type in "q()" to get out. However, this is not the major way of launching R in a cluster if you want to execute a large set of commands in sequence and allow for inputs and outputs. For these purposes, you should write an R script (let's called example.R) and run it through batch command:
<pre><code>R CMD BATCH example.R</code></pre>
As other Unix command, there are several options that allow you to better control the process. For example,
To submit R jobs via SLURM, the R command in your SLURM batch file should be in the format:
<pre><code>R CMD BATCH --quiet --no-restore --no-save RscriptFile OutputFile</code></pre>
<ul>
<li>--quiet: silences the startup messages so that they won't appear in your output</li>
<li>--no-restore does not restore the R workspace at startup</li>
<li>--no-save does not save your R batch environment at exit</li>
</ul>
If you wish to pass along command line arguments in your SLURM batch script, you need to use the format:
<pre><code>R CMD BATCH --no-save --no-restore '--args a=1 b=c(2,5,6)' test.R test.out</code></pre> 
and include the following lines in your R script:
<pre><code>
 # First read in the arguments listed at the command line
 args=(commandArgs(TRUE))
 
 # args is now a list of character vectors
 # First check to see if arguments are passed.
 # Then cycle through each element of the list and evaluate the expressions.
 if (length (args) == 0) {
   print("No arguments supplied.")
   ## supply default values
   a = 1
   b = c(1,1,1)
 } else {
   for (i in 1:length(args)) {
      eval (parse (text = args[[i]] ))
   }
 }
 print (a)
 print (b)
</code></pre>
Your output file test.out should have the following lines in it:
<pre><code>
> print (a)
[1] 1
> print (b)
[1]  2 5 6
</code></pre>
More examples and detail can be found at this <a href="http://stackoverflow.com/questions/14167178/passing-command-line-arguments-to-r-cmd-batch">Stack Overflow post</a>. 
You can also put the batch command in a bash file (start with "#!bin/bash"; let's call it runRexample.sh) and run the bash file
<pre><code>chmod +x runRexample.sh
./runRexample.sh</code></pre>
Since it may take a while for your R script to run, you can add an "&" in the end of your command to execute it in the background.
<h3>Use packages in R</h3>
R offers a wide variety of packages for its users. Some of them need to be additionally installed into R. A package is a set of functions, help files and data files that have been linked together. In order to use a package in R you need to first make sure that it is installed in the local library. In general, the one system-level library is used for storing the default R packages. You can, however, add additional libraries. You also need to remember about loading packages into your current R session. This is very important when using R. It is recommended that you do not load too many packages at the time. Loading a large number of packages may result in errors due to clashes of function names coming from two different packages.
As a cluster user, you can can install local copies in your home directory following several steps. First, ensure the ~/.bashrc file in your home directory has the following contents (~/.../R is the directory where you keep all your packages; make sure it exists):
<pre><code>export R_LIBS_USER=~/.../R:$R_LIBS_USER</code></pre>
Enter the same command at the command line. Then you should be able to install packages at the R prompt by the usual <pre><code>install.packages("name of package here")</code></pre>
You'll be prompted to select a nearby CRAN mirror. R should then install the package. Unfortunately for me (as always...), I failed the first time when trying to install a package to my newly installed R-3.2.2. At first it gave me the following message
<pre><code>
install.packages("ggplot2")
Installing package into ‘...’
(as ‘lib’ is unspecified)
--- Please select a CRAN mirror for use in this session ---
Error in download.file(url, destfile = f, quiet = TRUE) :
  unsupported URL scheme
HTTPS CRAN mirror
 1: 0-Cloud [https]             2: Austria [https]
 3: China (Beijing 4) [https]   4: China (Hefei) [https]
 5: Colombia (Cali) [https]     6: France (Lyon 2) [https]
 7: Iceland [https]             8: Russia (Moscow 1) [https]
 9: Switzerland [https]        10: UK (Bristol) [https]
11: UK (Cambridge) [https]     12: USA (CA 1) [https]
13: USA (KS) [https]           14: USA (MI 1) [https]
15: USA (TN) [https]           16: USA (TX) [https]
17: USA (WA) [https]           18: (HTTP mirrors)
Selection:
</code></pre>
I randomly selected 16, it gave me an error message:
<pre><code>
Warning: unable to access index for repository https://cran.revolutionanalytics.com/src/contrib
Warning message:
packages ‘reshape2’, ‘ggplot2’ are not available (for R version 3.2.2)
</code></pre>
I googled it and found in <a href="https://github.com/glibiseller/IPO/issues/30">this post</a> that it happened because the repository I chose earlier (i.e., 16) did not work. I followed the solution provided in that post by typing the following and it started to download. Weird...Huh!
<pre><code>install.packages("ggplot2", repos="http://cran.cnr.berkeley.edu")</code></pre>
<h3>Run R scripts on Linux from the command line</h3>
If you want the output to print to the terminal it is best to use Rscript
<pre><code>Rscript a.R</code></pre>
You can also use the following to run R script, but instead of displaying outputs on the terminal, this command redirects outputs to a new file called a.Rout
<pre><code>R CMD BATCH a.R
# Check the output
cat a.Rout</code></pre>

<h2>Running Python</h2>
RENCI uses the Anaconda python distribution from Continuum Analytics. You can use Python and Anaconda by running:
<pre><code>module load python</code></pre>
At this point you can upgrade or install a package named PACKAGE with the following command (it's the same whether installing or upgrading):
<pre><code>conda install PACKAGE</code></pre>
The commands "conda list" and "conda show" will list installed and available packages, respectively. If the package is not available from conda, you can install it into your environment using pip:
<pre><code>pip install PACKAGE</code></pre>
Once I wanted to install from a local package (assume it's in the current directory as a compressed file snap.tar.gz), I had to do so by putting it in $HOME/.local/lib. Below is the command I used
<pre><code>pip install --user --install-option="--install-scripts=/usr/local/bin" ./snap.tar.gz</code></pre>
And to make it work (i.e., allow python to find this package), I added the following line to the ".bash_profile" file in my home directory
<pre><code>export PYTHONPATH=$PYTHONPATH:/home/linly/.local/lib/python2.6/site-packages/</code></pre>
<h2>Working with Java</h2>
I bet <a href="http://ice.he.net/~hedden/jrelinux.html">this post</a> is going to be a huge help someday.
<b>"Could not reserve enough space for object heap" even though there is enough RAM</b>
I often get the following error message, so today I decide to do some research on it.
<pre>
Error occurred during initialization of VM
Could not reserve enough space for object heap
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
</pre>
According to <a href="http://unix.stackexchange.com/questions/109653/java-could-not-reserve-enough-space-for-object-heap-even-though-there-is-enoug">this page</a>, sometimes the system tries to allocate more than I need and a solution for this problem is to limit the chunk of memory java tries to allocate at run time with a minimum Xms or while running with maximum Xmx.

<h2>Playing with Gephi on Linux</h2>
On Linux you can try some simple scripts to get a quick intuition about the network data before plotting the entire network, which can be too large to handle easily. Bash scripts are often more convenient to use than coding with other programming languages.
Suppose the network is stored in the following format (each line defines an edge)
<pre>nodeA nodeB weight</pre>
Then you can use the following command to compute node degrees (assuming indirected networks). The degree report should give you intuition about how well connected the network is.
<pre><code>
#Compute out-degree of all nodes in nodeA column
cat network.vna |  " " | sort | uniq -c | sort -n
#Compute in-degree of all nodes in nodeB column
cat network.vna | cut -f 2 -d " " | sort | uniq -c | sort -n
</code></pre>
With AWK you can easily filter your network based on the weight column.
<pre><code>
#Only print edges which have weight larger than 20:
awk 'NR <= 2 { print; next; } NR > 2 { if($3 >= 20) {print}}' network.vna > network-w20.vna
#NR stands for line number - in the example we skip first 2 lines because they are usually the vna format headers (*Tie data\nfrom to weight).
#When NR is larger than 2 the script prints the line if the third weight parameter is larger than 20.
#Output is redirected to a file network-w20.vna.
</code></pre>

<h2>References</h2>
<ul>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software-on-odyssey/modules/">Modules HowTo</a></li>
<li><a href="http://how-to.linuxcareer.com/introduction-to-gnu-r-on-linux-operating-system">Introduction to GNU R on Linux Operating System</a></li>
<li><a href="http://how-to.linuxcareer.com/running-gnu-r-on-linux-operating-system">Running GNU R on Linux Operating System</a></li>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software/r/">Use R via SLURM</a></li>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software-on-odyssey/python/">Python</a></li>
</ul>
