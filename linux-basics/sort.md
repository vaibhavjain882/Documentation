### Sort Commands Examples:
By default, the rules for sorting are:
1. lines starting with a number will appear before lines starting with a letter;
2. lines starting with a letter that appears earlier in the alphabet will appear before lines starting with a letter that appears later in the alphabet;
3. lines starting with a lowercase letter will appear before lines starting with the same letter in uppercase.

####Syntax:
```
sort [OPTION]... [FILE]...
sort [OPTION]... --files0-from=F
```
####Examples:

Let's say you have a file, data.txt, which contains the following ASCII text:
```
apples
Apples
oranges
pears
kiwis
bananas
```

####To sort the lines in this file alphabetically, use the following command:
```
sort data.txt
```
####You can also use the built-in sort option -o, which allows you to specify an output file:
```
sort -o output.txt data.txt
```
#### Sorting In Reverse Order
```
sort -r data.txt
You can use -f/--ignore-case flag to ignore letter cases.
```
NOTE: if you are using the join command in conjunction with sort, be aware that there is a known incompatibility between the two programs — unless you define the locale. If you are using join and sort to process the same input, it is highly recommended that you set LC_ALL to C, which will standardize the localization used by all programs.

####Checking For Sorted Order:
```
sort -c data.txt
```
####Sorting Multiple Files Using The Output Of find:
It would be nice if we could use this output to tell the sort command, "sort the data in any files found by find as if they were all one big file." The problem with the standard find output is, even though it's easy for humans to read, it can cause problems for other programs that need to read it in. This is because filenames can include non-standard characters, so in some cases, this format will be read incorrectly by another program.

The correct way to format find's output to be used as a file list for another program is to use the -print0 option when running find. This terminates each filename with the NUL character (ASCII character number zero), which is universally illegal to use in filenames. This makes things easier for the program reading the file list, since it knows that any time it sees the NUL character, it can be sure it's at the end of a filename.
```
find -name "data?.txt" -print0
```
Be careful how you word the find command. It's important to specify -print0 last; find needs this to be specified after the other options.

Okay, but how do we tell sort to read this file list and sort the contents of all those files?

One way to do it is to pipe the find output to sort, specifying the --files0-from option in the sort command, and specify the file as a dash ("-"), which will read from the standard input. Here's what the command will look like:
```
find -name "data?.txt" -print0 | sort --files0-from=-
```
####Comparing Only Selected Fields Of Data:
For instance, if you have an input file data.txt With the following data:
```
01 Joe
02 Marie
03 Albert
04 Dave
```
if you want to sort based on the names, you can use the following command:
```
sort -k 2 data.txt
```
This command will sort the second field, and ignore the first. (The "k" in "-k" stands for "key" — we are defining the "sorting key" used in the comparison.)

So, let's say our input file data.txt contains the following data:
```
01 Joe Sr.Designer
02 Marie Jr.Developer
03 Albert Jr.Designer
04 Dave Sr.Developer
```
we can sort by seniority if we specify the third field as the sort key:
```
sort -k 3 data.txt
```
we can ignore the first three characters of the third field, and sort solely based on title, ignoring seniority:
```
sort -k 3.3 data.txt
```
We can also specify where in the line to stop comparing. If we sort based on only the third-through-fifth characters of the third field of each line, like this:
```
sort -k 3.3,3.5 data.txt
```
####Using sort And join Together:
sort can be especially useful when used in conjunction with the join command. Normally join will join the lines of any two files whose first field match. Let's say you have two files, file1.txt and file2.txt. file1.txt contains the following text:
3       tomato
1       onion
4       beet
2       pepper

and file2.txt contains the following:
4       orange
3       apple
1       mango
2       grapefruit
If you'd like sort these two files and join them, you can do so all in one command if you're using the bash command shell, like this:
```
join <(sort file1.txt) <(sort file2.txt)
```
Here, the sort commands in parentheses are each executed, and their output is redirected to join, which takes their output as standard input for its first and second arguments; it is joining the sorted contents of both files. This will be your result:
1 onion mango
2 pepper grapefruit
3 tomato apple
4 beet orange

####sort file on the basis of 1st field:
```
$ sort -t"," -k1,1 file 
```
This is being more explicit. '-t' option is used to provide the delimiter in case of files with delimiter. '-k' is used to specify the keys on the basis of which the sorting has to be done. The format of '-k' is : '-km,n' where m is the starting key and n is the ending key. In other words, sort can be used to sort on a range of fields just like how the group by in sql does. In our case, since the sorting is on the 1st field alone, we speciy '1,1'. Similarly, if the sorting is to be done on the basis of first 3 fields, it will be: '-k 1,3'. 
NOTE: For a file which has fields delimited by a space or a tab, there is no need to specify the "-t" option since the white space is the delimiter by default in sort.

####sorting file on the basis of 2nd field , numerically: 
```
$ sort -t"," -k2n,2 file
```
####Remove duplicates from the file based on 1st field:
```
$ sort -t"," -k1,1 -u file
```
#### Sort the file numerically on the 2nd field in reverse order:
```
$ sort -t"," -k2nr,2 file
```
####sort the file alphabetically on the 1st field, numerically on the 2nd field: 
```
$ sort -t"," -k1,1 -k2n,2 file
```
####sort a file based on the 1st and 2nd field, and numerically on 3rd field on  a file containing 5 columns:
```
$ sort -t"," -k1,2 -k3n,3 file
```

