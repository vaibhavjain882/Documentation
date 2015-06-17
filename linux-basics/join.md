### JOIN Commands Examples:
```
If we have a file, myfile1.txt, whose contents are:
```
1 India 
2 US 
3 Ireland 
4 UK 
5 Canada 
```
and another file, myfile2.txt, whose contents are:
```
1 NewDelhi 
2 Washington 
3 Dublin 
4 London 
5 Toronto 
```
The common fields are the fields which begin with the same number. We can join the contents using the following command:
```
join myfile1.txt myfile2.txt
```
which outputs the following to standard output:
1 India NewDelhi 
2 US Washington 
3 Ireland Dublin 
4 UK London 
5 Canada Toronto 

####Write a join command to join two files on the first field?
```
> cat emp.txt
10 mark
10 steve
20 scott
30 chris
> cat dept.txt
10 hr
20 finance
30 db
```
```
> join emp.txt dept.txt
10 mark hr
10 steve hr
20 scott finance
30 chris db
```

####Write a join command to join the two files? Here use the second field from the first file and the first field from the second file to join:
```
> cat emp.txt
mark 10 1
steve 10 1
scott 20 2
chris 30 3
> cat dept.txt
10 hr 1
20 finance 2
30 db 3
```
```
> join -1 2 -2 1 emp.txt dept.txt
10 mark 1 hr 1
10 steve 1 hr 1
20 scott 2 finance 2
30 chris 3 db 3
```
Here -1 2 specifies the second field from the first file (emp.txt) and -2 1 specifies the first field from the second file (dept.txt)
You can also see that the two files can also be joined on the third filed. As the both the files have the matching join field, you can use the j option in the join command:
```
> join -j 3 emp.txt dept.txt
1 mark 10 10 hr
1 steve 10 10 hr
2 scott 20 20 finance
3 chris 30 30 db
```
####Write a join command to select the required fields from the input files in the output? Select first filed from first file and second field from second file in the output:
By default, the join command prints all the fields from both the files (except the join field is printed once). We can choose what fields to be printed on the terminal with the -o option. We will use the same files from the above example:
```
> join -o 1.1 2.2 -1 2 -2 1 emp.txt dept.txt
mark hr
steve hr
scott finance
chris db
```
Here 1.1 means in the first file select the first field. Similarly, 2.2 means in the second file select the second field

####Write a command to join two delimited files? Here the delimiter is colon (:)
So far we have joined files with space delimiter. Here we will see how to join files with a colon as delimiter. Consider the below two files:
```
> cat emp.txt
mark:10
steve:10
scott:20
chris:30
> cat dept.txt
10:hr
20:finance
30:db
```
The -t option is used to specify the delimiter. The join command for joining the files is:
```
> join -t: -1 2 -2 1 emp.txt dept.txt
10:mark:hr
10:steve:hr
20:scott:finance
30:chris:db
```
####  Write a command to ignore case when joining the files:
If the join fields are in different cases, then the join will not be performed properly. To ignore the case in join use the -i option.
```
> cat emp.txt
mark,A
steve,a
scott,b
chris,C
> cat dept.txt
a,hr
B,finance
c,db

> join -t, -i -1 2 -2 1 emp.txt dept.txt
A,mark,hr
a,steve,hr
b,scott,finance
C,chris,db
```

#### Write a join command to print the lines which do not match the values in joining fields:
By default the join command prints only the matched lines from both the files which means prints the matched lines that passed the join condition. We can use the -a option to print the non-matched lines:
```
> cat P.txt
A 1
B 2
C 3
> cat Q.txt
B 2
C 3
D 4

Print non pairable lines from first file.

> join -a 1 P.txt Q.txt
A 1
B 2 2
C 3 3

Print non pairable lines from second file.

> join -a 2 P.txt Q.txt
B 2 2
C 3 3
D 4

Print non pairable lines from both file.

> join -a 1 -a 2 P.txt Q.txt
A 1
B 2 2
C 3 3
D 4
```

