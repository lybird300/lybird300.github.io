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
<li>List multiple jobs in the queue that meet certain conditions (e.g., user xx's currently running on a specific node say "largemem-0-0"). You can use the "grep" command under the AND logic:
<pre><code>squeue | grep largemem-0-0 | grep xx</code></pre>
<li>List contents of a user's jobs in the queue: squeue -u <username></li>
<li>Remove a job from the queue by its ID: scancel JobID</li>
<li>Remove a seriel of jobs by jobID
The first command below removes jobs with consecutive IDs {783111, 783112,..., 783118}. The second command removes jobs with IDs 2 apart {783111, 783113, 783115,..., 783229}
<pre><code>scancel `seq 7823111 7823118`
scancel `seq 7823111 2 7823229`
</code></pre>
Usually the jobIDs of multiple jobs of yours are not consecutive (i.e., they may be intermediated by others' jobs), but you don't have to identify and then delete them one by one. Just provide a range that can cover all the jobs that you would like to cancel and use the aforementioned command. Only your jobs in this range will be cancelled (as long as you are not the admin), even though there will be a whole bunch of error messages -- "scancel: error: Kill job error on job id 13637530: Invalid job id specified".
</li>
<li>Remove one or more jobs by job name (allowed for wildcard?): scancel --name myJobName</li>
<li>Remove all jobs from a specific user, say linly
<pre><code>squeue | grep linly | awk '{print $1}' | xargs scancel</code></pre>
</li>
<li>Cancel all pending jobs belonging to a specific user: scancel --state=PENDING --user=<username></li>
<li>Cancel a specific process on the head node (not shown in squeue) using the commands below in turn
<pre><code>
ps -efl | grep -i 'linly' #first check the pid of all your jobs (at the fourth field of the displayed list)
kill -9 <pid> #for example, kill -9 27240
</code></pre>
</li>
</ul>

<h2>Submit a job</h2>
A typical command is
<pre><code>sbatch file.sh</code></pre>
Again there are many flags (i.e., options) that you can use to fit your special needs. For example,
<ul>
<li>-t: specify the walltime</li>
<li>-p: specify the queue you want to use</li>
<li>-N: specify the number of compute nodes you would like to reserve</li>
<li>-n: specify the number of tasks to be conducted on each compute node. n > 1 usually means that you are submitting a parallel job. There are several ways a parallel job can be created:
<ul>
<li>by running a multi-process program (SPMD paradigm, e.g. with MPI)</li>
<li>by running a multithreaded program (shared memory paradigm, e.g. with OpenMP or pthreads)
<li>by running several instances of a single-threaded program (so-called embarrassingly parallel paradigm)
<li>by running one master program controling several slave programs (master/slave paradigm)
</ul>
In the Slurm context, a task is to be understood as a process. So a multi-process program is made of several tasks. By contrast, a multithreaded program is composed of only one task, which uses several CPUs. Tasks are requested/created with the --ntasks option, while CPUs, for the multithreaded programs, are requested with the --cpus-per-task option. Tasks cannot be split across several compute nodes, so requesting several CPUs with the --cpus-per-task option will ensure all CPUs are allocated on the same compute node. By contrast, requesting the same amount of CPUs with the --ntasks option may lead to several CPUs being allocated on several, distinct compute nodes. <a href="https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/">This post</a> explains why "nodes" and "threads" are different concepts and their values are usually not the same in modern computing systems. </li>
<li>-x, --exclude=<node name list>: explicitly exclude certain nodes from the resources granted to the job. For example, you can submit a job from the command line and avoid the two largemem nodes by typing:
<pre><code>sbatch --exclude=largemem-0-0,largemem-1-0 myScript.sh</code></pre>
You can also include this option in your job script by adding a line at the top of myScript.sh:
<pre><code>#SBATCH --exlcude=largemem-0-0,largemem-1-0</code></pre>
</li>
<li>--exclusive (do NOT use this on RENCI machines): sometimes it is good to have the entire compute node reserved for your own use (i.e., not sharing with other people and this flag is for this purpose (note it starts with two slashes instead of one slash)</li>
<li>--mem=<MB>: specify the minimum amount real memory you require per node in MegaBytes</li>
<li>--mem-per-cpu=<MB></li>: To allow for more precise scheduling of resources, RENCI IT urges us to use this handle. If your job requires more than 2G of memory per CPU, you will need to specify the amount required via the –mem-per-cpu option. For example, if you need 10GB(i.e., 10*1024MB) for a serial job, then on top of the job script you should add the following (also see below for the difference between command line and job script):
<pre><code>
#SBATCH -N 1
#SBATCH -n 1
#SBATCH --mem-per-cpu=10240
</code></pre>
If your job exceeds the limit, it will be cancelled automatically by Slurm. You will find lines like the following in your Slurm error output file:
<pre><code>slurmstepd: Exceeded job memory limit
slurmstepd: *** JOB 23008 ON compute-0-0 CANCELLED AT 2015-12-03T10:43:56 ***</code></pre>
One way to determine how much memory your job will require per CPU is to use the top command. Identify your process and use the value in the "VIRT" column as a guideline for your target memory requirements. A new option has also been added to >b>sinteractive</b> to help profile your job after we implement this change. This is the –m <MEM_PER_CPU_IN_MB> option. If you find your job failing due to memory limits, use sinteractive with a generous value for –m and use top to help find your target requirement.
<li>Each institution tends to customize SLURM commands for their own needs, you can know what is allowed for a command by using the "--help" option and if you are only interested in a certain topic, use "grep". For example, if I want to know what options I have for regulationg the memory use when submitting a job, I can type the following at the command line:<br/>
<pre><code>
sbatch --help | grep mem
</code></pre>
</li>
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
<h2>Submit large numbers of jobs using job array</h2>
My script for running hundreds of CHAT replicate datasets was adapted from those in <a href="https://rc.fas.harvard.edu/resources/documentation/submitting-large-numbers-of-jobs-to-odyssey/">this tutorial</a>.
Basically, I created three files:
<ul>
<li>jobArrayList.txt: containing the working directory of all datasets, each per line. Its contents look like below:
<pre><code>
/projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/SNP_8308741_1.0E-4_0.001/Case100_Control100_GRR20.0_BDR0.3/ChatInput_tagSNP/Rep2
/projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/SNP_8308741_1.0E-4_0.001/Case100_Control100_GRR20.0_BDR0.3/ChatInput_tagSNP/Rep3
/projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/SNP_8308741_1.0E-4_0.001/Case100_Control100_GRR20.0_BDR0.3/ChatInput_tagSNP/Rep4
/projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/SNP_8308741_1.0E-4_0.001/Case100_Control100_GRR20.0_BDR0.3/ChatInput_tagSNP/Rep5
...</code></pre>
</li>
<li>preChatJobArrayRun.sh: it reads in the working directories from the above file, puts them into a bash array, and exports the name of the array as an environment variable ${DIRS} so that the array is visible to the shell script below. This file has the following cotents:
<pre><code>
#!/bin/bash
# read a list of working directories stored in jobArrayList.ext into a bash array
readarray DIRS < ./jobArrayList.txt
export DIRS
# get size of array
NUMFASTQ=${#DIRS[@]}
# now subtract 1 as we have to use zero-based indexing (first cell is 0)
ZBNUMFASTQ=$(($NUMFASTQ - 1))
# now submit to SLURM
if [ $ZBNUMFASTQ -ge 0 ]; then
sbatch --array=0-$ZBNUMFASTQ chatJobArraySubmit.sh
fi
</code></pre>
</li>
<li>chatJobArraySubmit.sh: it loops through the array created above, navigates to corresponding working directories and launch chat.jar. This file has the following cotents. Note that ${SLURM_ARRAY_TASK_ID} is a specific shell variable that is set when the job runs, and it is substituted into the parameter to generate the proper filename which is passed on
<pre><code>
#!/bin/bash
#SBATCH -J chat                    # A single job name for the array
#SBATCH -n 1                       # Number of cores
#SBATCH -N 1                       # All cores on one machine
#SBATCH --mem-per-cpu=10240        # Memory request (10Gb)
#SBATCH -o chat_%A_%a.out        # Standard output
#SBATCH -e chat_%A_%a.err        # Standard error
#module load java/jdk1.8.0_05
cd ${DIRS[$SLURM_ARRAY_TASK_ID]}
java -Xmx6g -cp ~/bin/chat.jar org.renci.chat.Main /projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/CHATResources/CHAT_prep.xml
java -Xmx6g -cp ~/bin/chat.jar org.renci.chat.Main /projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/CHATResources/CHAT.xml
</code></pre></li>
</ul>
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
<li>You can count how many of your jobs (with or without certain patterns) are currently running by using:
<pre><code>
squeue -u yourUserID | wc -l
squeue -u yourUserID | grep certainStringPattern | wc -l
</code></pre>
You can also list 5 most recently submitted jobs that are running or pending using
<pre><code>squeue -u userID | head -n 5</code></pre>
To find 5 most recently submitted jobs that failed, use
<pre><code>sacct -u userID | grep FAIL | tail -n 5</code></pre>
</li>
</ul>
The following script will show you in one line how many of your jobs are running, paused, idle, and finished. I "stole" it from Kirk, so credits all go to him.
<pre><code>
RUNNING=`squeue | grep linly | grep "R $1"| wc -l`
SUSPENDED=`squeue |grep linly | grep "S $1"| wc -l`
PENDING=`squeue | grep linly | grep "PD $2" | wc -l`
COMPLETED=`sacct -u linly | grep "COMPLETE $1" | wc -l`
FAILED=`sacct -u linly | grep "FAIL $1" | wc -l`
echo "$RUNNING running, $SUSPENDED suspended, $PENDING pending, $COMPLETED completed, $FAILED failed"
</code></pre>
<h2>Update jobs</h2>
The command below will change the wall time of the specified job to 1 day 12 hours
<pre><code>scontrol update JobID=JOB_ID Timelimit=1-12:00:00</code></pre>
Before you do this, you can use the following command to see details about a particular job. As long as you did not specify a walltime in your submit script, you should get the default, and the following command allows you to check the default.
<pre><code>scontrol show job jobID</code></pre>
The output will be something like below
<pre><code>
JobId=13441734 JobName=interactive
UserId=*** GroupId=external(2000)
Priority=5052 Nice=0 Account=(null) QOS=normal
JobState=RUNNING Reason=None Dependency=(null)
Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
RunTime=1-17:55:10 <b>TimeLimit=14-00:00:00</b> TimeMin=N/A
SubmitTime=2015-11-16T14:08:18 EligibleTime=2015-11-16T14:08:18
StartTime=2015-11-16T14:08:18 EndTime=2015-11-30T14:08:18
PreemptTime=None SuspendTime=None SecsPreSuspend=0
Partition=batch AllocNode:Sid=ht0:6529
ReqNodeList=largemem-1-0 ExcNodeList=(null)
NodeList=largemem-1-0
BatchHost=largemem-1-0
NumNodes=1 NumCPUs=40 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
MinCPUsNode=1 MinMemoryCPU=2G MinTmpDiskNode=0
Features=(null) Gres=(null) Reservation=(null)
Shared=OK Contiguous=0 Licenses=(null) Network=(null)
Command=(null)
WorkDir=/../..
</code></pre>
<h2>Get an interactive shell use using sinteractive</h2>
<ul>
<li>If you don't know what arguments you can use along with "sinteractive", type
<pre><code>sinteractive --help</code></pre><br/>
On RENCI machines, I can also use the -c flag to request the number of CPU cores and the -w flag to specify which node I want to land on. For example, if you need a lot of memory and thus would like to require a largemem computing node exclusively, you can do
<pre><code>sinteractive -c 40 -w largemem-1-0</code></pre>
</li>
<li>(not on RENCI machines) If you pass the -e flag to sinteractive it will reserve an entire node, i.e., using
<pre><code>sinteractive -e</code></pre>
</li>

<li>If you need more than just one core (but not all cores in a node), utilize the -c option to specify the number.</li>
<li>sinteractive --help will show you the available options.</li>
<li>Also note that on RENCI machines, although GUIs can be executed (e.g., when you want to run Eclipse in debug mode), they can't be done via the shell that sinteractive drops you in (i.e., by using sinteractive -X), as the cluster I'm working on has no additional plugins that would enable proper X11 forwarding inside of sinteractive. Instead you need to open a new shell by SSH -X to the node that was allocated to you via sinteractive. So a shortcut would be to first copy the address of the compute node allocated to you by the above command, i.e., all texts inside [], for example, username@ComputeNode-1-6. Then open a new ssh session and get onto the head node of the cluster first (for me it's ht0 or ht1) and then at the command line type: ssh <the address you just copied> -X, e.g., ssh linly@croatan-1-6 -X. Enter. Then you can run Eclipse. In other words, you need to open another terminal on ht0 or ht1 after you have obtained an interactive session on a node with sinteractive. Next just "ssh <node> -X" to the node that was reserved for you with sinteractive. Then you can run eclipse on that node. You won't be able to run eclipse through the terminal that you obtained via sinteractive.</li>
<li>It is often preferrable to use the interactive mode (i.e., open an interactive shell) to diagnose a problematic job when you don't know the causes. First open an interactive shell. Then run the progrom you plan to run in your job script from the command line directly(end with a "&". That allows you to push it to the background and run other stuffs, such as doing diagnosis, on top of the program). Then run "top" command to see the resource usage of that program and determine whether you need more CPUs or memory or disk space, etc. Another useful command in this context is "ps". The process status command (ps) displays information about all currently active processes. If the program writes into an output file over time, you can use the following command to see data appended to the output file:
<pre><code>tail -f "name of the output file"</code></pre>
</li>
</ul>
<h2>Get informations on resources</h2>
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

Sometimes you may get an error message saying that file output failed (i.e., cannot find the file) because "No space left on device". It pays to doublecheck the space that is left, with a df command. It turns out that you can get the same error message if there are not enough inodes. IF that is the case, take a look at <a href="https://www.ivankuznetsov.com/2010/02/no-space-left-on-device-running-out-of-inodes.html">this post</a> for how to diagnose and fix it. If you are a cluster user, you need help from your IT people.


<h2>Work with directory</h2>
Check last modified time of a file
<pre><code>stat filename</code></pre>
In the output, the "Access" line shows the time of last data access. "Modify" shows the time of last data modification. "Change" shows the time the file status last changed. If you want just the last modified date (in human-readable form), use
<pre><code>stat -c '%y' file</code></pre>

<h2>Job dependencies</h2>
Currently when I tested the latest version of CHAT (V3) on multiple simulated datasets in parallele, there is a high rate of job failure and we suspected that it was because the cluster cannot handle such high demands of threads. As a solution, we decided to run ChatV3 on each replicate set sequentially, meaning a new master job would not be launched until the current one has stopped. And that requires using slurm job dependencies.

Job dependencies are used to defer the start of a job until the specified dependencies have been satisfied. They are specified with the --dependency option to sbatch in the format
<pre><code>sbatch --dependency=<type:job_id[:job_id][,type:job_id[:job_id]]> ...</code></pre>
Dependency types:
after:jobid[:jobid...]     job can begin after the specified jobs have started
afterany:jobid[:jobid...]     job can begin after the specified jobs have terminated
afternotok:jobid[:jobid...]     job can begin after the specified jobs have failed
afterok:jobid[:jobid...]     job can begin after the specified jobs have run to completion with an exit code of zero. When the previous job ends with an exit code of zero, the next job will become eligible for scheduling. However, if the previous job fails (ends with a non-zero exit code), the next job will not be scheduled but will remain in the queue and needs to be canceled manually.
singleton     jobs can begin execution after all previously launched jobs with <b>the same name and user</b> have ended. This is useful to collate results of a swarm or to send a notification at the end of a swarm.
Below is an exemplary bash script that builds pipelines using slurm job dependencies that I borrowed from <a href="https://hpc.nih.gov/docs/job_dependencies.html">this webpage</a>.
<pre><code>
#! /bin/bash

# first job - no dependencies
jid1=$(sbatch  --mem=12g --cpus-per-task=4 job1.sh)

# multiple jobs can depend on a single job
jid2=$(sbatch  --dependency=afterany:$jid1 --mem=20g job2.sh)
jid3=$(sbatch  --dependency=afterany:$jid1 --mem=20g job3.sh)

# a single job can depend on multiple jobs
jid4=$(sbatch  --dependency=afterany:$jid2:$jid3 job4.sh)

# swarm can use dependencies
jid5=$(swarm --dependency=afterany:$jid4 -t 4 -g 4 -f job5.sh)

# a single job can depend on an array job
# it will start executing when all arrayjobs have finished
jid6=$(sbatch --dependency=afterany:$jid5 job6.sh)

# a single job can depend on all jobs by the same user with the same name
jid7=$(sbatch --dependency=afterany:$jid6 --job-name=dtest job7.sh)
jid8=$(sbatch --dependency=afterany:$jid6 --job-name=dtest job8.sh)
sbatch --dependency=singleton --job-name=dtest job9.sh

# show dependencies in squeue output:
squeue -u $USER -o "%.8A %.4C %.10m %.20E"
</code></pre>

Eventually I used "singleton" to control chat jobs.

<h2>References</h2>
<ol>
<li>A nice <a href="http://ecs.rutgers.edu/slurm_commands.html">cheatsheet</a> found by my colleague Ruhi Rai (She's gonna be a great bioinformaticist in the future)</li>
<li>A <a href="http://www.arc.ox.ac.uk/content/slurm-job-scheduler">tutorial</a> on how to write a shell script with SLURM commands</li>
<li><a href="https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/">https://amigotechnotes.wordpress.com/2014/02/17/how-multi-core-processors-accelerate-your-lamp-applications/</a></li>
<li><a href="http://www.linuxnix.com/abslute-path-vs-relative-path-in-linuxunix/">Absloute path vs relative path in Linux/Unix</a></li>
<li><a href="https://hpc.nih.gov/docs/job_dependencies.html">Building pipelines using slurm dependencies</a></li>
</ol>
