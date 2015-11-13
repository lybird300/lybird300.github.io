---
layout: post
title: "Fine tune CoJava"
date: 2015-11-13
---
For the fork/join framework, in practice it uses a thread pool in which a fixed number of threads are created. Each thread has a queue of tasks that are awaiting a chance to execute. When a task is started (forked), it is added to the queue of the thread that is executing its parent task.Because each thread can be executing only one task at a time, each thread's task queues can accumulate tasks which are not currently executing. Threads that have no tasks allocated to them will attempt to steal a task from a thread whose queue has at least one task - this is called work stealing. By this mechanism, tasks are distributed to all of the threads in the thread pool. By using a thread pool with work stealing, a fork/join framework can allow a relatively fine-grained division of the problem, but only create the minimum number of threads needed to fully exploit the available CPU cores. <b>Typically, the thread pool will have one thread per available CPU core</b>.<br/>
Each thread in a Java application has its own <a href="https://en.wikipedia.org/wiki/Stack_(abstract_data_type)">stack</a>. The stack is used to hold return addresses, function/method call arguments, etc. When a thread intends to process large structures via recursive algorithms, it may need a large stack for all those return addresses and such. With Sun/Oracle JVM, you can set thread stack size via the parameter -Xss when you launch a java application, e.g., java -jar ...jar <b>-Xss</b>2000M -Xmx15g. Exactly what value you should assign need to be tuned/tested. Increase -Xss value if your keep getting StackOverflowError, which means the stack size is greater than the setting. Decrease the value if you get "OutOfMemoryError: unable to create new native thread", which may happen when -Xss is set too large, i.e., there are too many threads, each of which has a large stack. Note that this error is different from "OutOfMemoryError: Java heap space". The stack space used is not allocated from the heap (indicated by <a href="http://stackoverflow.com/questions/14763079/what-are-the-xms-and-xmx-parameters-when-starting-jvms">-Xms and -Xmx switches</a>) though. In fact the memory used by your JVM is more than what -Xmx parameter specifies. JVM uses more memory than just the heap. For example Java methods, <b>thread stacks</b> and native handles are allocated in memory separate from the heap, as well as JVM internal data structures. The default thread stack size varies with JVM (generally larger for 64bit JVMs), OS and environment variables. A typical value is 512k. This means that if your app uses 150 threads, 75MB will be used for thread stacks. In some environments the defaults stack may be as large as 2MB. With a large number of threads, this can consume a significant amount of memory which could otherwise be used by your application or OS. The situation may become even more complicated when you are working on a cluster and need to decide how many nodes and cpus you should use. To monitor the memory use, you can log on to the cluster node (ssh username@computeNodeName) and then use "htop" command. So far I've only monitored the use of memory, swap (ideally 0), and CPU load. Here is a very good post on <a href="http://blog.scoutapp.com/articles/2009/07/31/understanding-load-averages">CPU load</a>.<br/>


<h2>References</h2>
<ul>
<li><a href="http://www.andrew.cmu.edu/user/ikaveror/15618/">FJava: Fork Join for Java</a></li>
</ul>