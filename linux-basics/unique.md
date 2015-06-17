###UNIQUE Commands Examples:
Let's say we have an eight-line text file, myfile.txt, which contains the following text:
This is a line.
This is a line.
This is a line.
 
This is also a line.
This is also a line.
 
This is also also a line.
Here are several ways to run uniq on this file, and the output it creates:
####Only unique lines:
```
uniq myfile.txt
```
####Count Number of Occurrences using -c option like:
```
3 This is a line.
      1  
      2 This is also a line.
      1  
      1 This is also also a line.
```
```
uniq -c myfile.txt
```
####Print ONly duplicate lines:
```
uniq -d myfile.txt
```
###Only print unique lines:
```
uniq -u myfile.txt
```
#### Limit Comparison to ‘N’ characters using -w option:
This option restricts comparison to first specified ‘N’ characters only. For this example, use the following test2 input file:
```
$ cat test2
hi Linux
hi LinuxU
hi LinuxUnix
hi Unix
```
The following uniq command using option ‘w’ is compares the first 8 characters of lines in file, and then using ‘c’ option prints number of occurrences of lines of file:
```
$ uniq -c -w 8 testNew
  3 hi Linux
  1 hi Unix
```
#### Avoid Comparing first ‘N’ Characters using -s option
This option skips comparison of first specified ‘N’ characters. For this example, use the following test3 input file:
```
$ cat test3
aabb
xxbb
bbc
bbd
```
The following uniq command using option ‘s’ skips comparing first 2 characters of lines in file, and then using ‘D’ option prints all duplicate lines of file:
```
$ uniq -D -s 2 test3
aabb
xxbb
```
####Avoid Comparing first ‘N’ Fields using -f option:
This option skips comparison of first specified ‘N’ fields of lines in file:
```
$ cat test2
hi hello Linux
hi friend Linux
hi hello LinuxUnix
```
The following uniq command using option ‘f’ skips comparing first 2 fields of lines in file, and then using ‘D’ option prints all duplicate lines of file.
```
$ uniq -D -f 2 test2
hi hello Linux
hi friend Linux
```

