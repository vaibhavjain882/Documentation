Go to the 143rd line of file:
$ vim +143 filename.txt

Go to the first match of the specified:
$ vim +/search-term filename.txt

Open the file in read only mode:
$ vim -R /etc/passwd

###Cursor movement:
You must be in command mode if you wish to move the cursor to another position in your file. If you've just finished typing text, you're still in insert mode and will need to press ESC to return to the command mode.

####Moving One Character at a Time

Try using your direction keys to move up, down, left and right in your file. Sometimes, you may find that the direction keys don't work. If that is the case, to move the cursor one character at the time, you may use the h, j, k, and l keys. These keys move you in the following directions:
```
h: left one space		l	right one space
j: down one space		k	up one space
```
If you move the cursor as far as you can in any direction, you may see a screen flash or hear a beep.

####Moving among Words and Lines

While these four keys (or your direction keys) can move you just about anywhere you want to go in your file, there are some shortcut keys that you can use to move a little more quickly through a document. To move more quickly among words, you might use the following:
```
w	moves the cursor forward one word 
b	moves the cursor backward one word (if in the middle of a 
 	word, b will move you to the beginning of the current word).
e	moves to the end of a word.
```
To build on this further, you can precede these commands with a number for greater movement. For example, 5w would move you forward five words; 12b would move you backwards twelve words. [You can also use numbers with the commands mentioned earlier. For example, 5j would move you down 5 characters.]

####Command Keys and Case

You will find when using vi that lower case and upper case command keys are interpreted differently. For example, when using the lower case w, b, and e commands, words will be defined by a space or a punctuation mark. On the other hand, W, B, and E commands may be used to move between words also, but these commands ignore punctuation.
Shortcuts

Two short cuts for moving quickly on a line include the $ and the 0 (zero) keys. The $ key will move you to the end of a line, while the 0 will move you quickly to the beginning of a line.

####Screen Movement

To move the cursor to a line within your current screen use the following keys:
```
H	moves the cursor to the top line of the screen.
M	moves the cursor to the middle line of the screen.
L	moves the cursor to the last line of the screen.
```
####To scroll through the file and see other screens use:
```
ctrl-f	scrolls down one screen
ctrl-b	scrolls up one screen
ctrl-u	scrolls up a half a screen
ctrl-d	scrolls down a half a screen
```
Two other useful commands for moving quickly from one end to the other of a document are G to move to the end of the file and 1G to move to the beginning of the file. If you precede G with a number, you can move to a specific line in the document (e.g. 15G would move you to line 15).

####Moving by Searching

One method for moving quickly to a particular spot in your file is to search for specific text. When you are in command mode, type a / followed the text you wish to search for. When you press Return, the cursor will move to the first incidence of that string of text. You can repeat the search by typing n or search in a backwards direction by using N.

###Basic Editing:

To issue editing commands, you must be in command mode. As mentioned before, commands will be interpreted differently depending upon whether they are issued in lower or upper case. Also, many of the editing commands can be preceded by a number to indicate a repetition of the command.

####Deleting (or Cutting) Characters, Words, and Lines

To delete a character, first place your cursor on that character. Then, you may use any of the following commands:
```
x	deletes the character under the cursor.
X	deletes the character to the left of your cursor.
dw	deletes from the character selected to the end of the word.
dd	deletes all the current line.
D	deletes from the current character to the end of the line.
```
Preceding the command with a number will delete multiple characters. For example, 10x will delete the character selected and the next 9 characters; 10X will delete the 10 characters to the left of the currently selected character. The command 5dw will delete 5 words, while 4dd deletes four lines.

####Pasting Text using Put

Often, when you delete or cut text, you may wish to reinsert it in another location of the document. The Put command will paste in the last portion of text that was deleted since deleted text is stored in a buffer. To use this command, place the cursor where you wish the deleted text to appear. Then use p to reinsert the text. If you are inserting a line or paragraph use the lower case p to insert on the line below the cursor or upper case P to place in on the line above the cursor.
Copying Text with Yank

If you wish to make a duplicate copy of existing text, you may use the yank and put commands to accomplish this function. Yank copies the selected text into a buffer and holds it until another yank or deletion occurs. Yank is usually used in combination with a word or line object such as the ones shown below:
```
yw	copies a word into a buffer (7yw copies 7 words)
yy	copies a line into a buffer (3yy will copy 3 lines)
```
Once the desired text is yanked, place the cursor in the spot in which you wish to insert the text and then use the put command (p for line below or P for line above) to insert the contents of the buffer.

####Replacing or Changing Characters, Words, and Lines

When you are using the following commands to replace text, you will be put temporarily into insert mode so that you can change a character, word, line, or paragraph of text.
```
r	replaces the current character with the next character you enter/type.
   	Once you enter the character you are returned to command mode.
R	puts you in overtype mode until you hit ESC which will then return
   	you to command mode.
cw	changes and replaces the current word with text that you type.  A dollar
   	sign marks the end of the text you're changing.  Pressing ESC when you
   	finish will return you to command mode.
```
####Inserting Text

If you wish to insert new text in a line, first position the cursor to the right of where you wish the inserted text to appear. Type i to get into insert mode and then type in the desired text (note that the text is inserted before the cursor). Press ESC to return to command mode.
Inserting a Blank Line

To insert a blank line below the line your cursor is currently located on, use the o key and then hit ESC to return to the command mode . Use O to insert a line above the line the cursor is located on.
Appending Text

You can use the append command to add text at any place in your file. Append (a) works very much like Insert (i) except that it insert text after the cursor rather than before it. Append is probably used most often for adding text to the end of a line. Simply place your cursor where you wish to append text and press a. Once you've finished appending, press ESC to go back to command mode.
Joining Lines

Since vi does not use automatic word wrap, it is not unusual in editing lines to end up with lines that are too short and that might be improved if joined together. To do this, place your cursor on the first line to be joined and type J. As with other commands, you can precede J with a number to join multiple lines (4J joins 4 lines).
Undoing

Be sure to remember this command. When you make a mistake you can undo it. DO NOT move the cursor from the line where you made the change. Then try using one of the following two commands:
```
u	undoes the last change you made anywhere in the file.  Using u again 
   	will "undo the undo".
U	undoes all recent changes to the current line.  You can not have moved
	from the line to recover the original line.
```
