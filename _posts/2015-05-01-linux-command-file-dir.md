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

<h2>Download files from external sources</h2>
If you want to get something from an ftp server, use wget as follows (m for mirroring).
<pre><code>wget ftp://....
wget -m ftp://username:password@server/path</code></pre>
If your username or password contains special characters, you may need to use the format:
wget -m --user=username --password=password ftp://...

<h2>Create a link to a directory</h2>
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
If you would like to see the actual path of a symbolic link, use the following
<pre><code>readlink -f [symbolic link name]</code></pre>
<h2>Extract gz files</h2>
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
The following find command goes to all subdirectories in path1/, find each file ends with a digit number and a gz extension, and remove the ".gz" extension. In previous examples, "." after "find" means current directory. Here "path1" indicates a specific path to look into.
<pre><code>find path1/ -type f -name "*[0-9].gz" | while read f; do mv "$f" "${f%.gz}"; done</code></pre>
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

<h2>Find out the number of rows of a large file</h2>
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

<h2>Count the number of specific characters in a line</h2>
<blockquote>If the file is not that big, you can simply open it using the internal text editor of MobaXterm and then put the cursor on the last element of a line (wrap view). The row and column information of that position will be displayed at the bottom of the text editor. Be careful though. The number of columns may not be the number of meaningful items per line due to possible whitespaces. Once I miscalculated the number of items and used the wrong number as a default parameter value. Then I spent almost a day trying to figure out why the program did not produce expected results and eventually relized that the program was fed with wrong data!</blockquote>
If you would like to know the number of files in a directory, navigate to that directory, then use:
<pre><code>ls -1 | wc -l</code></pre>
Work like a charm.

<h2>Display specific lines (based on line number) of a file using sed command</h2>
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
If you want to look into a zipped file without extracting it, you can use "zcat", such as
<pre><code>
zcat FILENAME.gz | head
zcat FILENAME.gz | tail
</code></pre>
If you want to see specific lines of a compressed file without extracting the file, use the following (the second for a tar.gz file):
<pre><code>
gzip -cd GraphSet_12_00001_16915.gz | head -n 2
tar -xzOf some_huge_file.tar.gz | head
</code></pre>
"zcat" can be combined with other commands mentioned in this post. For example, the first command below shows you the occurrences of a string per line in a gz file; the second command shows you the first column at the 350 line of a gz file. The third command counts the occurrences of '717165' in all files meet the name pattern in the current directory
<pre><code>
zcat filename.gz | grep -o -n 'string' | cut -d : -f 1 | uniq -c
zcat filename.gz | awk 'NR==350 {print $1}'
zcat out.pos-1_*.gz | grep -c "717165"
</code></pre>
You can also use zgrep on renci machine. For example, the following command allows you to find recursively in the current directory gzipped files with the name pattern "Final*.gz" and contains a string with the wildcard pattern "875545[0-9]". The command will output unique lines (the entire line, not just the string) that contain the matched string, following by the path and name of the file with these lines. 
<pre><code>find . -type f -name "Final*.gz" -exec zgrep -E "875545[0-9]" {} \; -print | sort | uniq -c</code></pre>
Sometimes you may want to search the file for a specific string pattern and then print the line above or below the matching line. For example, the pajek .net file of a large network starts with a huge list of vertices and after that there is a line "Edges" indicating the start of the edge list. For BSD or GNU grep you can use -B num to set how many lines before the match and -A num for the number of lines after the match, such as
<pre><code>grep -B 3 -A 2 foo README.txt</code></pre>
If you want the same number of lines before and after you can use -C num. The following will show 3 lines before and 3 lines after.
<pre><code>grep -C 3 foo README.txt</code></pre>

<br/>
<h2>Select every other line of a file (fileA) and put them in anohter file (fileB)</h2>
Method 1: <pre><code>sed -n '1~2!p' fileA > fileB</code></pre>
It means starting from the first line and printing every other line. Thus, you will get all odd lines, i.e., whose line number is 2K+1.
Method 2: <pre><code>sed 2~2d fileA > fileB</code></pre>
It deletes all even lines
The "FIRST~STEP" syntax means matching every STEP'th line starting with line FIRST. For example, "sed -n 1~2p" will print all the odd-numbered lines in the input stream, and 2~5 will match every fifth line, starting with the second. "d" means delete an indicated line. For example, the following command delete the 3rd line permanently (use -i option for permanent change)
<pre><code>sed -i '3d' filename</code></pre>

<h2>Display the contents of a text file on the command line</h2>
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
<li>To view the content a compressed file (e.g., gz, tar.gz) without extracting it, you can use the following command (also quit by typing ":q" and return)
<pre><code>vim filename</code></pre>
</li>
<li>Examine the contents of a tar file without unpacking it
<pre><code>$ tar tvf project.tar</code></pre>
</li>
</ul>

Sort based on fields/columns
<pre><code>sort -g -k7,7 GenotypeChrom_8_Rep10.info > sortedList</code></pre>

<h2>Finding a File Containing a Particular Text String</h2>
<pre><code>grep [option] "text string to search” directory-path</code></pre>
For example, we can search for a text string in all files under a directory using below (-r means recursively):
<pre><code>grep -r "redeem reward" /home/tom/</code></pre>
-nr: get line number
The following code find all files in the current directory that contains the string "8791 0 0" and delete them. If you would like to see which files are about to be deleted, simply remove the | xargs -0 rm -f -- part, and leave off the Z option to grep.
<pre><code>grep -lrIZ "8791 0 0" . | xargs -0 rm -f --</code></pre>
<ul>
<li>-l prints file names of files matching the search pattern.</li>
<li>-r performs a recursive search for the pattern foo in the given directory. If this doesn't work, try -R.</li>
<li>-I (capital i) causes binary files like PDFs to be skipped.</li>
<li>-Z ensures that file names are zero- (i.e., nul-)terminated so that a name containing white space does not get interpreted in the wrong way (i.e., as multiple names instead of one).</li>
<li>xargs -0 feeds the file names from grep to rm -f, separating words by zero (nul) bytes (remember the -Z option from grep).</li>
<li>-- is often forgotten but it is very important to mark the end of options and allow for removal of files whose names begin with -.</li>
</ul>
Using grep -v you can simulate the NOT conditions. -v option is for invert match. It matches all the lines except the given pattern. For example,
<pre><code>squeue | grep -v mini</code></pre>
Print the entire line with the matching pattern
<pre><code>grep -E 'needle' FILENAME</code></pre>
Count the total occurrences of a string in a file
<pre><code>grep -o 'needle' FILENAME | wc -l</code></pre>
Show the occurrences of a string in a file with the line number of occurrences
<pre><code>grep -o -n 'needle' FILENAME</code></pre>
Count the occurrences of a string PER LINE in a file
<pre><code>grep -o -n 'needle' FILENAME | cut -d : -f 1 | uniq -c</code></pre>
Count all occurrences of string in lots of files with grep. However, it counts multiple occurrences on one line only once. 
<pre><code>cat * | grep -c 'needle'</code></pre>
Note that if you want to count the exact string (i.e., not when it is part of another string), use the following expression
<pre><code>"\<needle\>"</code></pre>
If the string pattern that you would like to find matches include multiple fields separated by "TAB", I find the following grep option work
<pre><code>
grep -P "\t1.94\t10\t1\t[0-9]," (print lines with a string of " 1.94 10 1 [0-9],"; the last field starts with a digit followed by a comma)
</code></pre>
A pipeline example would be:
<pre><code>
find . -type f -name "Final*.gz" -exec zgrep -P "\t1.94\t10\t1\t[0-9]," {} \; -print | sort | uniq -c
</code></pre>
And if you would like to output the results to a file (say log.txt in the current folder) instead of displaying it on the screen, you can use
<pre><code>
find . -type f -name "Final*.gz" -exec zgrep -P "\t1.94\t10\t1\t[0-9]," {} \; -print | sort | uniq -c > log.txt
</code></pre>
Or maybe it's better to use "awk"
<pre><code>
awk '$3 ~ /snow/ { print }' dummy_file
awk '$3 == "snow"'
</code></pre>

<h2>Replace a specific string in a file with another one</h2>
The following example replaced the string "fea" in the file "hello.txt" with the string "asd". s -- substitute; g -- global, replace any found matches; i -- realtime works with file (without it the changed result will be ouput on screen -- suppose you don't specify any output file other than standard output -- instead of actually changing the original file)
<pre><code>sed -i 's/fea/asd/g' hello.txt</code></pre>
If you just want to replace the content of a certain column (say, change "12" in the first ID column with "8"), you can do
<pre><code>sed -i 's/12/8/1' hello.txt</code></pre>
If you just want to replace the matched string at a line (say, change all "0" in the second line to "1"), you can do
<pre><code>sed -i '2s/0/1/g' geno.txt</code></pre>
Note that sed cannot be directly used on gz file. You have to unzip the file ("gzip -d filename"), do the change (with "sed -i") and rezip the file ("gzip samefile").
If you want to replace only if the file name matches another string or has a specific extension or is of a certain type and you would like to check every subdirectories recursively under the current directory, you can use
<pre><code>find . -type f -name "*.sh" -exec sed -i 's/Xmx20g/Xmx12g/g' {} +</code></pre>
A similar command can be used to add a new line in specified files. For example, the following command add a new line with the number  "8" before the original line with a number "9"
<pre><code>find . -maxdepth 1 -type f -name "Chromo_9.txt" -exec sed -i 's/8\n9/9/g' {} +</code></pre>
If you want to delete a line in multiple files, you can use the following commands (the first one deletes the "8" line added above). Be careful when the file or directory name contains "_", as two "__" are difficult to distinguish from one "_" yet the difference totally matters when you try to find something!
<pre><code>find . -type f -name "Chromo_9.txt" -exec sed -i '/8/d' {} +
 find . -wholename './chatset_*/09_0000[3-4]*.sh' -exec sed -i '/#SBATCH -t/d' {} +</code></pre>
The "sed" command is very flexible, if there is "/" in the string that you would like to replace or use for replacement, you can Use another character as delimiter in the s command, as shown below
<pre><code></code></pre>
Note that you need to escape specific characters when using "sed" (See <a href="http://unix.stackexchange.com/questions/32907/what-characters-do-i-need-to-escape-when-using-sed-in-a-sh-script">here</a>). For example, the following command tries to replace "largemem-[0-1]-0" with "largemem-0-0,largemem-1-0"
<pre><code>find . -wholename './Rep[2-9]/batchRunChat.sh' -exec sed -i 's/largemem-\[0-1\]-0/largemem-0-0,largemem-1-0/g' {} +</code></pre>
Another example below removes the string "batchRunChat* &" in the file "jobList.txt"
<pre><code>sed -i 's/ batchRunChat\* \&//g' jobList.txt</code></pre>
If you want to delete empty lines in a file, you can use the first command below. However, sometimes it does not work because there are space/tabs in the seemingly "empty" lines. In the latter case, you should use the second command below, which also works in the former scenario (i.e., it is more general).
<pre><code>sed -i '/^$/d' filename
sed -i '/^\s*$/d' filename</code></pre>
If you want to change the entire column, you can use "awk" command. The following example change the 3th and 4th columns of the input file from "9999" and output the result to a new file. Note that you need to set Output Field Separator to tab "\t" and one way to do it is with -v option
<pre><code>awk -vOFS='\t' '{$3="9999"; $4="9999"; print}' DataMerge_testcase_Set1.fam > DataMerge_testcase_Set2.fam</code></pre>
If you want to change the value of a specific field (i.e., column) at a specific line, say, change the first two columns of the first line to 10000, you can use the following command ("awk" command is easier to understand than "sed" command, although "sed" can do in-line edit in the original file by specify the "-i" option)
<pre><code>
awk -vOFS='\t' 'NR==1{$1="10000";$2="10000"}1' DataMerge_testcase_commVar.ped > tmp #the field separator in this file is '\t' tab
awk -vOFS='\t' 'NR==1{print $1, $2, $3, $4}' tmp #now check the tmp file. See whether it is indeed as we want
mv tmp DataMerge_testcase_commVar.ped #if so, use the tmp file to replace the original file
</code></pre>
You can also use the following combined command
<pre><code>awk -vOFS='\t' 'NR==1{$1="10000";$2="10000"}1' DataMerge_testcase_commVar.ped > tmp && mv tmp DataMerge_testcase_commVar.ped</code></pre>

<h2>Add an extra column in the middle of a file</h2>
You can use the following command to add a third column (with the content '0') to the middle of a pre-existing tab-delimited text file. In this command, $2 means the second column, and we replace it with $2 FS "0", where "FS" is the delimiter used in this file and "0" is the content of the added column. mv -i can avoid creating a backup file
<pre><code>
awk -F'\t' '{$2 = $2 FS "0"; print}' DataMerge_testcase_reference.map > tmp
mv -i tmp DataMerge_testcase_reference.map
</code></pre>
You can also swap two columns using a similar command. Suppose you would like the swap the first and the second columns in a comma delimited file
<pre><code>awk -F',' '{print $2,$1,$3}' OFS=, file </code></pre>
You can combine these two operations together by
<pre><code>
awk -F'\t' '{$2 = $2 FS "0"; print $2,$1,$3}' DataMerge_testcase_reference.map > tmp
</code></pre>


<h2>Rename multiple files in a small part of their names</h2>
The following code will find all png files in the current directory and replace the string "rep2" in their names with the string "rep0"
<pre><code>for i in *.png; do mv $i $(echo $i | sed 's/rep2/rep0/g'); done</code></pre>

<h2>Count the number of unique values of a field in a tab-delimited text file</h2>
<pre><code>cut -f 1 input_file | sort | uniq</code></pre>
This command gets unique values in field 1 (1 is column no.), replacing 1 by 2 will give you unique values in field 2.
To count the number of unique occurences you can make use of wc command in the chain as:
<pre><code>cut -f 1 input_file | sort | uniq -c</code></pre>
If you want to get the total number of only unique values (i.e., ignore redundance), use
<pre><code>cut -f 1 input_file | sort | uniq | wc -l</code></pre>
If your file is not tab-delimited, you will have to specify the delimiter using -d, e.g., -d','

<h2>Compare two files</h2>
Suppose you want to compare the 1st column (field) in file1.txt and the 2nd column (field) in file2.txt and output all lines in file1.txt that does not match in file2.txt to a third file (output.txt). You can use the following code:
<pre><code>join -1 1 -2 2 -v 1 <(sort file1.txt) <(sort file2.txt) > output.txt</code></pre>
where -1 is a first file and 1 is the first FIELD and -2 is a second file and 2 is the second column; -v 1 means suppressing (not outputting) matched lines in file1.txt.
In order to use join, you need to make sure that FILE1 and FILE2 are sorted on the join fields. The "sort" command by default applies an alphabetical order. Thus, sort -k2 means sorting the 2nd field alphabetically, while sort -nk2 means sorting the same field numerically. Also, sort -k1 will not sort according to from the first field to the end of the line while sort -k1,1 sorts based on the first field.<br/>
Take a look at another example. It sees if there were any matches between column 2 of file1 and column 6 of file2. Any match will be printed along with the respective string in column 14 of file2, saved onto a different file, file3.
<pre><code>join -1 2 -2 6 -o 1.2,2.14 <(sort -k2 file1) <(sort -k6 file2 ) > file3</code></pre>
<ul>
<li>-1 2 defines that in file1 field 2 is the join field.</li>
<li>-2 6 defines that in file2 field 6 is the join field.</li>
<li>-o 1.2,2.14 is the output format file1 field 2 followed by file2 field 14.</li>
<li><(sort ...) the two files must be sorted at the join field -k for join to work properly. Note that <b>there should not be a space between "<" and "(". Otherwise the command will fail.</b></li>
</ul>
<br/>
Suppose you have two files file1 and file2. You want to compare file1 with file2 and generate a file3 which contains the lines in file1 which are not present in file2. You can use "comm", which compares two <b>sorted files</b>(yes, they must be sorted first, see the example command below) line by line. The option "-1" "-2" and "-3" suppress lines unique to FILE1, FILE2, and lines that appear in both files respectively. So the following command will do the job. Note that there should not be any space between "<" and "("!!
<pre><code>comm -23 <(sort FILE1) <(sort FILE2) > FILE3</code></pre>

<h2>Compare two directories recursively</h2>
If you just want to quickly compare the "structure" of the two directories not the actual content of files inside (the latter takes much more time), you can use "rsync -n" to do a dry run
<pre><code>rsync -nav --delete DIR1/ DIR2</code></pre>
<b>Caution</b>: (a) Always use the -n, aka --dry-run, option, or it will synchronize (change the contents of) the directories. (b) Put a / at the end of DIR1; otherwise it will compare the directory DIR1 with the contents of DIR2
The output will show you which files/directories differ. Files/directories present in DIR2 and not in DIR1 will be prefaced with the word deleting.
Or you can use the following command (but rsync will be faster if you've got a significant number of files/directories to compare). Basically, it prints out all of the directories including subdirectory paths relative to the base directoryN directories. <b>If you only care about directories, add "-type d" between "directory#" and "-printf"</b>
<pre><code>
find directory1 -printf "%P\n" | sort > file1
find directory2 -printf "%P\n" | sort | diff - file1
</code></pre>
The following command will give you a nice side-by-side display of the two directory hierarchies with any common sections folded.
<pre><code>vimdiff <(cd dir1; find . | sort) <(cd dir2; find . | sort)</code></pre>
If you also need to compare the contents of files, the following command will let you know if there are any diferences in directory structure and file contents, but it's gonna take a while...
<pre><code>diff -qry dir1 dir2</code></pre>
"-q" switch reports only whether the files differ, not the details of the difference. "-r" means recursively. "-y" outputs in two columns side by side. It reports which files are only in dir1 and those only in dir2 and also the changes of the files present in both directories if any. 
The following variant reports which files are only in dir1
<pre><code>diff -qr dir1 dir2 | grep dir1 | awk '{print $4}' > difference1.txt</code></pre>
<h2>Count the number of folders recursively</h2>
Navigate to your drive and execute
<pre><code>ls -lR | grep ^d | wc -l</code></pre>
Count the number of files with specific extension (e.g., ".info.gz"). -R is for recursive search in subdirectories.
<pre><code>ls -lR *.info.gz | wc -l</code></pre>

<h2>Make multiple directories with some naming pattern</h2>
The basic command for making a directory is "mkdir". If you want to make nested directories (i.e., parent directories do not exist yet), you can use the "-p" flag, i.e., "mkdir -p <path to the new directory>".
The following command will create three sub directories "folder01" "folder02" and "folder03" inside the directory "projects/Labs"
<pre><code>mkdir $(seq -f "projects/Labs/folder%02g" 3)</code></pre>
The following command will create folder "11" "12" "13" until "30" in the current directory
<pre><code>mkdir {11..30}</code></pre>
If you know the structure of a big file, instead of loading / reading the entire file, you could split it into smaller chunks with the <a href="http://www.theunixschool.com/2012/10/10-examples-of-split-command-in-unix.html">split</a> command.
<pre><code>$ split -l 500 -d -a 1 out.pos-1 out.pos-1_</code></pre>
Or the "awk" command:
<pre><code>$ awk 'NR%500==1{x="out.pos-1_"i++;}{print > x}' out.pos-1</code></pre>
But if the original file has a header and you want to keep the header in every split file, use the <a href="http://www.theunixschool.com/2012/06/awk-10-examples-to-split-file-into.html">awk</a> command.
<pre><code>awk 'BEGIN{getline f;}NR%500==2{x="out.pos-1_"i++;print f>x;}{print > x}' out.pos-1</code></pre>
In the code above, before the file is processed, the first line is read using getline into the variable f. NR%500 is checked with 2 instead of 1 as in the earlier case because since the first line is a header, we need to split the files at the 2nd line, and so on.

<h2>Remove Files efficiently</h2>
First, you can remove files with different extensions altogether using the "rm" command as below. It removes files with any one of the four types of extensions.
<pre><code>rm *.log *.rec.gz *.info.draft *.erate.gz</code></pre>
Next, let me introduce this powerful command "find", whose basic syntax is:
<pre><code>find dir-name criteria action
<ul>
<li>dir-name : - Defines the working directory such as look into /tmp/</li>
<li>criteria : Use to select files such as "*.sh"</li>
<li>action : The find action (what-to-do on file) such as delete the file</li>
</ul>
</code></pre>
Using "find", we can, for example, find all files having .bak (*.bak) extension in the current directory ("." means the current directory) and its subdirectories and remove them:
<pre><code>$ find . -type f -name "*.bak" -exec rm -f {} \;</code></pre>
"-f" will suppress warning messages and confirmation requests. If you would like to play safe, "-i" allows you to remove these files with confirmation requests:
<pre><code>$ find . -type f -name "*.bak" -exec rm -i {} \;</code></pre>
Sometimes the files you would like to find are distributed in multiple different directories that have a certain path pattern. The find command allows you to limit the search to these directories. For example,
<pre><code>find . -wholename './Case500_Control500_GRR20.0_BDR*/ChatInput_tagSNP/Rep[5-9]/ChromosomeSpecificData/GenotypeChrom_09.gz' -exec rm -i {} \;
find . -wholename './Rep[0-9]/ChromosomeSpecificData/GenotypeChrom_[0-1][!8].gz' -exec rm -i {} \;
find . -wholename './Case100_*/Imputation_MiniMacOutput_RefCtrl/*.info.draft' -exec rm -f {} \;</code></pre>
Note that these two commands won't work if you miss the "." at the beginning of the path.
Similarly, sometimes you would like to exclude some paths from searching to save time, you can use the following
<pre><code>find -name "*.js" -not -path "./directory/*"</code></pre>
Sometimes you may need to know whether certain subdirectories are empty or not. For example, the following command will list all non-empty subdirectories of the current working directory whose names end with "abc"
<pre><code>find . ! -empty -type d -name "*abc" </code></pre>
Sometimes you would like to limit the span of the search (otherwise it will go through the first level and all subdirectories of your specified directory!!)
If you just want to limit the find to the first level you can add the -maxdepth option, for example
<pre><code>find /dev -maxdepth 1 -name 'abc-*'</code></pre>
If you want to exclude a particular subdirectory named ".udev", you can do:
<pre><code>find /dev -name '.udev' -prune -o -name 'abc-*'</code></pre>
When attempting to remove a directory using a command such as the rmdir command, you may receive a prompt such as "rmdir: 'dir': Directory not empty" and be unable to delete the directory. Thus, to remove a directory that contains other files or directories, use the following command ("-r" means doing it recursively; "-f" means suppressing prompts for approval to delete each of the files and it also forces removal despite errors such as non-existent files or directories)
<pre><code>rm -rf mydir</code></pre>

<h2>Copy the files but not subdirectories</h2>
Suppose you would like to copy a content of directory 1 to directory 2. However, you only want to copy files (and not directories) from directory 1. You can use the "cp" command as below. cp will not copy directories unless explicitly told to do so (i.e., use "-r" option). Note that cp expects the last parameter to be a single file name or directory. There really should be no wildcard * after the name of the target directory.
<pre><code>cp dir1/* dir2</code></pre>

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

<h2>Move running processes to background/bring running processes to foreground</h2>
I constantly need these two operations depending on whehter I put "&" in the end of a command. Here is <a href="http://www.kossboss.com/linux---move-running-to-process-nohup">a good reference</a> for the first operation. And <a href="http://unix.stackexchange.com/questions/45025/how-to-suspend-and-bring-a-background-process-to-foreground">this one</a> for the second operation.

<h2>Remove the first and the last line from file</h2>
The most efficient way I find out is to use "more" and "head/tail" (relatively lightweight) and then output the result to a file (instead of the standard out)
<pre><code>more lrrkPairsAtSite_8003.out | head -n -1 | tail -n +2 > lrrkPairsAtSite_8003_new.out</code></pre>

<h2>Split a large .gz file</h2>
The first command below would grabs the first 4000000 lines of the file and output it to another one. The second command splits a large .gz file (GenotypeChrom_09.gz) into smaller files each with 6004 lines. These smaller files will be named as "GenotypeChrom_09_partaa", "GenotypeChrom_09_partab", and so on.
<pre><code>
gunzip -c hugefile.txt.gz | head -n 4000000 > log.file
zcat GenotypeChrom_09.gz | split -l 6004 - GenotypeChrom_09_part
</code></pre>
The following command is an improvement to the second one above as it allows you to immediately zip the resultant files, which are often quite large too.
<pre><code>zcat biglogfile.gz | split -l 500000 --filter='gzip > $FILE.gz'</code></pre>
<b>UPDDATE</b>: Up till today (09/11/2015, Uh-oh, a sad date) I've gone through a 300GB+ file, 1TB memory, 70TB disk space limit, 72h cpu time, 80 cores, etc...So I no longer have the strong feelings described at the beginning of this post. To be honest, it even sounds a little bit naive to me now. Yesterday when my colleague Ruhi told me our IT group has granted her an exclusive 5TB storage space, my first response was "only 5TB?" Then I couldn't believe I actually said that...

<h2>Remotely transfer files using scp</h2>
The scp command allows you to copy files over ssh connections. This is pretty useful if you want to transport files between computers, for example to backup something. The scp command uses the ssh command and they are very much alike. However, there are some important differences.
The scp command can be used in three* ways: to copy from a (remote) server to your computer, to copy from your computer to a (remote) server, and to copy from a (remote) server to another (remote) server. In the third case, the data is transferred directly between the servers; your own computer will only tell the servers what to do. The first command below will transfer the file “examplefile” to the directory “/home/yourusername/” at the server “yourserver”, trying to get ssh acces with the username “yourusername”. The second command copy the file “/home/yourusername/examplefile” to the current directory on your own computer (indicated by the "." in the end), provided that the username and password are correct and that the file actually exists. The third command uses a "-r" option, which will copy the entire directory from the remote server to your current location. The last command copies a file from a (remote) server to another (remote) server. (Note: usually a request for password will pompt up.)
<pre><code>scp examplefile yourusername@yourserver:/home/yourusername/
scp yourusername@yourserver:/home/yourusername/examplefile .
scp -r yourusername@yourserver:/home/yourusername/exampledirectory .
scp yourusername@yourserver:/home/yourusername/examplefile yourusername2@yourserver2:/home/yourusername2/
</code></pre>

<h2>Change file/directory access permission</h2>
To change all the subdirectories to 755 (-rwxr-xr-x):
<pre><code>find . -type d -exec chmod 755 {} \;</code></pre>
To change all the files to 644 (-rw-r--r--):
<pre><code>find /opt/lampp/htdocs -type f -exec chmod 644 {} \;</code></pre>


<h2>References</h2>
<ul>
<li><a href="https://linuxacademy.com/blog/linux/ssh-and-scp-howto-tips-tricks/">SSH and SCP: Howto, tips & tricks</a></li>
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
<li><a href="http://unix.stackexchange.com/questions/112023/how-can-i-replace-a-string-in-a-files">How can I replace a string in a file(s)?</a></li>
</ul>
