###top:
 it will show information like tasks, memory, cpu and swap. Press ‘q‘ to quit window.

#### Sorting with -O (Uppercase Letter ‘O’):
Press (Shift+O) to Sort field via field letter, for example press ‘a‘ letter to sort process with PID (Process ID).
then it will show something like this:
```
current sort field: K for windows 1:Def
.
.
.
.
Type any key to return to main top window with sorted PID order as shown in below screen. Press ‘q‘ to quit exit the window.
```
####Display Specific User Process:
```
top -u tecmint
```

####Highlight Running Process in Top:

Press ‘z‘ option in running top command will display running process in color which may help you to identified running process easily.

#### Shows Absolute Path of Processes:
Press ‘c‘ option in running top command, it will display absolute path of running process.

####Change Delay or Set ‘Screen Refresh Interval’ in Top:

By default screen refresh interval is 3.0 seconds, same can be change pressing ‘d‘ option in running top command and change it as desired.

####Kill running process with argument ‘k’
You can kill a process after finding PID of process by pressing ‘k‘ option in running top command without exiting from top window as shown below.


####Sort by CPU Utilisation
Press (Shift+P) to sort processes as per CPU utilization. See screenshot below

#### Renice a Process:
You can use ‘r‘ option to change the priority of the process also called Renice.

####Save Top Command Results:
Press (Shift+W) to save the running top command results under /root/.toprc

####Exit Top Command After Specific repetition:

Top output keep refreshing until you press ‘q‘. With below command top command will automatically exit after 10 number of repetition.
