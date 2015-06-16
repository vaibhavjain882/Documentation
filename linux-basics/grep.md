Search for a given string in a file (case in-sensitive search).
$ grep -i "the" demo_file

Print the matched line, along with the 3 lines after it.
grep -A 3 -i "example" demo_text

Search for a given string in all files recursively
grep -r "ramesh" *

Displaying lines before/after/around the match using grep -A, -B and -C.

Checking for full words, not for sub-strings using grep -w
grep -iw "is" demo_file

Highlighting the search using GREP_OPTIONS:
$ export GREP_OPTIONS='--color=auto' GREP_COLOR='100;8'
$ grep this demo_file

Invert match using grep -v
$ grep -v "go" demo_text

display the lines which does not matches all the given pattern:
grep -v -e "pattern" -e "pattern"

 Counting the number of matches using grep -c:
When you want to count that how many lines matches the given pattern/string, then use the option -c.
grep -c "pattern" filename

Display only the file names which matches the given pattern using grep -l
$ grep -l this demo_*

Show only the matched string:
$ grep -o "is.*line" demo_file

It might not be that much useful when you give the string straight forward. But it becomes very useful when you give a regex pattern and trying to see what it matches as

Show the position of match in the line:
grep -o -b "pattern" file

Show line number while displaying the output using grep -n:
$ grep -n "go" demo_text

