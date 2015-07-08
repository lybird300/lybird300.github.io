---
layout: post
title: "Work with the queue"
date: 2015-06-22
---
<h2>Submit jobs with qsub</h2>
Arguments placed on the command line when calling the qsub command will take precedent over those in the script, so a general script may be built and then tested or varied by varying the options on the command line.
<pre><code>
#PBS -N SimpleScript                    #Give the job this name.
#PBS -M youremailhere@*.edu             #A single user, send notification emails there.
#PBS -V                                 #Pass the current environment variables to the job.
#PBS -l nodes=1:ppn=1                   #Request a single node, single core for this job.
#PBS -l walltime=96:00:00               #Request a maximum wall time of 96 hours (HH:MM:SS format).
#PBS -o output/$PBS_JOBNAME.out         #Redirect STDOUT to ./output/$PBS_JOBNAME.out
#PBS -e error/$PBS_JOBNAME.err          #Redirect STDERR to ./output/$PBS_JOBNAME.err
env                                     #Echo the environment (variables)
cd $PBS_O_WORKDIR                       #PBS starts your job in your home directory, cd to the submit/work directory
echo -n CWD:; /bin/pwd                  #Echo the current working directory path
echo PBS_JOBNAME is live…               #Print to STDOUT (really, the file declared above) the job is live…
sleep 30                                #Sleep for 30 seconds, then exit.
</code></pre>
Environment Variables Available to Job:

You can use the following variables in your scripts as though they already exist in your environment; PBS sets them up as soon as your job starts running.

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

<h2>Debug using Eclipse (GUI) on a specific queue</h2>
First get an interactive PBS session using
<pre><code>qsub -q QUEUENAME -I -X</code></pre>
You can find out what "-q" "-I" and "-X" mean by typing
<pre><code>man qsub</code></pre>
Then launch Eclipse
If you forget "-X" you will probably get a command line message saying that "Eclipse: Cannot open display" and in the error log "org.eclipse.swt.SWTError: No more handles [gtk_init_check() failed]"
If for some reason Eclipse cannot locate JVM (Java Virtual Machine) in the current path (you may get a message saying "A java Runtime Environment (JRE) or Java Development kit (JDK) must be available in order to run Eclipse. No Java virtual machine was found after searching the following locations: /home/.../jre/bin/java in your current PATH"), take a look at <a href="http://stackoverflow.com/questions/2030434/eclipse-no-java-jre-jdk-no-virtual-machine">this post</a>.

<h2>Monitor with qstat</h2>
The qstat command provides the status of all jobs and queues in the cluster. The most useful options are:
<ul>
<li>qstat: Displays list of all jobs of the current user with no queue status information.</li>
<li>qstat -u hpc1***: Displays list of all jobs belonging to user hpc1***</li>
<li>qstat -u '*': Displays list of all jobs belonging to all users.</li>
<li>qstat -f: gives full information about jobs and queues.</li>
<li>qstat -j [job_id]: Gives the reason why the pending job (if any) is not being scheduled.</li>
</ul>
You can also find a good tutorial <a href="http://web.mit.edu/longjobs/www/status.html">here</a>.


qsub -I
top
logout
showq

<h2>References</h2>
<ul>
<li><a href="http://www.toptal.com/java/hunting-memory-leaks-in-java">Hunting Memory Leaks in Java</a></li>
<li><a href="https://hpcc.usc.edu/support/documentation/running-a-job-on-the-hpcc-cluster-using-pbs/">Running a Job on HPC using PBS</a> and <a href="https://hpcc.usc.edu/support/documentation/useful-pbs-commands/">Useful PBS Commands</a></li>
</ul>
