---
layout: post
title: "Working with clusters--the SLURM version"
date: 2015-10-01
---
<h2>Set environment variables</h2>
Before the real stuffs in this post, let's talk about how to set up your environment (e.g., modify variables such as PATH) so that you can better work with the cluster. First, which file to put the command for customization. All the commands written in this file can be typed in a terminal session to customize your environment, but to make permanent changes it is better to put everything in such a file. In Linux system (such as a cluster), you should define environment variables such as PATH in the "~/.profile" file (or the "~/.bash_profile" file if you don not care about shells other than bash) in your home directory (e.g., /home/linly). The ~/.bash_rc is not read by any program, and ~/.bashrc is the configuration file of interactive instances of bash. Here is <a href="http://superuser.com/questions/183870/difference-between-bashrc-and-bash-profile/183980#183980">a good discussion on their differences</a>. 
PATH is an environment variable on Unix-like operating systems, specifying a set of directories where executable programs are located. It is a colon-separated list of directories in which the shell looks for commands. When you run a command or type an executable’s name, the system looks into PATH to find the command/executable. If you want to find the location of an executable, 'which executableName' is the command you need. Now Suppose you want to add a new path "~/opt/bin" (~ means the HOME directory, you can change it to "$HOME/bin" or "${HOME}/bin"). Depending on whether you want to add it at the end (to be searched after all other directories, in case there is a program by the same name in multiple directories) or at the beginning (to be searched before all other directories), you can do
<pre><code>export PATH=$PATH:~/opt/bin
export PATH=~/opt/bin:$PATH</code></pre>
You can add multiple entries at the same time (with variations on the ordering work just fine).
<pre><code>PATH=$PATH:~/opt/bin:~/opt/node/bin</code></pre>
BTW, in unix system an absolute path starts with "/". If you need to indicate a relative path in the current directory, give its name without "/". For example, "cd siwGWAS/...". For more take a look at one of the references. Another useful thing (please bear with me) is that you can define alias for long directory path. Take a look at <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-useful-bash-aliases-and-functions">this tutorial</a>.
In addition to PATH, there are some other commonly used shell variables. You can display their values using echo. For example, you can show HOME, the home directory of the current user, using (must use $ followed by variable name to print a variable's contents)
<pre><code>$ echo "$HOME</code></pre>
<h2>Get the execuation time of a program using unix command time</h2>
We can measure the time that a program takes to finish (aka elapsed time) by using the "time" command, for example
<pre><code>$time ./myCProgram</code></pre>
The results are 3 measurements: user, system and total time. The user time is what you are looking for. Different system may have different format setting on the result though. For example,
$ typeset -F SECONDS=0; sleep 1; print "$SECONDS"
<h2>SLURM Usage</h2>
SLURM commands are different than previous PBS commands. Below are a few commonly used commands.
<ul>
<li>List contents of the queue: squeue</li>
<li>List contents of a user's jobs in the queue: squeue -u <username></li>
<li>Remove a job from the queue by its ID: scancel "id"</li>
<li>Remove a seriel of jobs by jobID: scancel `seq 7823111 7823118`</li>
<li>Remove one or more jobs by job name (allowed for wildcard?): scancel --name myJobName</li>
<li>Remove one or more jobs from a specific user: scancel -u <username></li>
<li>Cancel all pending jobs belonging to a specific user: scancel --state=PENDING --user=<username></li>
</ul>

<h2>Submit a job</h2>
A typical command is
<pre><code>sbatch file.sh</code></pre>
Again there are many flags (i.e., options) that you can use to fit your special needs. For example,
<ul>
<li>-t: specify the walltime</li>
<li>-p: specify the queue you want to use</li>
<li>-N: specify the number of compute nodes you would like to reserve</li>
<li>-n: specify the number of cores on each compute node that you would like to reserve. Note that "nodes" and "threads" are different concepts and their values are usually not the same in modern computing systems. <a href="https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/">This post</a> elaborates on this issue well.</li>
<li>--exclusive (do NOT use this on RENCI machines): sometimes it is good to have the entire compute node reserved for your own use (i.e., not sharing with other people and this flag is for this purpose (note it starts with two slashes instead of one slash)</li>
<li>-mem=<MB>: specify the real memory required per node in MegaBytes</li>
</ul>
You can also specify your needs at the top of the shell script (i.e., file.sh) using "#SBATCH ...", for example
<pre><code>#!/bin/bash
#SBATCH -N 1
#SBATCH -n 8
#SBATCH -t 1-20:00:00 (days-hours:minutes:seconds)
#SBATCH -mem=200000
#SBATCH -job-name=yourJobName
#SBATCH -constraints=largemem (only select nodes that fit certain constraints)
#SBATCH -o result.%J.out
#SBATCH --mail-type=ALL
#SBATCH --mail-user=yourEmailAddress
</code></pre>
<h2>Monitor your jobs</h2>
<ul>
<li>Use the sacct command you can see all your jobs (including completed) and you can customize how the job list is displayed by using the "fileds" option as below. Showing jobname is useful as it is by default the filename of corresponding job script. The 'state" option helps specify what types of jobs you would like to view, such as CA (CANCELLED), CD (COMPLETED), F (FAILED), PD (PENDING; i.e., awaiting resource allocation), R (RUNNING), and S (SUSPENDED; e.g., give way to jobs with higher priority).
<pre><code>sacct --state=RUNNING --fields=jobid,jobname,state</code></pre>
</li>
<li>Show job details: scontrol show job <jobID>. The "WorkDir" line tells you the path of the parent directory of your job script (i.e., from where the job was submitted). The "Command" line tells you the path of your job script file (including file name).</li>
<li>If the name of your job (by default the name of job script file) is too long to be seen. There are two solutions. One is to show only the job name using
<pre><code>scontrol show job | grep -e JobName -e 'username'</code></pre>
Please note that the quotes around "username" is necessary because JobName is a field contained in the output but username is a string you specify.
The other is to tweak the display format of squeue command. The default formats is "%.7i %.9P %.8j %.8u %.2t %.10M %.6D %R". "%[.][size]type" is used to indicate every output field and its display size. "i" stands for job id, "j" for job name. You can increase the number before j, which indicates the display size of that field, to leave more space for showing complete job name. An example command would be
<pre><code>squeue -u linly --format="%.7i %.9P %.20j %.8u %.2t %.10M %.6D %R"</code></pre>
Note that I keep all other default setting while changing the size of jobname field from the default value 8 to 20. It suits the size of my monitor screen but you may need a different value.
</li>
</ul>
<h2>Update jobs</h2>
The command below will change the wall time of the specified job to 1 day 12 hours
<pre><code>scontrol update JobID=JOB_ID Timelimit=1-12:00:00</code></pre>
<h2>Get an interactive shell use using sinteractive</h2>
<ul>
<li>On RENCI machines, you can use the -w flag to specify which node you want to land on. For example, if you need a lot of memory and thus a largemem computing node, you can do
<pre><code>sinteractive -w largemem-1-0</code></pre>
</li>
<li>(not on RENCI machines) If you pass the -e flag to sinteractive it will reserve an entire node, i.e., using
<pre><code>sinteractive -e</code></pre>
</li>
<li>If you don't know what arguments you can use along with "sinteractive", type
<pre><code>sinteractive --help</code></pre><br/>
On RENCI machines, I can also use "-c" to request the number of CPU cores</li>
<li>If you need more than just one core (but not all cores in a node), utilize the -c option to specify the number.</li>
<li>sinteractive --help will show you the available options.</li>
<li>Also note that on RENCI machines, although GUIs can be executed (e.g., when you want to run Eclipse in debug mode), they can't be done via the shell that sinteractive drops you in (i.e., by using sinteractive -X), as the cluster I'm working on has no additional plugins that would enable proper X11 forwarding inside of sinteractive. Instead you need to open a new shell by SSH -X to the node that was allocated to you via sinteractive. So a shortcut would be to first copy the address of the compute node allocated to you by the above command, i.e., all texts inside [], for example, username@ComputeNode-1-6. Then open a new ssh session and get onto the head node of the cluster first (for me it's ht0 or ht1) and then at the command line type: ssh -X <the address you just copied>, e.g., ssh -X linly@croatan-1-6. Enter. Then you can run Eclipse. In other words, you need to open another terminal on ht0 or ht1 after you have obtained an interactive session on a node with sinteractive. Next just ssh -X <node> to the node that was reserved for you with sinteractive. Then you can run eclipse on that node. You won't be able to run eclipse through the terminal that you obtained via sinteractive.</li>
<li>It is often preferrable to use the interactive mode (i.e., open an interactive shell) to diagnose a problematic job when you don't know the causes. First open an interactive shell. Then run the progrom you plan to run in your job script from the command line directly(end with a "&". That allows you to push it to the background and run other stuffs, such as doing diagnosis, on top of the program). Then run "top" command to see the resource usage of that program and determine whether you need more CPUs or memory or disk space, etc. Another useful command in this context is "ps". The process status command (ps) displays information about all currently active processes. If the program writes into an output file over time, you can use the following command to see data appended to the output file:
<pre><code>tail -f "name of the output file"</code></pre>
</li>
</ul>
<h2>Get more informations</h2>
You can use "sinfo" command to view information about Slurm nodes and partitions. It is similar to "showq" of PBS. For example, to view summary information of each partition (Partitions represent group of nodes with specific characteristics (similar resources, priority, job limits, access controls, etc)):
<pre><code>sinfo -s</code></pre>
All the queues have maximum durations and maximum amount of nodes that a job can use. You can check these limits with command
<pre><code>$ sinfo -l</code></pre>
If you want to know how much memory is installed in each compute node, the 7the column of the output of the following command will give you such information.
<pre><code>sinfo -N -l</code></pre>
Or specify the output of sinfo, such as below (the memory is in Mb)
<pre><code>sinfo -o "%15N %10c %10m %25f %10G"</code></pre>
If you want to know how much memory is already in use on each node, use the following command. It shows NodeName, RealMemory and AllocMem. (requires Slurm 2.6.0 or more recent)
<pre><code>$ scontrol -o show nodes | awk '{ print $1, $13, $14}'</code></pre>
Note: the memory size is in megabytes
The Unix 'man' command (e.g., man squeu) provides more detailed information on any command by bringing out the man page of that command. To search for a specific string in a man page. Type slash / and then type the string to search for. Then keep pressing the "n" bottom to get to the next item.

The command "sview" is available to inspect and modify jobs via a graphical user interface. To identify your jobs among the many ones in the list, select either the "specific user's jobs" or the "job ID" item from the menu "Actions -> Search". By right-clicking on a job of yours and selecting "Edit job" in the context menu, you can obtain a window which allows to modify the job settings. Please be careful about committing your changes.

<h2>References</h2>
<ol>
<li>A nice <a href="http://ecs.rutgers.edu/slurm_commands.html">cheatsheet</a> found by my colleague Ruhi Rai (She's gonna be a great bioinformaticist in the future)</li>
<li>A <a href="http://www.arc.ox.ac.uk/content/slurm-job-scheduler">tutorial</a> on how to write a shell script with SLURM commands</li>
<li><a href="https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/">https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/</a></li>
<li><a href="http://www.linuxnix.com/abslute-path-vs-relative-path-in-linuxunix/">Absloute path vs relative path in Linux/Unix</a></li>
</ol>
