---
layout: post
title: "Using other applications on Linux"
date: 2016-03-25
---

<h2>Working with Java on Linux</h2>
I bet <a href="http://ice.he.net/~hedden/jrelinux.html">this post</a> is going to be a huge help someday.
<b>"Could not reserve enough space for object heap" even though there is enough RAM</b>
I often get the following error message, so today I decide to do some research on it.
<pre>
Error occurred during initialization of VM
Could not reserve enough space for object heap
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
</pre>
According to <a href="http://unix.stackexchange.com/questions/109653/java-could-not-reserve-enough-space-for-object-heap-even-though-there-is-enoug">this page</a>, sometimes the system tries to allocate more than I need and a solution for this problem is to limit the chunk of memory java tries to allocate at run time with a minimum Xms or while running with maximum Xmx.


<h2>Playing with Gephi on Linux</h2>


<h2>References</h2>
