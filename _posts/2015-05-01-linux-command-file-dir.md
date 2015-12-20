---
layout: post
title: "File and directory operations on Linux"
date: 2015-05-01
---
<blockquote>The mechanic that would perfect his work must first sharpen his tools. -- Confucius</blockquote>
[Update: this post was originally titled "Explore and Handle large number of big files" and below is the background story I wrote months ago. I will keep it here, for fun. If you come in for the juicy stuffs (at least they are for me), just ignore my "over-emotional" story.]<br /><br />
To me, exploratory data analysis (EDA) is the most intriguing part of data science. I feel it is somewhat like autopsy -- both try to reveal the underlying story -- except that the goal of EDA is to revitalize the data. The task becomes even exiting when the data file to be inspected has unknown structure and huge size. Some of us have been searching the data set Daniel had created using CoJava and finally found them (or at least part of them) yesterday. And it is now my job to interpret the data, which include some pretty big files: one named "output.hap-1.test" of size 0.37TB. I have some clue about <a href="http://lybird300.github.io/2015/04/20/cojava-manual.html#anchor">what might be in that file </a>, but still need to be sure by peeking into them. Because of the daunting size of these files, I decided to inspect them remotely using <a href="http://mobaxterm.mobatek.net/">MobaXterm</a>, which Kirk has been using to demonstrating his CHAT program. (Did I mention that this PhD-MD is also good at computer programming?) I first installed R-3.2.0 on the server and then used "read.table" to obtain the first line of "output.hap-1.test". Fifteen minutes later nothing was returned. It was still reading that single line!!! It seems that the only option left for me is to use Unix commands for EDA. Luckily, I'm not the only one who has encountered such a problem. After researching online I've collected some useful tips and UNIX commands, as shown below. You may also find the references of this post useful and worth reading.</code></pre>

You may need to do many I/O opertions when exploring big files, such as reading parts of a file and then writing the contents to a new file. When this is the case, there are two things to remember. One thing is that there really isn't a good way to seek to a certain line in a file; in other words, eventually we have to read the file line by line. Thus, you may want to cut a huge file into smaller pieces and read these smaller files instead (use bash command to split a file is more efficient than using a Java program). The other thing is that parallelizing disk I/O is generally NOT a good idea (if you decide to do so anyway, here is a <a href="http://stackoverflow.com/questions/8737877/reading-and-writing-multiple-files-in-parallel">post</a> on how. Please note that these days this example runs best with a fork join pool, which can be used with new ForkJoinPool(numprocs) with an await termination. Intensive processes actually work best with these pools while small processes like a fibonacci sequence may be best with a singlethread or a thread executor (better with properly managed custom code)). Hard disks do not like random I/O because they have to continuously seek around to get to the data. This applies ANY disk I/O on a single disk, regardless of whether there are separate files, when different threads compete with one anohter in operating files on the same disk.

Stop an ongoing process: ctrl+z

</br>Create a link to a directory
It is often useful to change to another directory without typing its full pathname: symbolic links provide a useful shortcut to do this. A symbolic link differs from a hard link. It is a small file that contains a reference (by name) to a directory or file that already exists. Unlike normal links, symbolic links can cross filesystems and link to directories. (They are used extensively by the system.) Also unlike normal links, symbolic links are separate files; they cease to work if the file they point to is deleted or renamed, or if they are moved.

Many of the files found in /bin, /lib, and /usr are actually symbolic links that point to files (of the same name) stored below /var/opt. The directories these files are located in are called ``storage sections''. Storage sections are used because they make it easier to install system upgrades. Software subsystems (such as UUCP) consist of many files, which may be installed in several directories. However, all the files in a subsystem belong to a single storage section. By overwriting the contents of the (single) storage section directory, all the files in the subsystem can be updated simultaneously.

To create a symbolic link, use the ln -s option, as follows:
<pre><code>$ ln -s directory symbolic_link</code></pre>
For example, suppose you work in /u/workgrp/tasks/projects and your home directory is /u/me. Your normal command to work on a file would be the following:
<pre><code>$ cd /u/workgrp/tasks/projects</code></pre>
To reduce the typing required, enter the following command:
<pre><code>$ ln -s /u/workgrp/tasks/projects mydata</code></pre>
This command creates a symbolic link called mydata in your current directory. From now on, mydata and /u/workgrp/tasks/projects refer to the same location, and you can relocate to /u/workgrp/tasks/projects by typing cd mydata instead of typing in the full pathname.
You must have write permission on a directory before you can create a link that involves that directory or a file in that directory.

<br/>Examine the contents of a tarfile without unpacking it
<pre><code>$ tar tvf project.tar</code></pre>
Extract gz files
<pre><code>$ gzip -d file.gz</code></pre>
If you want to rezip it, simply type
<pre><code>$ gzip <name of the extracted file></code></pre>
The first command below will gzip all files in subdirectories with a ".dose" extension. The second command below will skip files with a ".gz" extension. The -9 in the gzip command line tells gzip to use the maximum possible compression level (default is -6). Executed commands must end with \; (a backslash and semi-colon) and may use {} (curly braces) as a placeholder for each file that the find command locates. The semicolon at the end of each command line is necessary.
<pre><code>
find . -type f -name "*.dose" -exec gzip -9 {} \;
find . -type f ! -name "*.gz" -exec gzip {} \;
find . -type f -name "*.dose" -o 
</code></pre>
You can also write a script as below using a for loop to find every file then compress it
<pre><code>
for i in `find | grep -E "\.dose$|\.info$"`; do gzip "$i" ; done
</code></pre>
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
Find out the number of rows of a large file<br/>
<pre><code>wc -l filename</code></pre>
You can get a range of the number of columns by running the following two commands. The second command can be useful if you are checking for any missing data (i.e., the existence of any line that is shorter than it should be). In the example below, suppose we are expecting 1959504 columns for each line.
<pre><code>awk '{if( NF > max ) max = NF} END {print max}' filename
awk '{if( NF&lt;1959504||NF&lt;min ) min = NF} END {print min}' filename</code></pre>

Print the number of columns of a file. The first example assumes the columns are separated by tab; the second assumes they are separated by '|'
<pre><code>awk -F$'\t' '{print NF; exit}' FILENAME</code></pre>
<pre><code>awk -F'|' '{print NF; exit}' FILENAME</code></pre>
Print the number of characters in each line of a file
<pre><code>awk '{ print length($0); }' FILENAME</code></pre>
Print the number of characters in a specific line (e.g., the first line; NR indicates line number) of a file
<pre><code>awk 'NR==1{ print length($0); }' FILENAME</code></pre>
Print the value of a specific column at a specific line in a file, for example, the 4th column at the 5th line:
<pre><code>awk 'NR==5 { print $4 }' FILENAME</code></pre>
Get the length of the shortest line
<pre><code>awk '(NR==1||length&lt;shortest){shortest=length} END {print shortest}' filename</code></pre>
Count the number of specific characters in a line

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

Select every other line of a file (fileA) and put them in anohter file (fileB)
Method 1: <pre><code>sed -n '1~2!p' fileA > fileB</code></pre>
It means starting from the first line and printing every other line. Thus, you will get all odd lines, i.e., whose line number is 2K+1.
Method 2: <pre><code>sed 2~2d fileA > fileB</code></pre>
It deletes all even lines
The "FIRST~STEP" syntax means matching every STEP'th line starting with line FIRST. For example, "sed -n 1~2p" will print all the odd-numbered lines in the input stream, and 2~5 will match every fifth line, starting with the second. "d" means delete an indicated line. For example, the following command delete the 3rd line permanently (use -i option for permanent change)
<pre><code>sed -i '3d' filename</code></pre>

Display the contents of a text file on the command line
<ul>
<li>Using cat when your file is short
<pre><code>cat filename</code></pre>
</li>
<li>Using more when examining a large file as it displays the file contents one page at a time, allowing each page to be examined at will. You can press <PgUp> and <PgDn> buttons on the keyboard to page forwards and backwards in the file. To terminate more at any time, press <q>.
<pre><code>more filename</code></pre>
</li>
<li>less is similar to more.
<pre><code>less filename</code></pre>
You can make less behave like cat when invoked on small files and behave normally otherwise by passing it the -F and -X flags.
<pre><code>less -FX filename</code></pre>
</li>
<li>The default editor that comes with the UNIX operating system is called vi (<b>vi</b>sual editor). You can also use it to view a big file. Type ":q" then return (press the <enter> button) to quit vi.
<pre><code>vi filename</code></pre>
</li>
<li>To view the content a compressed file (e.g., gz, tar.gz) without extracting it, you can use the following command. also quit by typing ":q" and return
<pre><code>vim filename</code></pre>
</li>
</ul>

Sort based on fields/columns
<pre><code>sort -g -k7,7 GenotypeChrom_8_Rep10.info > sortedList</code></pre>

Finding a File Containing a Particular Text String
<pre><code>grep [option] "text string to search” directory-path</code></pre>
For example, we can search for a text string in all files under a directory using below (-r means recursively):
<pre><code>grep -r "redeem reward" /home/tom/</code></pre>
-nr: get line number

Replace a specific string in a file with another one.
The following example replaced the string "fea" in the file "hello.txt" with the string "asd". s -- substitute; g -- global, replace any found matches; i -- realtime works with file (without it the changed result will be ouput on screen -- suppose you don't specify any output file other than standard output -- instead of actually changing the original file)
<pre><code>sed -i 's/fea/asd/g' hello.txt</code></pre>
If you just want to replace the content of a certain column (say, change "12" in the first ID column with "8"), you can do
<pre><code>sed -i 's/12/8/1' hello.txt</code></pre>

Count the number of unique values of a field in a tab-delimited text file
<pre><code>cut -f 1 input_file | sort | uniq</code></pre>
This command gets unique values in field 1 (1 is column no.), replacing 1 by 2 will give you unique values in field 2.
To count the number of unique occurences you can make use of wc command in the chain as:
<pre><code>cut -f 1 input_file | sort | uniq -c</code></pre>
If you want to get the total number of only unique values (i.e., ignore redundance), use
<pre><code>cut -f 1 input_file | sort | uniq | wc -l</code></pre>

Compare two files
Suppose you want to compare the 1st column (field) in file1.txt and the 2nd column (field) in file2.txt and output all lines in file1.txt that does not match in file2.txt to a third file (output.txt). You can use the following code:
<pre><code>join -1 1 -2 2 -v 1 <(sort file1.txt) <(sort file2.txt) > output.txt</code></pre>
where -1 is a first file and 1 is the first FIELD and -2 is a second file and 2 is the second column; -v 1 means suppressing (not outputting) matched lines in file1.txt.
In order to use join, you need to make sure that FILE1 and FILE2 are sorted on the join fields. The "sort" command by default applies an alphabetical order. Thus, sort -k2 means sorting the 2nd field alphabetically, while sort -nk2 means sorting the same field numerically. Also, sort -k1 will not sort according to from the first field to the end of the line while sort -k1,1 sorts based on the first field.

Count the number of folders recursively
Navigate to your drive and execute
<pre><code>ls -lR | grep ^d | wc -l</code></pre>

If you know the structure of a big file, instead of loading / reading the entire file, you could split it into smaller chunks with the <a href="http://www.theunixschool.com/2012/10/10-examples-of-split-command-in-unix.html">split</a> command.
<pre><code>$ split -l 500 -d -a 1 out.pos-1 out.pos-1_</code></pre>
Or the <a href="http://www.theunixschool.com/2012/06/awk-10-examples-to-split-file-into.html">awk</a> command, which can handle header.
<pre><code>$ awk 'NR%500==1{x="out.pos-1_"++i;}{print > x}' fileName</code></pre>
<pre><code>awk 'BEGIN{getline f;}NR%500==2{x="out.pos-1_"++i;print f>x;}{print > x}' out.pos-1</code></pre>
This one is little tricky. Before the file is processed, the first line is read using getline into the variable f. NR%3 is checked with 2 instead of 1 as in the earlier case because since the first line is a header, we need to split the files at 2nd, 5th, 8th lines, and so on. All the file names are stored in the array "a" for later processing. Without the END label, all the files will have the header record, but only the last file will have the trailer record. So, the END label is to precisely write the trailer record to all the files other than the last file.

<h2>Remove Files With One Command On Fly</h2>
Let me introduce this powerful command "find", whose basic syntax is:
<pre><code>find dir-name criteria action
<ul>
<li>dir-name : - Defines the working directory such as look into /tmp/</li>
<li>criteria : Use to select files such as "*.sh"</li>
<li>action : The find action (what-to-do on file) such as delete the file</li>
</ul>
</code></pre>
Using "find", we can, for example, find all files having .bak (*.bak) extension in the current directory ("." means the current directory) and its subdirectories and remove them:
<pre><code>$ find . -type f -name "*.bak" -exec rm -f {} \;</code></pre>
Or to be safe, removes these files with confirmation from user:
<pre><code>$ find . -type f -name "*.bak" -exec rm -i {} \;</code></pre>
Sometimes you may need to know whether certain subdirectories are empty or not. For example, the following command will list all non-empty subdirectories of the current working directory whose names end with "abc"
<pre><code>find . ! -empty -type d -name "*abc" </code></pre>

<h2>Copy and/or merge directories</h2>
To copy multiple files (e.g., file1, file2, file3, and file4) from one directory to another
<pre><code>cp /home/usr/dir/{file1,file2,file3,file4} /home/usr/destination/</code></pre>
Note that there are no spaces between the files. If the all the files have the same prefix but different endings you could do <pre><code>cp /home/usr/dir/file{1..4} /home/usr/destination/</code></pre>
To copy a directory with all subdirectories and files, use a similar command as below
<pre><code>cp -r /home/hope/files/* /home/hope/backup</code></pre>
Another very useful command is "rsync", which is a great tool for backing up and restoring files. To sync the contents of dir1 to dir2 on the same system, type:
<pre><code>rsync -r dir1/ dir2</code></pre>
The -r option means recursive (i.e., it copies directories and sub directories). We could also use the -a flag instead:
<pre><code>rsync -a dir1/ dir2</code></pre>
Or use both of them
<pre><code>rsync -ar dir1/ dir2</code></pre>
The -a option stands for "archive" and syncs recursively and preserves symbolic links, special and device files, modification times, group, owner, and permissions.
Note that there is a trailing slash (/) at the end of the first argument in the above commands. It is necessary to mean "the contents of dir1". The alternative, without the trailing slash, would place dir1, including the directory, within dir2. This would create a hierarchy that looks like ~/dir2/dir1/[files]
If files with the same path and name exist in both directories, the command above will overwrite /dir2/SUBDIREC/SOMEFILE with /dir1/SUBDIRECT/SOMEFILE. If you want to replace only older files, add the option -u. If you want to always keep the version in /dir2, add the option --ignore-existing. If you pass the option --remove-source-files. Then rsync copies all the files in turn, and removes source files (in /dir1) when it's done. This is a lot slower than moving if the source and destination directories are on the same filesystem.
The -P flag (upper case) is very helpful as well. It combines the flags --progress and --partial. The first of these gives you a progress bar for the transfers and the second allows you to resume interrupted transfers.
If you wish to exclude certain files or directories located inside a directory you are syncing, you can do so by specifying them using "--exclude". If you don’t want to sync the dir3 directory (including all it’s subdirectories) from the source to the destination folder (i.e., dir3 is the EXACT name of a subdirectory of dir1), use the rsync –exclude option as shown below. You can also use wildcard to indicate the exclusion of subdirectories whose name matches a certain pattern (e.g., starting with 'dir'. The commands below implment the two scenarios
<pre><code>
rsync -ar --exclude 'dir3' dir1/ dir2
rsync -ar --exclude 'dir*' dir1/ dir2
</code></pre>
To exclude a specific file type that has a specific extension (e.g., .txt) or multiple specific file types, do the following.
<pre><code>
rsync -ar --exclude='*.txt' dir1/ dir2
rsync -av --exclude='*.FOO' --exclude='*.BAR' --exclude='*.ZIM' dir1/ dir2
</code></pre>
If we have specified a pattern to exclude, we can override that exclusion for files that match a different pattern by using the --include= option. If you only want to match a few files or directories, you need to use "--include" to include them, and every directory along the path that leads to them (for example with --include="*/"). And you ALSO need to exclude the rest with "--exclude='*'". This is because (a) excluding a directory will by default (i.e., automatically) exclude everything underneath it; (b) including a directory, however, doesn't automatically include its contents and to do so, you can use "--include='directory/***'" (in recent versions of rsync). For each file, the first matching rule applies (and anything never matched is included). For example, the following command will copy certain subdirectories (and their subdirectories) from dir1 to dir2 and each of these subdirectories should have a name start with "SNP_19".
<pre><code>rsync -arvP --include='SNP_19*/***' --exclude='*' --ignore-existing dir1/ dir2</code></pre>
This one below copy all files underneath dir1/SNP*/ to dir2, but not the files in the subdirectories of SNP*
<pre><code>rsync -arP --include='/SNP*/' --include='/SNP*/*' --exclude='*' --ignore-existing dir1/ dir2</code></pre>
About the patterns, if a pattern doesn't contain a "/", it applies to the file name without directory. If a pattern ends with "/", it applies to directories only. If a pattern starts with "/", it applies to the whole path from the directory that was passed as an argument to rsync. Use --include='*/' to include all subdirectories (add -m to not copy directories that would end up empty). '*' means any substring of a single directory component; '**' operator matches any path substring. For example, the following command will copy every file and subdirectories (because of -r) from dir1 to dir2, excepet for the contents of any third-level subdirectories whose name starts with "Imputation_Mini" or ".sh" file in any of the third-level subdirectories.
<pre><code>rsync -arvP --exclude='SNP_*/*/Imputation_Mini*/*' --exclude='SNP_*/*/*/*.sh' --ignore-existing dir1/ dir2</code></pre>
If you want to copy specific subdirectories, you need to "include" all the directories in the directory tree before the target subdirectory and the "***" to include everything underneath the target directory. For example,
<pre><code>rsync -arP --include='/SNP*/' --include='/SNP*/*/'  --include='SNP*/*/Imputation_MiniMacOutput_RefCtrl/' --include='SNP*/*/Imputation_MiniMacOutput_RefCtrl/***' --exclude='*' --ignore-existing --remove-source-files /projects/sequence_analysis/vol4/simGWASData/ /scratch/linly/simGWASData</code></pre>
Lastly, you can remove the files in the source folder (i.e., the sender side) using the option "--remove-source-file"

<b>UPDDATE</b>: Up till today (09/11/2015, Uh-oh, a sad date) I've gone through a 300GB+ file, 1TB memory, 70TB disk space limit, 72h cpu time, 80 cores, etc...So I no longer have the strong feelings described at the beginning of this post. To be honest, it even sounds a little bit naive to me now. Yesterday when my colleague Ruhi told me our IT group has granted her an exclusive 5TB storage space, my first response was "only 5TB?" Then I couldn't believe I actually said that...

<h2>References</h2>
<ul>
<li><a href="http://www.cureffi.org/2014/01/15/running-r-batch-mode-linux/">Running R in batch mode on Linux</a></li>
<li><a href="http://www.biostat.jhsph.edu/~rpeng/docs/R-large-tables.html">Reading large tables into R</a></li>
<li><a href="http://www.drbunsen.org/explorations-in-unix/">Explorations in Unix</a></li>
<li><a href="http://blog.ouseful.info/2011/06/04/playing-with-large-ish-csv-files-and-using-them-as-a-database-edina-openurl-logs/">Playing With Large (ish) CSV Files</a></li>
<li><a href="http://www.thegeekstuff.com/2009/08/10-awesome-examples-for-viewing-huge-log-files-in-unix/">10 Awesome Examples for Viewing Huge Log Files in Unix</a></li>
<li><a href="http://stackoverflow.com/questions/2016894/easy-way-to-split-a-large-text-file">Easy Way to Split a Large Text File</a></li>
<li><a href="http://www.cyberciti.biz/faq/howto-search-find-file-for-text-string/">Finding a File Containing a Particular Text String In Linux Server</a></li>
<li><a href="http://datavu.blogspot.com/2014/08/useful-unix-commands-for-exploring-data.html">Useful Unix commands for exploring data</a></li>
<li><a href="http://www.theunixschool.com/2011/02/sed-replace-or-substitute-file-contents.html">sed - Replace or substitute file contents</a></li>
<li><a href="http://www.thegeekstuff.com/2011/01/rsync-exclude-files-and-folders/">6 rsync Examples to Exclude Multiple Files and Directories using exclude-from</a></li>
</ul>