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
###Sed commands Examples:

Sed is a Stream Editor used for modifying the files in unix (or linux). Whenever you want to make changes to the file automatically, sed comes in handy to do this:
```
1. Replacing or substituting string:
sed 's/unix/linux/' file.txt

Here the "s" specifies the substitution operation. The "/" are delimiters. The "unix" is the search pattern and the "linux" is the replacement string.

By default, the sed command replaces the first occurrence of the pattern in each line and it won't replace the second, third...occurrence in the line.

2. Replacing the nth occurrence of a pattern in a line:
Use the /1, /2 etc flags to replace the first, second occurrence of a pattern in a line. The below command replaces the second occurrence of the word "unix" with "linux" in a line:

$ sed 's/unix/linux/2' file.txt

3. Replacing all the occurrence of the pattern in a line.

sed 's/unix/linux/g' file.txt

4. Replacing from nth occurrence to all occurrences in a line:

$ sed 's/unix/linux/3g' file.txt

5. Changing the slash (/) delimiter:

$ sed 's/http:\/\//www/' file.txt

6. Using & as the matched string:

There might be cases where you want to search for the pattern and replace that pattern by 
adding some extra characters to it. In such cases & comes in handy. The & represents the matched string.

$ sed 's/unix/{&}/' file.txt

7. Using \1,\2 and so on to \9
>sed 's/\(unix\)/\1\1/' file.txt

Output:unixunix is great os. unix is opensource. unix is free os.

The parenthesis needs to be escaped with the backslash character. Another example is if you 
want to switch the words "unixlinux" as "linuxunix", the sed command is:

$ sed 's/\(unix\)\(linux\)/\2\1/' file.txt

Output:linuxunix which one you choose

Another example is switching the first three characters in a line:

$ sed 's/^\(.\)\(.\)\(.\)/\3\2\1/' file.txt

inux is great os. unix is opensource. unix is free os.

8. Duplicating the replaced line with /p flag

$ sed 's/unix/linux/p' file.txt

linuxlinux which one you choose.

9. Printing only the replaced lines:

sed -n 's/unix/linux/p' file.txt

10. Running multiple sed commands:
You can run multiple sed commands by piping the output of one sed command as input to another sed command.

$ sed 's/unix/linux/' file.txt| sed 's/os/system/'

Sed provides -e option to run multiple sed commands in a single sed command. The above output can be achieved in a single sed command as shown below:

$ sed -e 's/unix/linux/' -e 's/os/system/' file.txt

11. Replacing string on a specific line number:

sed '3 s/unix/linux/' file.txt

12. Replacing string on a range of lines:

$ sed '1,3 s/unix/linux/' file.txt

Here $ indicates the last line in the file. So the sed command replaces the text from second line to last line in the file:

$ sed '2,$ s/unix/linux/' file.txt

13. Replace on a lines which matches a pattern:

You can specify a pattern to the sed command to match in a line. If the pattern match occurs
then only the sed command looks for the string to be replaced and if it finds, then the sed 
command replaces the string:

$ sed '/linux/ s/unix/centos/' file.txt

14. Deleting lines:
>sed '2 d' file.txt
>sed '5,$ d' file.txt

15. Duplicating lines:
>sed 'p' file.txt

16. If you have a large number of sed commands, you can put them into a file and use:

$ sed -f sedscript <old >new

17. the quit command:

There is one more simple command that can restrict the changes to a set of lines. It is the "q" command: quit. the third :
way to duplicate the head command is:

sed '11 q'

which quits when the eleventh line is reached.

18. Writing a file with 'w' command:

You may remember that the substitute command can write to a file. Here again is the example that will only write lines
that start with an even number (and followed by a space):

sed -n 's/^[0-9]*[02468] /&/w even' <file 

only one space must follow the command. Anything else will be considered part of the file name. The "w" 
command also has the same limitation as the "w" flag: only 10 files can be opened in sed:

19. Reading a file with 'r' command:

sed '/INCLUDE/ r file' <in >out
 The above will insert a file after the line with the word "INCLUDE:"

The following will append the file "end" at the end of the file (address "$)." 

20. Find blank lines and commmented lines:

grep -vE "^$|^#|^ #"

where:
-v: show content except which we r searching
-E: for multiple searching

```
#### Get Public IP of a Linux Host:
```
dig +short myip.opendns.com @resolver1.opendns.com
```
####Find serial number of linux box:
```
dmidecode -s system-serial-number
```