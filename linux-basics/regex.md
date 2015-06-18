### Regular Expressions Examples:

A Regular Expression contains one or more of the following:
####A Character Set:
These are the characters retaining their literal meaning. The simplest type of Regular Expression consists only of a character set, with no metacharacters.

####An Anchor:
These designate (anchor) the position in the line of text that the RE is to match. For example, ^, and $ are anchors.

####Modifiers:
These expand or narrow (modify) the range of text the RE is to match. Modifiers include the asterisk, brackets, and the backslash.

The main uses for Regular Expressions (REs) are text searches and string manipulation. An RE matches a single character or a set of characters -- a string or a part of a string:
```
1. The asterisk -- * -- matches any number of repeats of the character string or RE preceding it, including zero.

EXamples: "1133*" matches 11 + one or more 3's + possibly other characters: 113, 1133, 111312, and so forth.

2. The dot -- . -- matches any one character, except a newline. [1]

Examples: "13." matches 13 + at least one of any character (including a space): 1133, 11333, but not 13 (additional character missing).

3. The caret -- ^ -- matches the beginning of a line, but sometimes, depending on context, negates the meaning of a set of characters in an RE.


4. The dollar sign -- $ -- at the end of an RE matches the end of a line.

5. "^$" matches blank lines.


6. Brackets -- [...] -- enclose a set of characters to match in a single RE.
Examples:

"[xyz]" matches the characters x, y, or z.

"[c-n]" matches any of the characters in the range c to n.

"[B-Pk-y]" matches any of the characters in the ranges B to P and k to y.

"[a-z0-9]" matches any lowercase letter or any digit.

"[^b-d]" matches all characters except those in the range b to d. This is an instance of ^ negating or inverting the meaning of the following RE (taking on a role similar to ! in a different context).

7. Combined sequences of bracketed characters match common word patterns. "[Yy][Ee][Ss]" matches yes, Yes, YES, yEs, and so forth. "[0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9][0-9][0-9]" matches any Social Security number.

8. The backslash -- \ -- escapes a special character, which means that character gets interpreted literally.

9. A "\$" reverts back to its literal meaning of "$", rather than its RE meaning of end-of-line. Likewise a "\\" has the literal meaning of "\".


10. Escaped "angle brackets" -- \<...\> -- mark word boundaries.

The angle brackets must be escaped, since otherwise they have only their literal character meaning.

"\<the\>" matches the word "the", but not the words "them", "there", "other", etc.
```
####Extended REs:

Additional metacharacters added to the basic set. Used in egrep, awk, and Perl:
```
1. The question mark -- ? -- matches zero or one of the previous RE. It is generally used for matching single characters.

2. The plus -- + -- matches one or more of the previous RE. It serves a role similar to the *, but does not match zero occurrences:
Examples:
# GNU versions of sed and awk can use "+",
# but it needs to be escaped.

echo a111b | sed -ne '/a1\+b/p'
echo a111b | grep 'a1\+b'
echo a111b | gawk '/a1+b/'
# All of above are equivalent.

3. Escaped "curly brackets" -- \{ \} -- indicate the number of occurrences of a preceding RE to match:

It is necessary to escape the curly brackets since they have only their literal character meaning otherwise. This usage is technically not part of the basic RE set.

"[0-9]\{5\}" matches exactly five digits (characters in the range of 0 to 9).

4. Parentheses -- ( ) -- enclose groups of REs. They are useful with the following "|" operator and in substring extraction using expr:

5. The -- | -- "or" RE operator matches any of a set of alternate characters:
Examples:

$ egrep 're(a|e)d' misc.txt
People who read seem to be better informed than those who do not.
 The clarinet produces sound by the vibration of its reed.
```

####POSIX Character Classes:
This is an alternate method of specifying a range of characters to match.
```
[:alnum:] matches alphabetic or numeric characters. This is equivalent to A-Za-z0-9.

[:alpha:] matches alphabetic characters. This is equivalent to A-Za-z.

[:blank:] matches a space or a tab.

[:cntrl:] matches control characters.

[:digit:] matches (decimal) digits. This is equivalent to 0-9.

[:graph:] (graphic printable characters). Matches characters in the range of ASCII 33 - 126. This is the same as [:print:], below, but excluding the space character.

[:lower:] matches lowercase alphabetic characters. This is equivalent to a-z.

[:print:] (printable characters). Matches characters in the range of ASCII 32 - 126. This is the same as [:graph:], above, but adding the space character.

[:space:] matches whitespace characters (space and horizontal tab).

[:upper:] matches uppercase alphabetic characters. This is equivalent to A-Z.

[:xdigit:] matches hexadecimal digits. This is equivalent to 0-9A-Fa-f.

Examples:
POSIX character classes generally require quoting or double brackets ([[ ]]).
bash$ grep [[:digit:]] test.file
abc=723

These character classes may even be used with globbing, to a limited extent:
bash$ ls -l ?[[:digit:]][[:digit:]]?
-rw-rw-r--    1 bozo  bozo         0 Aug 21 14:47 a33b
```
