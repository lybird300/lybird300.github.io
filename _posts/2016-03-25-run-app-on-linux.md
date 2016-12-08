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

<h2>Working with Eclipse</h2>
The first thing to remember is that, to run an executable by issuing a command from the terminal, if the executable is not on the path (i.e., environment variable $PATH), you need to specify the directory - even if it is the current directory. Linux is different than Windows in that regard. Thus, even if you have been "cd.." to the eclipse folder where the ecplise executable file exists, you still need to run the following command
<pre><code>./eclipse</code></pre>
instead of just typing
<pre><code>eclipse</code></pre>
And this is true for all executables, not just Eclipse!!!
I wrote a shortcut script as below, named it as "runClips" and put it in my home directory bin folder (path in $PATH), so that whenever I need to run Eclipse, I only need to type "runClips" at the command line.
<pre><code>
#!/bin/bash
cd /home/linly/eclipse
./eclipse -vm /usr/java/jdk1.7.0_60/bin/java &
</code></pre>
The -vm option is recommended, as it specifies a specific JVM for Eclipse to run on.Doing this ensures that you are absolutely certain which JVM Eclipse will run in and insulates you from system changes that can alter the "default" JVM for your system.
Some useful options when you need to clean up a mess are
<ul>
<li>-clean: Cleans cached data used by the OSGi framework and Eclipse runtime. Try to run Eclipse once with this option if you observe startup errors after install, update, or using a shared configuration.</li>
<li>-initialize: Initializes the configuration being run. All runtime related data structures and caches are refreshed. Handy with shared installs: running Eclipse once with this option from an account with write privileges will improve startup performance.</li>
</ul>

Eclipse startup is also controlled by the options in eclipse.ini . eclipse.ini is a text file (often in your Eclipse installation directory) containing command-line options that are added to the command line used when Eclipse is started up. There are <a href="http://help.eclipse.org/mars/index.jsp?topic=/org.eclipse.platform.doc.isv/reference/misc/runtime-options.html">many options</a> available. However, it is recommanded that you experiment with changes to the launch command from your Command Prompt/Terminal before changing the eclipse.ini itself. Only the changes that you are pretty certain should go in eclipse.ini. The default setting of the JVM I used (indicated a few lines above) is as follows.
<pre><code>
JVM terminated. Exit code=1
/usr/java/jdk1.7.0_60/bin/java
-Dosgi.requiredJavaVersion=1.7
-XX:MaxPermSize=256m
-Xms256m
-Xmx1024m
-jar /home/linly/eclipse//plugins/org.eclipse.equinox.launcher_1.3.100.v20150511-1540.jar
-os linux
-ws gtk
-arch x86_64
-showsplash /home/linly/eclipse//plugins/org.eclipse.platform_4.5.2.v20160212-1500/splash.bmp
-launcher /home/linly/eclipse/eclipse
-name Eclipse
--launcher.library /home/linly/eclipse//plugins/org.eclipse.equinox.launcher.gtk.linux.x86_64_1.1.300.v20150602-1417/eclipse_1612.so
-startup /home/linly/eclipse//plugins/org.eclipse.equinox.launcher_1.3.100.v20150511-1540.jar
--launcher.appendVmargs
-exitdata 80001
-product org.eclipse.epp.package.java.product
-vm /usr/java/jdk1.7.0_60/bin/java
-vmargs
-Dosgi.requiredJavaVersion=1.7
-XX:MaxPermSize=256m
-Xms256m
-Xmx1024m
-jar /home/linly/eclipse//plugins/org.eclipse.equinox.launcher_1.3.100.v20150511-1540.jar 
</code></pre>

If you want to disconnect a project with its SVN repository, right click the project name in "Package Explorer", choose "Team > Disconnect" in the drop-down menu.

<h2>Working with R</h2>
Although R is pretty amazing, ever since I started working on Renci clusters I've been avoiding it, because according to some of my colleagues, the installation process is painful and because the last several months I was working on really large data sets and R is not very good at dealing with them. But today I think I'm gonna give it a try.
<h3>Install R</h3>
The distribution version of the linux system used by our cluster is CentOS 6.5, which is built from the source packages of RHEL 6 update 5 (also known as RHEL version 6.5), which is a so-called "point release" of RHEL 6. According to this <a href="http://cran.us.r-project.org/">instruction</a> (R RPMS for Fedora, Red Hat Enterprise Linux and Derivatives), to install R on Redhat Linux and other Redhat like distribution spins (RENCI Hatteras uses Centros), I should use the followng command
<pre><code>su -c 'rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm'</code></pre>
Then I can install R using the "yum" command. The below command will install R along with all its prerequisites:
<pre><code>$ sudo yum install R</code></pre>
Unfortunately, I cannot use this fully automatic installation choice but have to install from R source code, because as a cluster user, I don't have root privileges to install new software on the system. I downloaded the latest source code package (looks like R-3.2.2.tar.gz) and uploaded to my home directory (or you can use "". Then I navigate to where I put the file and decompress it:
<pre><code>tar xzf R-3.2.2.tar.gz</code></pre>
A new directory named "R-3.2.2" is created. Navigate to this directory and execute the pre-compilation script "configure":
<pre><code>$ cd R-3.2.2
$ ./configure --prefix=$HOME/bin/R-3.2.2</code></pre>
With the "configure" script you can supply a various flags to adjust the compilation to your environment. If you do not have any special requirements you can start compilation with:
<pre><code>$ make</code></pre>
<h3>Interact with R</h3>
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
I googled it and found in <a href="https://github.com/glibiseller/IPO/issues/30">this post</a> that it happened because the repository I chose earlier (i.e., 16) did not work. I followed the solution provided in that post by typing the following and it started to download. Weird...Huh! But I guess it simply means trying another repository if one does not work...
<pre><code>install.packages("ggplot2", repos="http://cran.cnr.berkeley.edu")</code></pre>
When you are running R on a cluster and need to install a package, better download it to your own library (say, in the home directory). You can use the "lib" argument for this purpose.
<pre><code>install.packages("igraph", lib ="/home/linly/bin/R-3.2.2/library/", repos='http://cran.us.r-project.org')</code></pre>
<h3>Run R scripts on Linux from the command line</h3>
If you want the output to print to the terminal it is best to use Rscript
<pre><code>Rscript a.R</code></pre>
You can also use the following to run R script, but instead of displaying outputs on the terminal, this command redirects outputs to a new file called a.Rout
<pre><code>R CMD BATCH a.R
# Check the output
cat a.Rout</code></pre>

The source() function causes R to accept its input from the named file or URL or connection. Input is read and parsed from that file until the end of the file is reached, then the parsed expressions are evaluated sequentially in the chosen environment. However, when the URL is provided by Https, it is only supported on Windows system. On linux, you will get the error "https:// URLs are not supported". You should use the following command instead.
<pre><code>source(pipe(paste("wget -O -", "https://bioconductor.org/biocLite.R")))</code></pre>

<h3>Install higher-versions of R (e.g., 3.3.2)</h3>
#####Spoiler alert: the following process that you are going to observe or try if you dare is EXTREMELY long and painful##########
We should all applaud for the person who went through it and shared it online. Here is <a href="http://pj.freefaculty.org/blog/?p=315">the post</a>. Although it is originally for building R-devel on RedHat Linux 6, I found it also suitable for installng high-version R on RENCI cluster that uses Centos 6 (exactly the same errors!!). For details you can take a look at the post. Here I will simply repeat the procedure and essential bash commands I used, as well as potential traps. Note that I did not use "builddir" or $HOME/package as suggested in the post, which may be a better and cleaner way. I simply put everything in $HOME/bin
<pre><code>
$ cd ~/bin
$ wget http://cran.rstudio.com/src/base/R-3/R-3.3.2.tar.gz
$ tar xzf R-3.3.2.tar.gz
$ cd R-3.3.2
$ ./configure --prefix=/home/linly/bin/R-3.3.2 '--with-cairo' '--with-jpeglib' '--with-readline' '--with-tcltk' '--with-blas' '--with-lapack' '--enable-R-profiling' '--enable-R-shlib' '--enable-memory-profiling'
$ make
$ make install
########Error message (partial) ###########################################
    checking if zlib version >= 1.2.5... no
    checking whether zlib support suffices... configure: error: zlib library and headers are required
##########################################################################
$ cd ~/bin (or cd .. if your R directory is already inside /bin)
$ wget http://zlib.net/zlib-1.2.8.tar.gz
$ tar xzvf zlib-1.2.8.tar.gz
$ cd zlib-1.2.8
$ ./configure --prefix=$HOME/bin
$ make
$ make install

$ cd ~/bin
$ wget http://www.bzip.org/1.0.6/bzip2-1.0.6.tar.gz
$ tar xzvf bzip2-1.0.6.tar.gz
$ cd bzip2-1.0.6
$ make -f Makefile-libbz2_so
$ make clean
$ make
$ make -n install PREFIX=/home/linly/bin/
$ make install PREFIX=/home/linly/bin/
(Go into the bzip2 directory and inserted -fPIC as a CFLAG in the Makefile as follows. Then run make and make install...again)
Line 24 Change from "CFLAGS=-Wall -Winline -O2 -g $(BIGFILES)" to "CFLAGS=-Wall -fPIC -Winline -O2 -g $(BIGFILES)"

$ cd ~/bin
$ wget http://tukaani.org/xz/xz-5.2.2.tar.gz
$ tar xzvf xz-5.2.2.tar.gz
$ cd xz-5.2.2
$ ./configure --prefix=/home/linly/bin/
$ make -j3
$ make install

$ cd ~/bin
$ wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.38.tar.gz
$ tar xzvf pcre-8.38.tar.gz
$ cd /home/linly/bin/pcre-8.38/
$ ./configure --prefix=/home/linly/bin/ --enable-utf8
$ make -j3
$ make install

$ cd ~/bin
$ wget --no-check-certificate https://curl.haxx.se/download/curl-7.47.1.tar.gz
$ tar xzvf curl-7.47.1.tar.gz
$ cd curl-7.47.1
$ env LDFLAGS=-R$HOME/openssl/lib ./configure --prefix=$HOME/bin -with-ssl=$HOME/openssl -with-zlib=$HOME/bin
(make sure all "-" in the above command is hyphen not minus)
$ make
$ make install

$ cd ../R-3.3.2
$ ./configure --prefix=/home/linly/bin/R-3.3.2/ '--with-cairo' '--with-jpeglib' '--with-readline' '--with-tcltk' '--with-blas' '--with-lapack' '--enable-R-profiling' '--enable-R-shlib' '--enable-memory-profiling'
$ make
</code></pre>

<h3>Install RStudio on Redhat/CentOS Linux</h3>
I checked out the version that I would like to install on <a href="https://www.rstudio.com/products/rstudio/download3/">this page</a> and then use the following code on linux command line
<pre><code>
cd ~/bin
wget https://download1.rstudio.org/rstudio-1.0.44-x86_64.rpm
rpm2cpio rstudio-1.0.44-x86_64.rpm | cpio -idv
(I have to use the above command instead of "rpm -Uvh rstudio-1.0.44-x86_64.rpm" or "rpm -Uvh rstudio-1.0.44-x86_64.rpm --prefix=$HOME/bin" because as a cluster user I don't have root permission)
</code></pre>

<h3>Use bioconductor</h3>
I found <a href="https://www.r-bloggers.com/where-do-i-start-using-bioconductor/">this post</a> very useful for starters. In particular, it indicates two great learning sources: bioconductor <a href="http://www.bioconductor.org/help/workflows/">workflows</a> and <a href="http://www.bioconductor.org/help/course-materials/">courses</a>
Below are some useful commands. Note that they need to be executed in R environment.
<pre><code>
#Get started
source("https://bioconductor.org/biocLite.R")
biocLite()
library(BiocInstaller)
#Download a specific package and explore its vignette
biocLite("hapFabia")
browseVignettes("hapFabia")
</code></pre>
You can extract all of the R code from a vignette source file (these files are in Sweave format) as shown below. The result will be a .R file for each vignette source file written to your current working directory. You can use these R files to cut and paste into an R session so that you can work the examples in the vignette yourself.
<pre><code>
library("tools")
vigSrc = list.files(pattern = "Rnw$",
                    system.file("doc", package = "Biostrings"),
                    full.names = TRUE)
vigSrc
for (v in vigSrc) Stangle(v)
</code></pre>


<h2>Running Python</h2>
RENCI uses the Anaconda python distribution from Continuum Analytics. You can use Python and Anaconda by running:
<pre><code>module load python</code></pre>
At this point you can upgrade or install a package named PACKAGE with the following command (it's the same whether installing or upgrading):
<pre><code>conda install PACKAGE</code></pre>
The commands "conda list" and "conda show" will list installed and available packages, respectively. If the package is not available from conda, you can install it into your environment using pip (<b>must include "--user", as it is literally</b>):
<pre><code>pip install --user PACKAGE</code></pre>
If it tells you that you should upgrade pip, also include "--user" as below
<pre><code>pip install --user --upgrade pip</code></pre>
Once I wanted to install from a local package (assume it's in the current directory as a compressed file snap.tar.gz), I had to do so by putting it in $HOME/.local/lib. Below is the command I used
<pre><code>pip install --user --install-option="--install-scripts=/usr/local/bin" ./snap.tar.gz</code></pre>
And to make it work (i.e., allow python to find this package), I added the following line to the ".bash_profile" file in my home directory
<pre><code>export PYTHONPATH=$PYTHONPATH:/home/linly/.local/lib/python2.6/site-packages/</code></pre>
To launch a python code script (e.g., p.py), type the following at the command line:
<pre><code>python (path to the code scripy)/p.py (argument list)</code></pre>
<h3>Interact with Python</h3>
As with R, you can simply type "python" at the command line. However, type "exit()" when you want to quit (for R you type "q()").
<h3>Import module installed in home directory</h3>
I don't have "sudo" permission that allows me to install packages to any compute node, but I don't want to bother Chris (our cluster admin) every time I need a new package. Fortunately, it turns out that you can do the following when you run an interactive or batch python job (for the latter, include the following commands in the script)
<pre><code>
#import the sys package to use one of its methods later
import sys
#append the path of the parent directory of your package
sys.path.append("/home/linly/.local/lib/python2.6/site-packages/")
#import your package
import networkx as nx
</code></pre>
<h2>Working with Java</h2>
This is a nice <a href="http://docs.oracle.com/javase/tutorial/getStarted/cupojava/unix.html">tutorial</a> and a good <a href="http://docs.oracle.com/javase/tutorial/getStarted/problems/index.html">Q&A</a> for newbies like me.
I bet <a href="http://ice.he.net/~hedden/jrelinux.html">this post</a> is going to be a huge help someday.
<b>"Could not reserve enough space for object heap" even though there is enough RAM</b>
I often get the following error message, so today I decide to do some research on it.
<pre>
Error occurred during initialization of VM
Could not reserve enough space for object heap
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
</pre>
According to <a href="http://unix.stackexchange.com/questions/109653/java-could-not-reserve-enough-space-for-object-heap-even-though-there-is-enoug">this page</a>, sometimes the system tries to allocate more than I need. The author said that a solution for this problem is to limit the chunk of memory java tries to allocate at run time with a minimum Xms or maximum Xmx. When you run Java application on a compute node. Type "htop" and you can see the "VIRT" and "RES" values of the running application. The former is whatever Xmx you predefined plus other memory needed to launch the application, while the latter is closer to what the application is actually using. 

<h2>Working with C++ packages</h2>
Type "make" to compile the code assuming it exists in the current directory

<h2>References</h2>
<ul>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software-on-odyssey/modules/">Modules HowTo</a></li>
<li><a href="http://how-to.linuxcareer.com/introduction-to-gnu-r-on-linux-operating-system">Introduction to GNU R on Linux Operating System</a></li>
<li><a href="http://how-to.linuxcareer.com/running-gnu-r-on-linux-operating-system">Running GNU R on Linux Operating System</a></li>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software/r/">Use R via SLURM</a></li>
<li><a href="https://rc.fas.harvard.edu/resources/documentation/software-on-odyssey/python/">Python</a></li>
</ul>
