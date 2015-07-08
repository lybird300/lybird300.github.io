---
layout: post
title: "Work with the queue"
date: 2015-06-22
---
<h2>Submit jobs with qsub</h2>
Arguments placed on the command line when calling the qsub command will take precedent over those in the script, so a general script may be built and then tested or varied by varying the options on the command line.
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
