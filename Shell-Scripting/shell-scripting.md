###Bash Cheat Sheet:

####Checking files:
```
-r file     #Check if file is readable.
-w file     #Check if file is writable.
-x file     #Check if we have execute access to file.
-f file     #Check if file is an ordinary file (not a directory, a device file, etc.)
-s file     #Check if file has size greater than 0.
-d file     #Check if file is a directory.
-e file     #Check if file exists.  Is true even if file is a directory.
```

Example:
```
if [ -s file ]
then
    #such and such
fi
```

####Checking strings:
```
s1 = s2     #Check if s1 equals s2.
s1 != s2    #Check if s1 is not equal to s2.
-z s1       #Check if s1 has size 0.
-n s1       #Check if s2 has nonzero size.
s1          #Check if s1 is not the empty string.

Example:

if [ $myvar = "hello" ] ; then
echo "We have a match"
fi
```

####Checking numbers: 

```
n1 -eq n2      #Check to see if n1 equals n2.
n1 -ne n2      #Check to see if n1 is not equal to n2.
n1 -lt n2      #Check to see if n1 < n2.
n1 -le n2      #Check to see if n1 <= n2.
n1 -gt n2      #Check to see if n1 > n2.
n1 -ge n2      #Check to see if n1 >= n2.

Example:

if [ $# -gt 1 ]
then
    echo "ERROR: should have 0 or 1 command-line parameters"
fi
```

####Boolean operators:
```
!     #not
-a    #and
-o    #or

Example:

if [ $num -lt 10 -o $num -gt 100 ]
then
    echo "Number $num is out of range"
elif [ ! -w $filename ]
then
    echo "Cannot write to $filename"
fi

NOTE: ifs can be nested. For example:

if [ $myvar = "y" ]
then
    echo "Enter count of number of items"
    read num
    if [ $num -le 0 ]
    then
        echo "Invalid count of $num was given"
    else
#... do whatever ...
fi
fi

The above example also illustrates the use of read to read a string from the keyboard and place it into a shell variable. 
Also note that most UNIX commands return a true (nonzero) or false (0) in the shell variable status to indicate whether they succeeded or not.
This return value can be checked. At the command line echo $status. In a shell script use something like this:

if grep -q shell bshellref
then
    echo "true"
else
    echo "false"
fi

Note that -q is the quiet version of grep. It just checks whether it is true that the string shell occurs in the file bshellref. It does not print the matching lines like grep would otherwise do.
```

####I/O Redirection:
```
pgm > file     #Output of pgm is redirected to file.
pgm < file     #Program pgm reads its input from file.
pgm >> file    #Output of pgm is appended to file.
pgm1 | pgm2    #Output of pgm1 is piped into pgm2 as the input to pgm2.
n > file       #Output from stream with descriptor n redirected to file.
n >> file      #Output from stream with descriptor n appended to file.
n >& m         #Merge output from stream n with stream m.
n <& m         #Merge input from stream n with stream m.
<< tag         #Standard input comes from here through next tag at start of line.

Note that file descriptor 0 is normally standard input, 1 is standard output, and 2 is standard error output.

#### Shell Built-in Variables:
```
$0             #Name of this shell script itself.
$1             #Value of first command line parameter (similarly $2, $3, etc)
$#             #In a shell script, the number of command line parameters.
$*             #All of the command line parameters.
$-             #Options given to the shell.
$?             #Return the exit status of the last command.
$$             #Process id of script (really id of the shell running the script)
```

####Pattern Matching:

```
*              #Matches 0 or more characters.
?              #Matches 1 character.
[AaBbCc]       #Example: matches any 1 char from the list.
[^RGB]         #Example: matches any 1 char not in the list.
[a-g]          #Example: matches any 1 char from this range.
```

####Quoting:
```
\c             #Take character c literally.
`cmd`          #Run cmd and replace it in the line of code with its output.
"whatever"     #Take whatever literally, after first interpreting $, `...`, \
'whatever'     #Take whatever absolutely literally.

 Example:

match=`ls *.bak`        #Puts names of .bak files into shell variable match.
echo \*                 #Echos * to screen, not all filename as in:  echo *
echo '$1$2hello'        #Writes literally $1$2hello on screen.
echo "$1$2hello"        #Writes value of parameters 1 and 2 and string hello.
```

####Grouping:
Parentheses may be used for grouping, but must be preceded by backslashes since parentheses normally have a different meaning to the shell (namely to run a command or commands in a subshell). For example, you might use:
```
if test \( -r $file1 -a -r $file2 \) -o \( -r $1 -a -r $2 \)
then
    #do whatever
fi
```

####Case Statemnet:
Here is an example that looks for a match with one of the characters a, b, c. If $1 fails to match these, it always matches the * case. A case statement can also use more advanced pattern matching.
```
case "$1" in
    a) cmd1 ;;
    b) cmd2 ;;
    c) cmd3 ;;
    *) cmd4 ;;
esac
```

####Loops:
Bash supports loops written in a number of forms,
```
for arg in [list]
do
    echo $arg
done

for arg in [list] ; do
    echo $arg
done
```
You can supply [list] directly
```
NUMBERS="1 2 3"
for number in `echo $NUMBERS`
do
  echo $number 
done

for number in $NUMBERS
do
  echo -n $number
done

for number in 1 2 3
do
  echo -n $number
done
```
If [list] is a glob pattern then bash can expand it directly, for example:
```
for file in *.tar.gz
do
    tar -xzf $file
done
```
You can also execute statements for [list], for example:
```
for x in `ls -tr *.log`
do
   cat $x >> biglog
done
```

#### Shell Arithmetic:
In the original Bourne shell arithmetic is done using the expr command as in:
```
result=`expr $1 + 2`
result2=`expr $2 + $1 / 2`
result=`expr $2 \* 5`               #note the \ on the * symbol
```
With bash, an expression is normally enclosed using [ ] and can use the following operators, in order of precedence:
```
* / %       #(times, divide, remainder)
+ -         #(add, subtract)
< > <= >=   #(the obvious comparison operators)
== !=       #(equal to, not equal to)
&&          #(logical and)
||          #(logical or)
=           #(assignment)
```
Arithmetic is done using long integers. Example:
```
result=$[$1 + 3]
```
####Order of Interpretation:
The bash shell carries out its various types of interpretation for each line in the following order:
```
brace expansion         (see a reference book)
~ expansion             (for login ids)
parameters              (such as $1)
variables               (such as $var)
command substitution    (Example:  match=`grep DNS *` )
arithmetic              (from left to right)
word splitting
pathname expansion      (using *, ?, and [abc] )
```
#### Other Shell Features:
```
$var           #Value of shell variable var.
${var}abc      #Example: value of shell variable var with string abc appended.
#              #At start of line, indicates a comment.
var=value      #Assign the string value to shell variable var.
cmd1 && cmd2   #Run cmd1, then if cmd1 successful run cmd2, otherwise skip.
cmd1 || cmd2   #Run cmd1, then if cmd1 not successful run cmd2, otherwise skip.
cmd1; cmd2     #Do cmd1 and then cmd2.
cmd1 & cmd2    #Do cmd1, start cmd2 without waiting for cmd1 to finish.
(cmds)         #Run cmds (commands) in a subshell.
```
####Functions:
```
fname(){
commands
}


Call it by using the following syntax: fname

Or, create a function that accepts arguments:

fname2 (arg1,arg2...argN){
commands
}

And call it with: fname2 arg1 arg2 ... argN

you can retutrn values from a function.
fnReturnFunction()
{
        echo $1$2
}
value=$(fnReturnFunction "holly" "wood")
```

#### Signal and traps:

Signals are software interrupts sent to a program to indicate that an important event has occurred. The events can vary from user requests to illegal memory access errors. Some signals, such as the interrupt signal, indicate that a user has asked the program to do something that is not in the usual flow of control.

```
SIGHUP	1	Hang up detected on controlling terminal or death of controlling process
SIGINT	2	Issued if the user sends an interrupt signal (Ctrl + C).
SIGQUIT	3	Issued if the user sends a quit signal (Ctrl + D).
SIGFPE	8	Issued if an illegal mathematical operation is attempted
SIGKILL	9	If a process gets this signal it must quit immediately and will not perform any clean-up operations
SIGALRM	14	Alarm Clock signal (used for timers)
SIGTERM	15	Software termination signal (sent by kill by default).
```
kill -l gives you the whole list of signals.

####Sending Signals:
```
The other common method for delivering signals is to use the kill command whose syntax is as follows:

$ kill -signal pid
Here signal is either the number or name of the signal to deliver and pid is the process ID that the signal should be sent to. For Example:

$ kill -1 1001
Sends the HUP or hang-up signal to the program that is running with process ID 1001. To send a 
kill signal to the same process use the folloing command:

$ kill -9 1001
This would kill the process running with process ID 1001.

```
#### Trapping Signals:

```
When you press the Ctrl+C or Break key at your terminal during execution of a shell program, normally that program is immediately terminated, and your command prompt returned. This may not always be desirable. For instance, you may end up leaving a bunch of temporary files that won't get cleaned up.

Trapping these signals is quite easy, and the trap command has the following syntax:

$ trap commands signals
Here command can be any valid Unix command, or even a user-defined function, and signal can be a list of any number of signals you want to trap.

There are three common uses for trap in shell scripts:

Clean up temporary files

Ignore signals
```
####Cleaning Up Temporary Files:
As an example of the trap command, the following shows how you can remove some files and then exit if someone tries to abort the program from the terminal:

$ trap "rm -f $WORKDIR/work1$$ $WORKDIR/dataout$$; exit" 2
From the point in the shell program that this trap is executed, the two files work1$$ and dataout$$ will be automatically removed if signal number 2 is received by the program.

So if the user interrupts execution of the program after this trap is executed, you can be assured that these two files will be cleaned up. The exit command that follows the rm is necessary because without it execution would continue in the program at the point that it left off when the signal was received.

Signal number 1 is generated for hangup: Either someone intentionally hangs up the line or the line gets accidentally disconnected.

You can modify the preceding trap to also remove the two specified files in this case by adding signal number 1 to the list of signals:

$ trap "rm $WORKDIR/work1$$ $WORKDIR/dataout$$; exit" 1 2
Now these files will be removed if the line gets hung up or if the Ctrl+C key gets pressed.

The commands specified to trap must be enclosed in quotes if they contain more than one command. Also note that the shell scans the command line at the time that the trap command gets executed and also again when one of the listed signals is received.

So in the preceding example, the value of WORKDIR and $$ will be substituted at the time that the trap command is executed. If you wanted this substitution to occur at the time that either signal 1 or 2 was received you can put the commands inside single quotes:

$ trap 'rm $WORKDIR/work1$$ $WORKDIR/dataout$$; exit' 1 2

####Ignoring Signals:
```
If the command listed for trap is null, the specified signal will be ignored when received. For example, the command:

$ trap '' 2
Specifies that the interrupt signal is to be ignored. You might want to ignore certain signals when performing some operation that you don't want interrupted. You can specify multiple signals to be ignored as follows:

$ trap '' 1 2 3 15
Note that the first argument must be specified for a signal to be ignored and is not equivalent to writing the following, which has a separate meaning of its own:

$ trap  2
If you ignore a signal, all subshells also ignore that signal. However, if you specify an action to be taken on receipt of a signal, all subshells will still take the default action on receipt of that signal.
```
####Resetting Traps:
```
After you've changed the default action to be taken on receipt of a signal, you can change it back again with trap if you simply omit the first argument; so

$ trap 1 2
```
####Resetting Traps:
```
After you've changed the default action to be taken on receipt of a signal, you can change it back again with trap if you simply omit the first argument; so

$ trap 1 2
resets the action to be taken on receipt of signals 1 or 2 back to the default.
```
#### Exporting Variables:

```
You export shell variables using the following syntax:

 
export VAR
 
### Assign value before exporting ##
export VAR=value
 
OR

 
VAR=value
export VAR
 
The export command will marks each VAR for automatic export to the environment of subsequently executed commands i.e. 
make the local shell variable VAR global. To make the local shell variable called PATH type the following:

### export PATH ###
export PATH=$PATH:/usr/local/bin
echo "$PATH"
 
Set a new EDITOR variable:

 
export EDITOR=/usr/bin/vim
 
You need to add export statements to ~/.bash_profile or ~/.profile or /etc/profile file. This will export variables permanently:
$ vi ~/.bash_profile

Sample file

 
PATH=$PATH:$HOME/bin
export PATH
 
# set vim as a text editor
export EDITOR=/usr/bin/bin
 
# set colorful prompt 
export PS1='\[\e[1;32m\][\u@\h \W]\$\[\e[0m\] '
 
# set java_home
export JAVA_HOME=/usr/local/jdk
 
To see all a list of all exported variables and functions, enter:

 
export -p
```

####EVAL Command:
```
eval "$1" executes the command in the current script. It can set and use shell variables from the current script, 
set environment variables for the current script, set and use functions from the current script, set the current 
directory, umask, limits and other attributes for the current script, and so on. bash "$1" executes the command in 
a completely separate script, which inherits environment variables, file descriptors and other process environment 
(but does not transmit any change back) but does not inherit internal shell settings (shell variables, functions, options, traps, etc.).

There is another way, (eval "$1"), which executes the command in a subshell: it inherits everything from 
the calling script but does not transmit any change back.

For example, assuming that the variable dir isn't exported and $1 is cd "$foo"; ls, then:

cd /starting/directory; foo=/somewhere/else; eval "$1"; pwd lists the content of /somewhere/else and prints /somewhere/else.
cd /starting/directory; foo=/somewhere/else; (eval "$1"); pwd lists the content of /somewhere/else and prints /starting/directory.
cd /starting/directory; foo=/somewhere/else; bash -c "$1"; pwd lists the content of /starting/directory 
(because cd "" doesn't change the current directory) and prints /starting/directory.
```

