###Paste Commands Examples:
paste command, by definition of man page, is used to merge lines of files. It is very useful for merging a single file and also for merging set of files as well. This article is divided into 2 parts:
paste command examples for single file handling and paste command examples for multiple files handling.
Let us consider a file with the sample contents as below: 
```
$ cat file1 
Linux 
Unix 
Solaris 
HPUX 
AIX
```

1. paste command without any options is as good as the cat command when operated on a single file. 
```
$ paste file1
Linux
Unix
Solaris
HPUX
AIX
```
2. Join all lines in a file: 
```
$ paste -s file1
Linux   Unix    Solaris HPUX    AIX
```
-s option of paste joins all the lines in a file. Since no delimiter is specified, default delimiter tab is used to separate the columns. 

3. Join all lines using the comma delimiter: 
```
$ paste -d, -s file1
Linux,Unix,Solaris,HPUX,AIX
```
-d option is used to specify the delimiter. Using this -d and -s combination, all the lines in the file get merged into a single line.

 4. Merge a file by pasting the data into 2 columns: 
```
$ paste - - < file1
Linux   Unix
Solaris HPUX
AIX
```
The '-' reads a line from the standard input. Two '-' reads 2 lines and pastes them side by side.

5.Merge a file by pasting the data into 2 columns using a colon separator: 
```
$ paste -d':' - - < file1
Linux:Unix
Solaris:HPUX
AIX:
```
This is same as joining every 2 lines in a file.

6. Merge a file by pasting the file contents into 3 columns: 
```
$ paste - - - < file1
Linux   Unix    Solaris
HPUX    AIX
```

7. Merge a file into 3 columns using 2 different delimiters: 
```
$ paste -d ':,' - - - < file1
Linux:Unix,Solaris
HPUX:AIX,
```
The -d option can take multiple de-limiters. The 1st and 2nd columns is separated by ':', whereas the 2nd and 3rd are separated by a ','.

 paste command with multiple files: 

 Let us consider a file, file2, with the following contents: 
```
$ cat file2
Suse
Fedora
CentOS
OEL
Ubuntu
```

8. paste contents of 2 files side by side. 
```
$ paste file1 file2
Linux   Suse
Unix    Fedora
Solaris CentOS
HPUX    OEL
AIX     Ubuntu
```
paste command is used in scenarios to merge multiple files side by side. As shown above, the file contents are pasted side by side.

9. paste contents of 2 files side by side with a comma separator: 
```
$ paste -d, file1 file2
Linux,Suse
Unix,Fedora
Solaris,CentOS
HPUX,OEL
AIX,Ubuntu
```

10. paste command can take standard input in case of multiple files too: 
```
$ cat file2 | paste -d, file1 -
Linux,Suse
Unix,Fedora
Solaris,CentOS
HPUX,OEL
AIX,Ubuntu
```
Like this as well: 
```
$ cat file1 | paste -d, - file2
Linux,Suse
Unix,Fedora
Solaris,CentOS
HPUX,OEL
AIX,Ubuntu
```
One more: 

```
$ cat file1 file2 | paste -d, - -
Linux,Unix
Solaris,HPUX
AIX,Suse
Fedora,CentOS
OEL,Ubuntu
```

11. Read lines in both the files alternatively: 
```
$ paste -d'\n' file1 file2
Linux
Suse
Unix
Fedora
Solaris
CentOS
HPUX
OEL
AIX
Ubuntu
```
Using the newline character as the delimiter, we can read 2 files line by line alternatively.
