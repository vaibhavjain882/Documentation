###DIFF Commands Examples:
diff analyzes two files and prints the lines that are different. Essentially, it outputs a set of instructions for how to change one file in order to make it identical to the second file.
It does not actually change the files; however, it can optionally generate a script (with the -e option) for the program ed (or ex which can be used to apply the changes.
Let's say we have two files, file1.txt and file2.txt:
If file1.txt contains the following four lines of text:
```
I need to buy apples.
I need to run the laundry.
I need to wash the dog.
I need to get the car detailed.
```
and file2.txt contains these four lines:
```
I need to buy apples.
I need to do the laundry.
I need to wash the car.
I need to get the dog detailed.
```
then we can use diff to automatically display for us which lines differ between the two files with this command:
```
diff file1.txt file2.txt
```
and the output will be:
```
2,4c2,4
< I need to run the laundry.
< I need to wash the dog.
< I need to get the car detailed.
---
> I need to do the laundry.
> I need to wash the car.
> I need to get the dog detailed.
```
Diff is telling you how to change the first file to make it match the second file:
The first line of the diff output will contain:
```
line numbers corresponding to the first file,
a letter (a for add, c for change, or d for delete), and
line numbers corresponding to the second file.
```
In our output above, "2,4c2,4" means: "Lines 2 through 4 in the first file need to be changed in order to match lines 2 through 4 in the second file." It then tells us what those lines are in each file:
```
Lines preceded by a < are lines from the first file;
lines preceded by > are lines from the second file.
```
NOTE: The three dashes ("---") merely separate the lines of file 1 and file 2.

#### Another example for adding:
```
$cat file1.txt
I need to go to the store.
I need to buy some apples.
When I get home, I'll wash the dog.

$cat file2.txt
I need to go to the store.
I need to buy some apples.
Oh yeah, I also need to buy grated cheese.
When I get home, I'll wash the dog.
```
Coomand:
```
diff file1.txt file2.txt
```
Output:
2a3
Here, the output is telling us "After line 2 in the first file, a line needs to be added: line 3 from the second file." It then shows us what that line is.

#### Example for deletion:
```
file1:
I need to go to the store.
I need to buy some apples.
When I get home, I'll wash the dog.
I promise.

file2:
I need to go to the store.
I need to buy some apples.
When I get home, I'll wash the dog.
```
Command:
```
diff file1.txt file2.txt
```
Output:
4d3
Here, the output is telling us "You need to delete line 4 in the first file so that both files sync up at line 3." It then shows us the contents of the line that needs to be deleted.

### GNU Diff:
```
file1.txt:
apples
oranges
kiwis
carrots

file2.txt:
apples
kiwis
carrots
grapefruits
```
Commands:
```
diff -c file1.txt file2.txt
```
Output:
```
*** file1.txt   2014-08-21 17:58:29.764656635 -0400
--- file2.txt   2014-08-21 17:58:50.768989841 -0400
***************
*** 1,4 ****
  apples
- oranges
  kiwis
  carrots
--- 1,4 ----
  apples
  kiwis
  carrots
+ grapefruits
```
#### Unified Diff:

```
file1.txt:
apples
oranges
kiwis
carrots

file2.txt:
apples
kiwis
carrots
grapefruits
```
Command:
```
diff -u file1.txt file2.txt
```
Output:
```
--- file1.txt   2014-08-21 17:58:29.764656635 -0400
+++ file2.txt   2014-08-21 17:58:50.768989841 -0400
@@ -1,4 +1,4 @@
 apples
-oranges
 kiwis
 carrots
```

####Finding differences in Directory Contents:


```
diff dir1 dir2
```
Output:
```
Only in dir1: tab2.gif
Only in dir1: tab3.gif
Only in dir1: tab4.gif
Only in dir1: tape.htm
Only in dir1: tbernoul.htm
Only in dir1: tconner.htm
Only in dir1: tempbus.psd
```

####Usefull Diff Options:
```
-b: Ignore any changes which only change the amount of whitespace (such as spaces or tabs).

-w: Ignore whitespace entirely.

-B: Ignore blank lines when calculating differences.
-y: Display output in two columns.
--suppress-common-lines: Do not output lines common between the two files.
-r: Recursively compare any subdirectories found.
-I: ignore changes whose lines all match regular expression RE.
```

#### Use Diff to create an editing script:
The -e option tells diff to output a script, which can be used by the editing programs ed or ex, that contains a sequence of commands. The commands are a combination of c (change), a (add), and d (delete) which, when executed by the editor, will modify the contents of file1 (the first file specified on the diff command line) so that it matches the contents of file2 (the second file specified).

Let's say we have two files with the following contents:

file1.txt:

Once upon a time, there was a girl named Persephone.
She had black hair.
She loved her mother more than anything.
She liked to sit outside in the sunshine with her cat, Daisy.
She dreamed of being a painter when she grew up.

file2.txt

Once upon a time, there was a girl named Persephone.
She had red hair.
She loved chocolate chip cookies more than anything.
She liked to sit outside in the sunshine with her cat, Daisy.
She would look up into the clouds and dream of being a world-famous baker.

Coomand:
```
diff -e file1.txt file2.txt > my-ed-script.txt

It will produce a script to create a file identical to file2.txt from the contents of file1.txt:
```
This will not display anything on the screen (unless there is an error); instead, the output is redirected to the file my-ed-script.txt. If my-ed-script.txt doesn't exist, it will be created; if it exists already, it will be overwritten.
If we now check the contents of my-ed-script.txt with the cat command:
There's still one thing missing, though: we need the script to tell ed to actually write the file. All that's missing from the script is the w command, which will write the changes. We can add this to our script by echoing the letter "w" and using the >> operator to add it to our file. (The >> operator is similar to the > operator. It redirects output to a file, but instead of overwriting the destination file, it appends to the end of the file.) The command looks like this:
```
echo "w" >> my-ed-script.txt
```
We can issue this script to ed with the following command, telling it to overwrite our original file. The dash ("-") tells ed to read from the standard input, and the < operator directs our script to that input. In essence, the system enters whatever is in our script as input to the editing program. The command looks like this:
```
ed - file1.txt < my-ed-script.txt
```
we can see that file1.txt now matches file2.txt exactly.
#### cmp: compares files by byte by byte.
#### comm: compare sorted file line by line
####merge: I always use merge tool instead of this command
