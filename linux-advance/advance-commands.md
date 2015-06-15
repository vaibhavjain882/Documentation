#### 1. Echo and Append something from local to remote linux machines:
```
for i in {125..128}; do ssh -t saurabhs@192.168.22.$i "echo '192.168.21.11    IRONMQ.bookmyshow.org' | sudo tee -a /etc/hosts";done

Note: redirection >> doesn't work with echo in this case and it requires tty shell also so avoid to use >>.
```
###AWK:
Awk is a scripting language which is used  for  processing or analyzing text files. Or we can say that awk is mainly used for grouping of data based on either a column or field , or on a set of columns. Mainly it's used for reporting data in a usefull manner. It also employs Begin and End Blocks to process the data.
```
awk 'pattern {action}' input-file > output-file

Lets take a input file with the following data

$ cat  awk_file
Name,Marks,Max Marks
Ram,200,1000
Shyam,500,1000
Ghyansham,1000
Abharam,800,1000
Hari,600,1000
Ram,400,1000

###Print all the lines from a file:
awk '{print;}' awk_file

###Print only Specific field like 2nd & 3rd after delimeter comma (,).
awk -F "," '{print $2, $3;}' awk_file

###Print the lines which matches the pattern:
awk '/Hari|Ram/' awk_file

###How do we find unique values in the first column of name:
 awk -F, '{a[$1];}END{for (i in a)print i;}' awk_file

###How to find the sum of data entry in a particular column:
awk -F, '$1=="Ram"{x+=$2;}END{print x}' awk_file    ##Answer 600

###How to find the  total of all numbers in a column:
awk -F"," '{x+=$2}END{print x}' awk_file       ### Answer 3500

###How to find the sum of individual group records:
awk -F, '{a[$1]+=$2;}END{for(i in a)print i”, “a[i];}' awk_file

###How to find the sum of all entries in second column  and append it to the end of the file.
awk -F"," '{x+=$2;y+=$3;print}END{print "Total,"x,y}' awk_file

###How to find the count of entries against every column based on the first column:
awk -F, '{a[$1]++;}END{for (i in a)print i, a[i];}' awk_file

###How to print only the first record of every group:
awk -F, '!a[$1]++' awk_file

###AWK Begin and End Block:

Syntax :
awk ‘BEGIN{awk initializing code}{actual AWK code}’END{ Action } filename.txt

Let us create a datafile with below contents:

Thomas 	 Manager 	 Sales 	         $5,000
Jason 	 Developer 	 Technology 	 $5,500
Sanjay 	 Sysadmin 	 Technology 	 $7,000
Nisha 	 Manager 	 Marketing 	 $9,500
Randy 	 DBA 	 	 Technology 	 $6,000

Actions specified in the BEGIN section will be executed before starts reading the lines from the input.
END actions will be performed after completing the reading and processing the lines from the input.

$ awk 'BEGIN {print "Name\tDesignation\tDepartment\tSalary";}
> {print $2,"\t",$3,"\t",$4,"\t",$NF;}
> END{print "Report Generated\n--------------";
> }' employee.txt


Name	Designation	Department	Salary
Thomas 	 Manager 	 Sales 	         $5,000
Jason 	 Developer 	 Technology 	 $5,500
Sanjay 	 Sysadmin 	 Technology 	 $7,000
Nisha 	 Manager 	 Marketing 	 $9,500
Randy 	 DBA 	 	 Technology 	 $6,000
Report Generated
--------------

###Print the list of employees in Technology department
awk '$4 ~/Technology/' employee.txt

### Print number of employees in Technology department:
awk 'BEGIN { count=0;}

checks if the department is Technology, if it is yes, in the Action, just increment the count variable, which was initialized with zero in the BEGIN section.

###How to change the Field Separator:

As we can see space is the field separator in the datafile , in the below example we will change field separator  from space to "|"
awk 'BEGIN{OFS="|"}{print $1,$2,$3,$4,$5}' datafile




```

