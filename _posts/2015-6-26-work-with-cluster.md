---
layout: post
title: "Work with the queue"
date: 2015-06-22
---

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

<h2>References</h2>
<ul>
<li><a href="http://www.toptal.com/java/hunting-memory-leaks-in-java">Hunting Memory Leaks in Java</a></li>
<li><a href="https://hpcc.usc.edu/support/documentation/running-a-job-on-the-hpcc-cluster-using-pbs/">Running a Job on HPC using PBS</a> and <a href="https://hpcc.usc.edu/support/documentation/useful-pbs-commands/">Useful PBS Commands</a></li>
</ul>
