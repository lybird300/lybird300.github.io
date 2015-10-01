---
layout: post
title: "Working with clusters--SLURM version"
date: 2015-10-01
---
<h2>Useful SLURM commands</h2>
SLURM commands are different than previous PBS commands. Below are a few commonly used commands.
<ul>
<li>List contents of the queue: squeue</li>
<li>List contents of a user's jobs in the queue: squeue -u <username></li>
<li>Remove a job from the queue: scancel "id"</li>
<li>Remove all jobs from a specific user: scancel -u <username></li>
<li>Cancel all pending jobs belonging to a specific user: scancel --state=PENDING --user=<username></li>
<li>Submit a job using sbatch file.sh
Again there are many flags (i.e., options) that you can use to fit your special needs. For example,
<ul>
<li>-t: specify the walltime</li>
<li>-p: specify the queue you want to use</li>
<li>-N: specify the number of compute nodes you would like to reserve</li>
<li>-n: specify the number of cores on each compute node that you would like to reserve</li>
<li>--exclusive: sometimes it is good to have the entire compute node reserved for your own use (i.e., not sharing with other people and this flag is for this purpose (note it starts with two slashes instead of one slash)</li>
<li>-mem=<MB>: specify the real memory required per node in MegaBytes</li>
</ul>
</li>
<li>Monitor your jobs
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
</li>
<li>Get an interactive shell use: sinteractive 
<ul>
<li>If you pass the -e flag to sinteractive it will reserve an entire node, i.e., using
<pre><code>sinteractive -e</code></pre>
</li>
<li>If you need more than just one core (but not all cores in a node), utilize the -c option to specify the number.</li>
<li>sinteractive --help will show you the available options.</li>
<li>Also note that GUIs can be executed (e.g., when you want to run Eclipse in debug mode), but they can't be done via the shell that sinteractive drops you in (i.e., by using sinteractive -X), as the cluster I'm working on has no additional plugins that would enable proper X11 forwarding inside of sinteractive. Instead you need to open a new shell by SSH -X to the node that was allocated to you via sinteractive. So a shortcut would be to first copy the address of the compute node allocated to you by the above command, i.e., all texts inside [], for example, username@ComputeNode-1-6. Then open a new ssh session and at the command line type: ssh -X <the address you just copied>, e.g., ssh -X linly@croatan-1-6. Enter. Then you can run Eclipse. In other words, you need to open another terminal on ht0 or ht1 after you have obtained an interactive session on a node with sinteractive. Next just ssh -X <node> to the node that was reserved for you with sinteractive. Then you can run eclipse on that node. You won't be able to run eclipse through the terminal that you obtained via sinteractive.</li>
</ul>
</li>
<li>Job arrays offer a mechanism for submitting and managing collections of similar jobs quickly and easily; job arrays with millions of tasks can be submitted in milliseconds (subject to configured size limits). All jobs must have the same initial options (e.g. size, time limit, etc.). Job arrays are only supported for batch jobs and the array index values are specified using the --array or -a option of the sbatch command. A maximum number of simultaneously running tasks from the job array may be specified using a "%" separator. For example "--array=0-15%4" will limit the number of simultaneously running tasks from this job array to 4.
<pre><code># Submit a job array with index values between 0 and 31
$ sbatch --array=0-31
# Submit a job array with index values of 1, 3, 5 and 7
$ sbatch --array=1,3,5,7
# Submit a job array with index values between 1 and 7 with a step size of 2 (i.e. 1, 3, 5 and 7)
$ sbatch --array=1-7:2</code></pre>
</li>
<li>You can use "sinfo" command to view information about Slurm nodes and partitions. It is similar to "showq" of PBS. For example, to view summary information of each partition (Partitions represent group of nodes with specific characteristics (similar resources, priority, job limits, access controls, etc)):
<pre><code>sinfo -s</code></pre>
All the queues have maximum durations and maximum amount of nodes that a job can use. You can check these limits with command
<pre><code>sinfo -l</code></pre>
</li>
<li>The Unix 'man' command (e.g., man squeu) provides more detailed information on any command by bringing out the man page of that command. To search for a specific string in a man page. Type slash / and then type the string to search for. Then keep pressing the "n" bottom to get to the next item.</li>
<li>Lastly, the command "sview" is available to inspect and modify jobs via a graphical user interface. To identify your jobs among the many ones in the list, select either the "specific user's jobs" or the "job ID" item from the menu "Actions -> Search". By right-clicking on a job of yours and selecting "Edit job" in the context menu, you can obtain a window which allows to modify the job settings. Please be careful about committing your changes.</li>
</ul>
