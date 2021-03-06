---
layout: post
title: "Fine tune Java programs"
date: 2015-11-13
---
<blockquote>There is simply no substitute for the experience of writing and tuning your own parallel programs.</blockquote>
Well, as you will very soon find out, the stuffs presented here are a little unorganized. My apology! For one thing, I've been dumping things that I thought useful over the time here. Hopefully in the future I will find some time to sort them out. For another, I feel the experience/knowledge I gained related to the contents presented here has come into the spectrum of "tacit knowledge", which is well known for being hard to explain (imagine teaching people how to swim or ride a bike). However, I did come across <a href="http://java-performance.info/">a pretty organized blog on the same matter</a>, in case you are interested.
<h2>Multi-thread and parallele programming</h2>
<img src="https://cloud.githubusercontent.com/assets/5496192/11194008/b9e11154-8c77-11e5-89ec-2d56b974c2d1.gif" /><br/>
Since CoJava applies Java fork-join parallelism framework, this post is mostly concerned with efficiently parallelizing fork-join computations in Java. The fork/join framework uses a thread pool in which a fixed number of threads are created. Each thread has a queue of tasks that are awaiting a chance to execute. When a task is started (forked), it is added to the queue of the thread that is executing its parent task. Because each thread can be executing only one task at a time, each thread's task queues can accumulate tasks which are not currently executing. Threads that have no tasks allocated to them will attempt to steal a task from a thread whose queue has at least one task - this is called work stealing. By this mechanism, tasks are distributed to all of the threads in the thread pool. By using a thread pool with work stealing, a fork/join framework can allow a relatively fine-grained division of the problem, but only create the minimum number of threads needed to fully exploit the available CPU cores. <b>Typically, the thread pool will have one thread per available CPU core</b>. 
There are some rules of thumb regarding general parallel programming (credit goes to the <a href="http://15418.courses.cs.cmu.edu/spring2015/home">course website of CMU 15-418/618</a>)
<ul>
<li>Want at least as much work as parallel execution capability (e.g., program should probably spawn at least was much work as there are cores)</li>
<li>Want more independent work than execution capability to allow for good workload balance of all the work onto the cores. “parallel slack” = ratio of independent work to machine’s parallel execution capability (in practice: ~8 is a good ratio)
<li>But not too much independent work so that granularity of work is too small (too much slack incurs overhead of managing fne-grained work)</li>
</ul>
The amount of work generated by a fork/join algorithm depends on a sequential cutoff, which is a certain condition (e.g., the size of an array to determine its maximum value is of size 1000) once the algorithm has reached it will switch from parallel processing to sequential processing (i.e., checking arrat elements one by one). When running time and resource intensive algorithms, it becomes important to fine tune the threshold against the system on which the algorithm is running and to determine the number of resources (e.g., CPU cores, memory size) that you should request and allocate during the process. Byt how? Let's first take a closer look at the fork-join framework.<br/>

<h3>Big data structure</h3>
fastUtil library
Code Example: http://marxsoftware.blogspot.com/2016/01/fastutil-java-collections.html


<h3>More about Fork-Join</h3>
Fork-Join is usually used to handle recursive tasks. It was first introduced in the Java 7 and since then served its purpose well, probably because many large tasks in their nature can be represented as recursive. The central piece in the ForkJoin framework is a ForkJoinPool, which is an ExecutorService and thus can accept asynchronous tasks and return a Future object. With the Future object, you can keep track of the status of the computation. What makes ForkJoinPool different from other ExecutorServices is that its thread worker keeps checking out and "stealing" work from each other. Work stealing is a way to manage workload in a decentralized fashion with an attempt to improve efficiency.

<h2>Memory handling in Java</h2>
Java handles its memory in the heap and the stack. In the heap the Java Virtual Machine (JVM) stores all objects created by the Java application. The memory for new objects is allocated on the heap at run time. Instance variables live inside the object in which they are declared. Once your application have no reference anymore to an object (i.e., no other object refers to this object), the Java garbage collector of the JVM is allowed to delete this object and release the memory so that your application can use this memory again. If other objects still hold references to this object, then the garbage collector cannot release the memory. A memory leak occurs when object references that are no longer needed are unnecessarily maintained. <a href="https://en.wikipedia.org/wiki/Stack_(abstract_data_type)">Stack</a> is where the method invocations and the local variables are stored. If a method is called then its stack frame is put onto the top of the call stack. The stack frame holds the state of the method including which line of code is executing and the values of all local variables. The method at the top of the stack is always the current running method for that stack.<br/>
Each thread in a Java application has its own stack, which is used to hold return addresses, function/method call arguments, etc. When a thread intends to process large structures via recursive algorithms, it may need a large stack for all those return addresses and such. With Sun/Oracle JVM, you can set thread stack size via the parameter -Xss when you launch a java application, e.g., java -jar ...jar <b>-Xss</b>2000M -Xmx15g. Exactly what value you should assign need to be tuned/tested. Increase -Xss value if your keep getting StackOverflowError, which means the stack size is greater than the setting. Decrease the value if you get "OutOfMemoryError: unable to create new native thread", which may happen when -Xss is set too large, i.e., there are too many threads, each of which has a large stack. Note that this error is different from "OutOfMemoryError: Java heap space". The stack space used is not allocated from the heap (indicated by <a href="http://stackoverflow.com/questions/14763079/what-are-the-xms-and-xmx-parameters-when-starting-jvms">-Xms and -Xmx switches</a>) though. In fact the memory used by your JVM is more than what -Xmx parameter specifies. JVM uses more memory than just the heap. For example Java methods, <b>thread stacks</b> and native handles are allocated in memory separate from the heap, as well as JVM internal data structures. The default thread stack size varies with JVM (generally larger for 64bit JVMs), OS and environment variables. You can check your JVM using the command
<pre><code>java -XX:+PrintFlagsFinal -version | grep ThreadStackSize
Or java -XX:+PrintFlagsFinal -version | grep -iE 'HeapSize|ThreadStackSize'</code></pre>
Suppose the value is 512k, which means that if your app uses 150 threads, 75MB will be used for thread stacks. In some environments the defaults stack may be as large as 2MB. With a large number of threads, this can consume a significant amount of memory which could otherwise be used by your application or OS. The situation may become even more complicated when you are working on a cluster and need to decide how many nodes and cpus you should request. To monitor the resource usage of your program, you can log on to the cluster node (ssh username@computeNodeName) and then use "htop" command. So far I've only monitored the use of memory, swap (ideally 0), and <a href="http://blog.scoutapp.com/articles/2009/07/31/understanding-load-averages">CPU load</a>.<br/>
You can use <a href="http://docs.oracle.com/javase/6/docs/api/java/lang/Runtime.html">the Runtime class</a> to monitor memory usage inside a java program. This class provides some useful API functions:
<ul>
<li>totalMemory()</li>: Returns the total amount of memory in the Java virtual machine. The value returned by this method may vary over time, depending on the host environment. Note that the amount of memory required to hold an object of any given type may be implementation-dependent.
<li>maxMemory()</li>: Returns the maximum amount of memory that the Java virtual machine will attempt to use (equal to the -Xmx value if you set it up). If there is no inherent limit then the value Long.MAX_VALUE will be returned. <b></b>
<li>freeMemory()</li>: Returns the amount of free memory in the Java Virtual Machine. Calling the gc method (garbage collection) may result in increasing the value returned by freeMemory. 
</ul>
Lets say you start your Java process as such:
<pre><code>java -Xms64m -Xmx1024m -jar chat.jar</code></pre>
Your process starts with 64mb of memory, and if and when it needs more (up to 1024m), it will allocate memory.  totalMemory() corresponds to the amount of memory currently allocated (and thus available) to the JVM for chat.jar. If the JVM needs more memory, it will lazily allocate it up to the maximum memory. If you run with -Xms1024m -Xmx1024m, the value you get from totalMemory() and maxMemory() will be equal.
Note that freeMemory() returns currently allocated free memory, i.e., currently allocated space ready for new objects. It is NOT the total available free memory. To calculate the latter, you first calculate used memory = Runtime.getRuntime().totalMemory() - Runtime.getRuntime().freeMemory(). Then totalfree memory = Runtime.getRuntime().maxMemory() - usedMemory;

<h2>Use jconsole</h2>
jconsole has been installed in RENCI cluster, so I can launch it simply by typing "jconsole" at the command line, which opens its GUI. If you have launched the java application (using java -jar #.jar) with sbatch command, you need to choose "Remote Process" and input the hostname of the machine, the port of the application, and probably your credentials (i.e., username and password). To get the hostname of the machine, first do a "squeue" or "scontrol show job" to see which machine the application is running on. Suppose it is "croatan-0-1", you need to log onto the machine using "ssh username@machineName" and then at the command line run "hostname". The port number was specified when launching the application, as part of the Java command. In order to get jConsole connected to a JVM, you must start the JVM enabling JMX by specifying the following options within the Java command (see the port number?) It seems that the last option is not required but may be necessary for Ubuntu: without it sometimes an java IOException may occur.
<pre><code>
-Dcom.sun.management.jmxremote.port=7009
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.local.only=false
</code></pre>
Below is an example of including the above options in java launching command
<pre><code>
java -Dcom.sun.management.jmxremote \
 -Dcom.sun.management.jmxremote.port=7009 \
 -Dcom.sun.management.jmxremote.ssl=false \
 -Dcom.sun.management.jmxremote.authenticate=false \
 -Dcom.sun.management.jmxremote.local.only=false \
 -Xmx6g -jar /home/linly/bin/chatV3_best.jar /projects/sequence_analysis/vol4/CHAT_simGWAS/newSimGWASData/CHATResources/CHAT_prep.xml
</code></pre>

<h2>Run Java-program-based jobs on HPC</h2>
One of the senior members in our group, Dr. Chris Bizon, is helping me optimize CHAT. He told me for every job that I submit to the cluster, I should aim for hour-ish wall time. Otherwise, with so many CHAT jobs (the order of millions) finishing so quickly (in minutes), the cluster scheduler is hustling to keep up. I think a "syndrom" of this problem would be when you use "squeue" to check for job status, you either get a "time-out" error or see many jobs with a "CG" condition (which means "COMPLETING").

An HPC machine is shared by multiple users and usually has many cores, which ironically can sometimes cause JVM to fail. As a result, you get an error message typically saying "there is insufficient memory for the Java Runtime Environment to continue. Cannot create GC thread. Out of system resources" and the system dumps a hs_err_pid#.log file to where your shell script is. The ironical part is that it may actually be because the system has too many resources...See <a href="https://confluence.csiro.au/pages/viewpage.action?pageId=278167841">this post</a>. Now suppose you have a similar problem and would like to decrease the number of parallele gc threads. What next? In other words, how do you figure out the appropriate number of GC threads? Take a look at <a href="https://blog.codecentric.de/en/2013/01/useful-jvm-flags-part-6-throughput-collector/">this post</a>

With -XX:ParallelGCThreads=<value> we can specify the number of GC threads to use for parallel GC. For example, with -XX:ParallelGCThreads=6 each parallel GC will be executed with six threads. If we don’t explicitly set this flag, the JVM will use a default value which is computed based on the number of available (virtual) processors. The determining factor is the value N returned by the Java method Runtime.availableProcessors(). For N <= 8 parallel GC will use just as many, i.e., N GC threads. For N > 8 available processors, the number of GC threads will be computed as 3+5N/8.

Using the default setting makes most sense when the JVM uses the system and its processors exclusively. However, if more than one JVM (or other CPU-hungry systems) are all running on the same machine, we should use -XX:ParallelGCThreads in order to reduce the number of GC threads to an adequate value. For example, if four server JVMs are running on a machine with 16 processor cores, then -XX:ParallelGCThreads=4 is a sensible choice so that GCs of different JVMs don’t interfere with each other.

I also got the following error messages  for some job failure:
<pre><code>
Java HotSpot(TM) 64-Bit Server VM warning: Insufficient space for shared memory file:
   /tmp/hsperfdata_linly/22248
Try using the -Djava.io.tmpdir= option to select an alternate temp location.
</code></pre>
The file /tmp/hsperfdata_linly/22248 was created by JVM by default to export its statistics. It keeps creating /tmp/hsperfdata_username directories. There are cases that the JVM statistics caused garbage collection pauses because the JVM modifies its statistics during garbage collection and safepoints by memory mapping to  /tmp/hsperfdata_username, which could be blocked for hundreds of milliseconds until disk I/O completes. For details, take a look at this <a href="http://www.evanjones.ca/jvm-mmap-pause.html">article</a>. Based on this <a href="http://stackoverflow.com/questions/76327/how-can-i-prevent-java-from-creating-hsperfdata-files">post</a>, we can use "-XX:+PerfDisableSharedMem=false" to prevent JVM from creating the statistics and thus suppresses the creation of the hsperfdata_userid directories. This option is recommended by Sun for causing less performance issues than turning off "-XX:-UsePerfData" option. 
 
 <h2>Integrate other languages into Java program</h2>
 <h3>Set up java.library.path in Eclipse IDE</h3>
 Take a look at <a href="http://javarevisited.blogspot.com/2013/04/what-is-javalibrarypath-how-to-set-in-Eclipse.html">this post</a>
 
 <h3>With R</h3>
 JRI: if you are compiling your java program using Maven, you must see <a href="http://stackoverflow.com/questions/32170664/running-jar-built-using-maven-causes-java-lang-noclassdeffounderror-org-rosuda?noredirect=1&lq=1">this post</a>. I copied the contents below in case the post gets lost in the future.
 
Strictly speaking both R and rJava need to be installed on the machine in order to resolve the "Cannot find JRI native library!" issue when instantiating your org.rosuda.REngine.REngine object, and this cannot be done exclusively by way of adding the JRIEngine dependency in your pom.xml.

Steps:
<ol>
<li>Install R</li>
<li>Install rJava with R</li>
<pre><code>install.packages("rJava")</code></pre>
<li>Add rJava/jri to java.library.path classpath
<pre><code>
-Djava.library.path="/usr/local/lib/R/3.3/site-library/rJava/jri/"
 </code></pre>
 </li>
<li>Add R_HOME to environment variables (where you installed R). Note that if you're trying to run this in your IDE, it won't inherit the path you set in ~/.bash_profile, you need to set it in your run configuration.
In Eclipse, select the run or debug configuration of your project. Click the Environment tab. Add new environment variable such as
R_HOME=/usr/local/Cellar/r/3.3.1_2/
</li>
<li>Ensure maven has dependency for JRIEngine in pom.xml
<pre><code>
<dependency>
    <groupId>com.github.lucarosellini.rJava</groupId>
    <artifactId>JRIEngine</artifactId>
    <version>0.9-7</version>
</dependency>
</code></pre>
<li>Instantiate REngine (I need this version in order to pass dataframe to R from java)
<pre><code>
String[] Args = {"--vanilla"};
REngine engine = REngine.engineForClass("org.rosuda.REngine.JRI.JRIEngine", Args, new REngineStdOutput (), false);
</code></pre>
What you should end up with looks something like this at runtime, if you instantiate with the callback argument (new REngineStdOutput () ); otherwise if you just instantiate with the String engineForClass("org.rosuda.REngine.JRI.JRIEngine"), you'll wont get the below output from R on startup/elsewise, depending on if you want it or not:
<pre><code>
    R version 3.3.1 (2016-06-21) --
 </code></pre>
</li>
</ol>

<h2>Figure out the logging system</h2>
Kirk's old code has been using apache.commons.logging and my current modification involves using apache flink, which depends on log4j. And I got the following error message
<pre><code>
log4j:WARN No appenders could be found for logger (org.apache.flink.api.java.ExecutionEnvironment).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
</code></pre>
First of all, what is the difference between log4j and apache.commons.logging? According to <a href="http://stackoverflow.com/questions/8488762/relation-between-log4j-and-apache-commons-logging">this post</a>, log4j is a logging framework, i.e. it provides the code to log messages. Commons-logging is an abstraction layer for logging frameworks, it doesn't log anything itself. In other words, Apache Commons Logging can use log4j (or other logging implementation of the specific library you use), if present and configured. 
Then, why did I get the error message? I went to <a href="http://logging.apache.org/log4j/1.2/faq.html#a3.5">the website</a> mentioned in the error message, where I found the following.
<pre><code>
<i>Why do I see a warning about "No appenders found for logger" and "Please configure log4j properly"?</i>
This occurs when the default configuration files log4j.properties and log4j.xml can not be found and the application performs no explicit configuration. log4j uses Thread.getContextClassLoader().getResource() to locate the default configuration files and does not directly check the file system. Knowing the appropriate location to place log4j.properties or log4j.xml requires understanding the search strategy of the class loader in use. log4j does not provide a default configuration since output to the console or to the file system may be prohibited in some environments. Also see FAQ: Why can't log4j find my properties in a J2EE or WAR application?.
</code></pre>
I took that as a request for setting up log4j.properties or log4j.xml files if I want to keep using apache flink. OK. I have no clue how to do that. Shame on me. Let's take a look at <a href="http://logging.apache.org/log4j/1.2/manual.html">log4j manual</a>, or <a href="http://www.codejava.net/coding/how-to-configure-log4j-as-logging-mechanism-in-java">this one</a>, which looks like an easy tutorial for dummies like me, or <a href="http://stackoverflow.com/questions/5081316/where-is-the-correct-location-to-put-log4j-properties-in-an-eclipse-project">an even better one</a>, right to the point! Yuan, all you need to do is actually figuring out Google search terms as specific as possible...
<h2>References</h2>
<ul>
<li><a href="http://www.cs.mun.ca/java-api-1.5/guide/management/jconsole.html">Using Jconsole</a></li>
<li>http://jagadesh4java.blogspot.com/2014/09/analyzing-jvm-crash.html</li>
<li><a href="http://www.vogella.com/tutorials/EclipseMemoryAnalyzer/article.html#memory">Eclipse Memory Analyzer (MAT) - Tutorial</a></li>
<li><a href="http://homes.cs.washington.edu/~djg/teachingMaterials/spac/grossmanSPAC_forkJoinFramework.html">Beginner's Introduction to Java's ForkJoin Framework</a></li>
</ul>
