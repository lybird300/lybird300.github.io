---
layout: post
title: "Explore unknown big files"
date: 2015-05-01
---
<blockquote>The mechanic that would perfect his work must first sharpen his tools. -- Confucius</blockquote>
To me, exploratory data analysis (EDA) is the most intriguing component of data science. It is somewhat like autopsy -- both try to reveal the underlying story -- except that the goal of EDA is to revitalize the data. The task becomes even exiting when the data file to be inspected has unknown structure and huge size. We were looking for the simuated data Daniel created using CoJava and eventually found them (or part of them) yesterday. And it is now my job to interpret the data, which include some pretty big files: one named "output.hap-1.test" of size 1.82GB and the other named "output.pos-1" of size 15.8MB. I have some clue about <a href="http://lybird300.github.io/2015/04/20/cojava-manual.html#anchor">what might be the contents </a>, but still need to be sure by peeking into these files. Because of their daunting size, I decided to inspect them remotely using <a href="http://mobaxterm.mobatek.net/">MobaXterm</a>, which Kirk has been using to demonstrating his CHAT program. (Did I mention that this PhD + MD is also good at computer programming?)

I first installed R-3.2.0 on the server and then used "read.table" to obtain the first line of "output.hap-1.test". Fifteen minutes later nothing was returned. It was still reading that single line!!! It seems that the only option left for me is to use Unix commands for EDA. Luckily, I'm not the only one who has encountered such a problem (see the references of this post). 


<h2>References</h2>
<ul>
<li><a href="http://www.cureffi.org/2014/01/15/running-r-batch-mode-linux/">Running R in batch mode on Linux</a></li>
<li><a href="http://www.biostat.jhsph.edu/~rpeng/docs/R-large-tables.html">Reading large tables into R</a></li>
<li><a href="http://www.drbunsen.org/explorations-in-unix/">Explorations in Unix</a></li>
<li><a href="http://blog.ouseful.info/2011/06/04/playing-with-large-ish-csv-files-and-using-them-as-a-database-edina-openurl-logs/">Playing With Large (ish) CSV Files</a></li>
</ul>
