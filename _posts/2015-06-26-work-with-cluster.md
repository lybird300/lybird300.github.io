---
layout: post
title: "Working with clusters"
date: 2015-06-22
---
<img src="https://cloud.githubusercontent.com/assets/5496192/9962833/d3a3a0c6-5df5-11e5-9106-dcd88f848996.jpg" width=30% height=30% /><br />
Disclaimer: The commands introduced in this post work on the clusters I'm using (PBS and SLURM), but may not on yours. (I learned that they are different when trying to kill hundreds of batch jobs I had submitted to the queue. The common command "bkill" did not work for me and I ended up using "qdel".) I also want to express my gratitude to my colleagues <a href="http://renci.org/staff/jeffrey-tilson/">Dr. Jeff Tilson</a> and <a href="http://renci.org/staff/marcin-sliwowski/">Marcin Sliwowski</a>. They helped me understand these very basic stuffs with great patience and clear instructions. Every time when they came into my office, grabbed a chair and sat beside me, I knew whatever problem I had would soon go away. Thank you guys! You are the best!
OK. Now let's roll! Happy computing everybody!
When working with clusters, no strategy is more important than "devide and conquer," in my opinion (Recently I'm reading Season 1 scripts of CBS' "the good wife" for fun). The first thing to consider is how to divide your tasks into several jobs and whether you can parallelize the program used to run each of these jobs (i.e., make it multi-threaded).
<h2>Parallelize your code</h2>
Tread, thread, thread...<br/>
<img src="https://cloud.githubusercontent.com/assets/5496192/8731041/b29a9e8a-2bc2-11e5-9d51-f83a51f87408.jpg"/><br/>
Check out <a href="http://www.omsn.de/blog/how-to-parallelize-loops-with-java-7-fork-join-framework">this article</a> that compares the application of Thread, ExecutorService, and ForkJoinPool in the same problem. Here is <a href="http://blog.takipi.com/forkjoin-framework-vs-parallel-streams-vs-executorservice-the-ultimate-benchmark/">another one</a> that includes Java 8 parallel streams.
Note: Your JVM and/or the host OS decide how many 'native' threads to use, and how those threads are mapped to physical processors. Thus, when submitting your jobs to the queue, make sure you grab as many processors as possible using ppn (e.g., qsub -q largemem -l nodes=1:ppn=32)

There are several thread states, A thread can be in any one of the state at a particular point of time. It can be running state. It can be ready to run state as soon as it gets CPU time. A running thread can be suspended. A suspended thread can be resumed. A thread can be blocked when waiting for a resource. At any time a thread can be terminated. When a parent thread P executes a thread join to join one of its child threads C, which is still running, P is suspended until C terminates. Once C terminates, P resumes.

<h2>Submit jobs with qsub</h2>
Arguments placed on the command line when calling the qsub command will take precedent over those in the script, so a general script may be built and then tested or varied by varying the options on the command line.
<pre><code>
-N SimpleScript                    #Give the job this name.
-M youremailhere@*.edu             #A single user, send notification emails there.
-V                                 #Pass the current environment variables to the job.
-l nodes=1:ppn=1                   #Request a single node, single core for this job. "ppn" stands for processors per node
-l walltime=96:00:00               #Request a maximum wall time of 96 hours (HH:MM:SS format).
-o output/$PBS_JOBNAME.out         #Redirect STDOUT to ./output/$PBS_JOBNAME.out
-e error/$PBS_JOBNAME.err          #Redirect STDERR to ./output/$PBS_JOBNAME.err
env                                     #Echo the environment (variables)
cd $PBS_O_WORKDIR                       #PBS starts your job in your home directory, cd to the submit/work directory
echo -n CWD:; /bin/pwd                  #Echo the current working directory path
echo PBS_JOBNAME is live…               #Print to STDOUT (really, the file declared above) the job is live…
sleep 30                                #Sleep for 30 seconds, then exit.
</code></pre>
Environment Variables Available to Job:
<pre><code>
PBS_O_WORKDIR – the absolute path of the current working directory of the qsub command. You must ‘cd’ to this directory if you want to work in the folder you submitted the job from.
PBS_JOBNAME – the job name supplied by the user.
PBS_O_HOST – the name of the host upon which the qsub command is running.
PBS_SERVER – the hostname of the pbs_server which qsub submits the job to.
PBS_O_QUEUE – the name of the original queue to which the job was submitted.
PBS_ARRAYID – each member of a job array is assigned a unique identifier (see -t)
PBS_ENVIRONMENT – set to PBS_BATCH to indicate the job is a batch job, or to PBS_INTERACTIVE to indicate the job is a PBS interactive job, see -I option.
PBS_JOBID – the job identifier assigned to the job by the batch system.
PBS_NODEFILE – the name of the file contain the list of nodes assigned to the job (for parallel and cluster systems). This file is particularly useful if you want to log in to remote machines for parallel debugging.
PBS_QUEUE – the name of the queue from which the job is executed.
PBS_JOBNAME – the job name supplied by the user.
</code></pre>
You can use these variables in your scripts as though they already exist in your environment; PBS sets them up as soon as your job starts running.

<h2>Debug using Eclipse (GUI) on a specific queue</h2>
First get an interactive PBS session using
<pre><code>qsub -q QUEUENAME -I -X</code></pre>
You can find out what "-q" "-I" and "-X" mean by typing
<pre><code>man qsub</code></pre>
Then launch Eclipse
If you forget "-X" you will probably get a command line message saying that "Eclipse: Cannot open display" and in the error log "org.eclipse.swt.SWTError: No more handles [gtk_init_check() failed]"
If for some reason Eclipse cannot locate JVM (Java Virtual Machine) in the current path (you may get a message saying "A java Runtime Environment (JRE) or Java Development kit (JDK) must be available in order to run Eclipse. No Java virtual machine was found after searching the following locations: /home/.../jre/bin/java in your current PATH"), take a look at <a href="http://stackoverflow.com/questions/2030434/eclipse-no-java-jre-jdk-no-virtual-machine">this post</a>.
A funny story is that I used to open another session (tab) in Mobex because once I started eclipse the command prompt was blocked and I couldn't do anything else on the same screen. Then Marcin, our Admin told me I can actually run command in the background by appending a & at the end of the command line. The & operator puts command in the background and free up your terminal. The command which runs in background is called a job. You can type other command while background command is running. The syntax is:
<pre><code>command & (e.g., eclipse &)</code></pre>

<h2>Give privileges to files and directories</h2>
To recursively give directories read&execute privileges:
<pre><code>find /path/to/base/dir -type d -exec chmod 755 {} +</code></pre>
To recursively give files read privileges:
<pre><code>find /path/to/base/dir -type f -exec chmod 644 {} +</code></pre>
<h2>Monitor job conditions</h2>
The qstat command provides the status of all jobs and queues in the cluster. Below are some useful options:
<pre><code><ul>
<li>qstat: Displays list of all jobs of the current user with no queue status information.</li>
<li>qstat -u USERNAME: Displays a list of jobs belonging to a specific user (identified by USERNAME)</li>
<li>qstat -u USERNAME -n: Displays all jobs of an user with node information (i.e., which node and which processor each job is running on)</li>
<li>qstat -u '*': Displays list of all jobs belonging to all users.</li>
<li>qstat -f: gives full information about jobs and queues.</li>
<li><b>qstat -j [job_id]: Gives the reason why the pending job (if any) is not being scheduled.</b></li>
<li>showq: displays information about active, eligible, blocked, and/or recently completed jobs.</li>
<li>ps: shows what processes (programs) you are running with your user id</li>
</ul></code></pre>
You can also find a good tutorial <a href="http://web.mit.edu/longjobs/www/status.html">here</a>.
Another command for checking a specific job is checkjob <jobID>

<h2>Terminate your queued jobs</h2>
To qdel multiple jobs at once, type:
<pre><code>qstat | grep (type-your-ID-here)</code></pre>
After getting the range of jobs you must delete (e.g., 7823111 and 7823118), type:
<pre><code>qdel `seq 7823111 7823118`</code></pre> or
<pre><code>qdel `seq -f "%.0f" 7823111 7823118`</code></pre>
If you see "qdel: Invalid request MSG=job cancel in progress", it probably means that the compute node is currently overloaded with concurrent jobs. The qdel will complete eventually, but may take some time, say, a few hours.
Kill all the jobs (both running and queued) of a specific user's
<pre><code>qselect -u username | xargs qdel</code></pre>
<pre><code>qstat | grep username | awk -F. '{print $1}' | xargs qdel</code></pre>
Delete all queued jobs only. Leaves all runnings jobs alone.
<pre><code>qstat -u username | grep ' Q ' | cut -d ' ' -f 1 | awk -F'.' '{print "qdel "$1}'</code></pre>
<pre><code>qstat -u username | awk -F. '/ Q   --/{printf $1" " }' | xargs qdel</code></pre>
<pre><code>qselect -u $USER -s Q | xargs qdel</code></pre>
Delete all jobs running by one user:
<pre><code>qselect -u $USER -s R | xargs qdel</code></pre>

<h2>Prevent performance degradation by controlling the number of jobs falling on a single node</h2>
Virtual Memory (VM) available = physical RAM + swap space (preconfigured space on the slower hard disk). Linux divides VM and physical RAM into chunks of memory, called pages. A procedure called swapping occurs when a page of memory is copied to swap space to free up the page of memory in RAM. Swapping is necessary because it provides more memory than that is physically available: the kernel is able to swap out less used pages from RAM and free memory for other immediate uses. Plus, lots of pages used by an application during initialization may not be used until much later. However, swapping has drawbacks. Disks are very slow compared to memory. If the application requires more memory than is physically available on either some or all of the nodes it has been running on, then excessive swapping, or thrashing, will happen. Generally, a page is swapped out and then very soon swapped in and then swapped out again, and so on. This is indicative of insufficient RAM for the workload. Therefore, if you have too many jobs landing on the same node and together they consume too many memories of that node, the node may be forced to use the swap space. Since that is hard disk I/O, job execution will dramatically slow down. Moreover, a PBS job will be killed when it uses >=60% of the swap space on one or more nodes.

If running all cores per node causes memory problems, you should request more nodes and run fewer cores per node. To prevent the system assign too many memory-intensitve jobs to the same node, you can tune your "qsub" command by increasing the number of cores you need for each job (e.g., nodes=1;ppn=4 instead of nodes=1;ppn=1), so that when you submit multiple memory intensive jobs, no more than 3 of them will land on a 12-core node and each of them will have sufficient memory to use.

<h2>Checking resources usage</h2>
Yesterday a weird thing happened: none of my jobs had any outputs, not even an error message. It turned out that that was because the folder I was writing into had reached its space limit, which is 70 TB!!! Thus, I think it would be useful to learn some Unix commands that can tell me about resource usage, for example, how much disk space or memory are being used, or how many CPU cycles are used by current running programs. So here comes a list. 
<pre><code><ul>
<li>qstat -q: lists the resource limits of each queue</li>
<li>ssh NODENAME: connect to a node specified by NODENAME, so that you can check its current usage information using, for example, free -m (display memory usage in MB; if you use free -g, it will display memory usage in GB)</li>
<li>du: prints the disk usage (in Kb) of each directory and it's sub-directories that you have execute permissions on. By default it starts from the current directory, but supplying  the name of a directory after the command will make it start from that directory.</li>
<li>df: tells you the amount of free space on all mounted file systems, or you can specify the name of a device you want to check. In the result table, Column 2 (labeled 1K-blocks) shows the total size (in Kb)  of the corresponding file system; Column 3 shows how much space has  been used; Column 4 gives the remaining space; Column five (Capacity)  shows (as a percentage) how much of the space has been used. The last column simply shows the mount folder names of that file system.</li>
<li>free: shows you information about the machine's memory. This includes  physical memory (RAM), swap as well as the shared memory and buffers used by the kernel. All measurements are in Kb.</li>
</ul></code></pre>
Please take a look at the last reference at the bottom of this page for how to use "du" and "df" to inspect a particular directory. The article introduces several commands, one of which I find very useful is
<pre><code>du -ch | grep total</code></pre>
Basically, "du" gives you a list of subdirectories that exist underneath the current directory along with their sizes. The last line of the output is the total size of the current directory. The option '-h' stands for human readable format. So the sizes of the files / directories are this time suffixed with a 'K' if its kilobytes and 'M' if its Megabytes and 'G' if its Gigabytes. This entire command would have only one line in its output that displays the total size of the current directory including all the subdirectories in terms of K, M, G, T, etc.
You can monitor the conditions of all jobs dynamically using top or htop (more colorful and expressive) and quit the monitoring window by typing "q". The output is full-screen and refreshes itself frequently (or at user definable intervals). There are several columns providing different information, such as:
<ul>
<li>VIRT: Total amount of virtual memory used by the process, including code, data, shared libraries, pages that swapped out</li>
<li>RES: Resident size (i.e., non-swapped physical memory a process has used) </li>
<li>%CPU: the CPU usage of a process. If it is more than 100%, e.g., 700%, it probably means that the job is actually using multiple CPUs on the node. In this situation, the process often runs very slowing because it is trashing the caches. The solution is to assign more cores (processors) to this job</li>
</ul>
<h2>References</h2>
<ul>
<li><a href="http://www.toptal.com/java/hunting-memory-leaks-in-java">Hunting Memory Leaks in Java</a></li>
<li><a href="https://hpcc.usc.edu/support/documentation/running-a-job-on-the-hpcc-cluster-using-pbs/">Running a Job on HPC using PBS</a> and <a href="https://hpcc.usc.edu/support/documentation/useful-pbs-commands/">Useful PBS Commands</a></li>
<li><a href="https://kb.iu.edu/d/admm">In Unix, what is the find command, and how do I use it to search through directories for files?</a></li>
<li><a href="http://www.codecoffee.com/tipsforlinux/articles/22.html">How to find size of a directory & Free disk space</a></li>
</ul>
