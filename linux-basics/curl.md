### Curl command examples:
```
$ curl http://www.url.com

command will get the content of the URL and display it in the STDOUT 

$ curl -o mytext.html http://www.gnu.org/text.html

-o (lowercase o) the result will be saved in the filename provided in the command line

$ curl -O http://www.gnu.org/text.html

-O (uppercase O) the filename in the URL will be taken and it will be used as the filename to store the result

$ curl -O URL1 -O URL2

Download multiple files at a time

$ curl -L http://www.google.com

When a requested web page is moved to another place, use -L option to follow the redirection.

$ curl -C - -O http://www.gnu.org/software/gettext/manual/gettext.html

If you stop a download, you can resume it by using -C option.

$ curl --limit-rate 1000B -O http://www.url.com/text.html

YOu can define data transfer limit 

$ curl -z 21-Jan-11 http://www.example.com/text.html

above command will download the text.html only if it is modified later than the given date and time.

$ curl -z -21-Dec-11 http://www.example.com/test.html

above command will download the yy.html, if it is modified before than the given date and time

$ curl -u username:password URL

Pass HTTP Authentication in cURL

$ curl -u ftpuser:ftppass -O ftp://ftp_server/public_html/xss.php

Download Files from FTP server

$ curl   ftp://ftp.uk.debian.org/debian/pool/main/[a-z]/

above command will list out all the packages from a-z ranges in the terminal.

$ curl -u ftpuser:ftppass -T myfile.txt ftp://ftp.testserver.com

Upload Files to FTP Server

$ curl -u ftpuser:ftppass -T "{file1,file2}" ftp://ftp.testserver.com

upload multiple files at a same time

$ curl --mail-from blah@test.com --mail-rcpt foo@test.com smtp://mailserver.com

Once the above command is entered, it will wait for the user to provide the data to mail. Once you’ve composed your 
message, type . (period) as the last line, which will send the email immediately.

$ curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://hostname/resource

curl get with json

$ curl -X POST -d @filename http://hostname/resource

curl http post
```




