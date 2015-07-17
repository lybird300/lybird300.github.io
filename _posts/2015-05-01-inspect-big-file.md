---
layout: post
title: "Explore unknown big files"
date: 2015-05-01
---
<blockquote>The mechanic that would perfect his work must first sharpen his tools. -- Confucius</blockquote>
To me, exploratory data analysis (EDA) is the most intriguing part of data science. I feel it is somewhat like autopsy -- both try to reveal the underlying story -- except that the goal of EDA is to revitalize the data. The task becomes even exiting when the data file to be inspected has unknown structure and huge size. Some of us have been searching the data set Daniel had created using CoJava and finally found them (or at least part of them) yesterday. And it is now my job to interpret the data, which include some pretty big files: one named "output.hap-1.test" of size 0.37TB. I have some clue about <a href="http://lybird300.github.io/2015/04/20/cojava-manual.html#anchor">what might be in that file </a>, but still need to be sure by peeking into them. Because of the daunting size of these files, I decided to inspect them remotely using <a href="http://mobaxterm.mobatek.net/">MobaXterm</a>, which Kirk has been using to demonstrating his CHAT program. (Did I mention that this PhD-MD is also good at computer programming?)

I first installed R-3.2.0 on the server and then used "read.table" to obtain the first line of "output.hap-1.test". Fifteen minutes later nothing was returned. It was still reading that single line!!! It seems that the only option left for me is to use Unix commands for EDA. Luckily, I'm not the only one who has encountered such a problem. After researching online I've collected some useful tips and UNIX commands, as shown below. You may also find the references of this post useful and worth reading.

You may need to do many I/O opertions when exploring big files, such as reading parts of a file and then writing the contents to a new file. When this is the case, there are two things to remember. One thing is that there really isn't a good way to seek to a certain line in a file; in other words, eventually we have to read the file line by line. Thus, you may want to cut a huge file into smaller pieces and read these smaller files instead (use bash command to split a file is more efficient than using a Java program). The other thing is that parallelizing disk I/O is generally NOT a good idea (if you decide to do so anyway, here is a <a href="http://stackoverflow.com/questions/8737877/reading-and-writing-multiple-files-in-parallel">post</a> on how. Please note that these days this example runs best with a fork join pool, which can be used with new ForkJoinPool(numprocs) with an await termination. Intensive processes actually work best with these pools while small processes like a fibonacci sequence may be best with a singlethread or a thread executor (better with properly managed custom code)). Hard disks do not like random I/O because they have to continuously seek around to get to the data. This applies ANY disk I/O on a single disk, regardless of whether there are separate files, when different threads compete with one anohter in operating files on the same disk.

Stop an ongoing process: ctrl+z
Examine the contents of a tarfile without unpacking it
<pre><code>$ tar tvf project.tar</code></pre>
Extract gz files
<pre><code>$ gzip -d file.gz</code></pre>
Extract tar.gz files
<pre><code>tar options file.tar.gz
e.g., tar -xvf file.tar.gz </code></pre>, where
<ul>
<li>-z : Work on gzip compression automatically when reading archives.</li>
<li>-x : Extract archives.</li>
<li>-v : Produce verbose output i.e. display progress and extracted file list on screen.</li>
<li>-f : Read the archive from the archive to the specified file.</li>
<li>-t : List the files in the archive.</li>
</ul>
Find out the number of rows and columns of a large file<br/>
Columns: 
<pre><code>awk: awk '{if( NF > max ) max = NF} END {print max}' filename</code></pre>
Rows:
<pre><code>wc -l filename</code></pre>
<blockquote>If the file is not that big, you can simply open it using the internal text editor of MobaXterm and then put the cursor on the last element of a line (wrap view). The row and column information of that position will be displayed at the bottom of the text editor. Be careful though. The number of columns may not be the number of meaningful items per line due to possible whitespaces. Once I miscalculated the number of items and used the wrong number as a default parameter value. Then I spent almost a day trying to figure out why the program did not produce expected results and eventually relized that the program was fed with wrong data!</blockquote>
If you would like to know the number of files in a directory, navigate to that directory, then use:
<pre><code>ls -1 | wc -l</code></pre>
Work like a charm.
Display specific lines (based on line number) of a file using sed command
<pre><code>$ sed -n -e Xp -e Yp FILENAME
$ sed -n M,Np FILENAME</code></pre>
The first example below prints out the 1st, 2nd, and 1050th lines. The second example below prints out the first two lines of the same file
<pre><code>$ sed -n -e 1p -e 2p -e 1050p /var/log/syslog
$ sed -n -e 1,2p /var/log/syslog</code></pre>
If you want to output these lines to a new file, use something like
<pre><code>sed -n -e '10,100p' input.txt > output.txt</code></pre>
You can also display first N lines of a file using head command.
<pre><code>$head -n N FILENAME
e.g., $ head -n 15 /var/log/maillog</code></pre>
Display last N lines of the file using tail command
<pre><code>$tail -n N FILENAME
e.g., $ tail -n 50 /var/log/messages</code></pre>
Print the number of characters in each line of a file
<pre><code>awk '{ print length($0); }' FILENAME</code></pre>
Print the number of characters in a specific line (e.g., the first line) of a file
<pre><code>awk 'NR==1{ print length($0); }' FILENAME</code></pre>
Count the number of specific characters in a line

Finding a File Containing a Particular Text String
<pre><code>grep [option] "text string to search” directory-path<br/>
e.g., grep -r "redeem reward" /home/tom/ searches for a text string all files under a directory (-r means recursively)</code></pre>

Count the number of folders recursively
Navigate to your drive and execute
<pre><code>ls -lR | grep ^d | wc -l</code></pre>

If you know the structure of a big file, instead of loading / reading the entire file, you could split it into smaller chunks with the <a href="http://www.theunixschool.com/2012/10/10-examples-of-split-command-in-unix.html">split</a> command.
<pre><code>$ split -l 500 -d -a 1 out.pos-1 out.pos-1_</code></pre>
Or the <a href="http://www.theunixschool.com/2012/06/awk-10-examples-to-split-file-into.html">awk</a> command, which can handle header.
<pre><code>$ awk 'NR%500==1{x="out.pos-1_"++i;}{print > x}' fileName</code></pre>
<pre><code>awk 'BEGIN{getline f;}NR%500==2{x="out.pos-1_"++i;print f>x;}{print > x}' out.pos-1</code></pre>
This one is little tricky. Before the file is processed, the first line is read using getline into the variable f. NR%3 is checked with 2 instead of 1 as in the earlier case because since the first line is a header, we need to split the files at 2nd, 5th, 8th lines, and so on. All the file names are stored in the array "a" for later processing.
    Without the END label, all the files will have the header record, but only the last file will have the trailer record. So, the END label is to precisely write the trailer record to all the files other than the last file.

<h2>References</h2>
<ul>
<li><a href="http://www.cureffi.org/2014/01/15/running-r-batch-mode-linux/">Running R in batch mode on Linux</a></li>
<li><a href="http://www.biostat.jhsph.edu/~rpeng/docs/R-large-tables.html">Reading large tables into R</a></li>
<li><a href="http://www.drbunsen.org/explorations-in-unix/">Explorations in Unix</a></li>
<li><a href="http://blog.ouseful.info/2011/06/04/playing-with-large-ish-csv-files-and-using-them-as-a-database-edina-openurl-logs/">Playing With Large (ish) CSV Files</a></li>
<li><a href="http://www.thegeekstuff.com/2009/08/10-awesome-examples-for-viewing-huge-log-files-in-unix/">10 Awesome Examples for Viewing Huge Log Files in Unix</a></li>
<li><a href="http://stackoverflow.com/questions/2016894/easy-way-to-split-a-large-text-file">Easy Way to Split a Large Text File</a></li>
</ul>
