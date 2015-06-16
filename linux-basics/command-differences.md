####Difference between dig, host and nslookup:

Dig: Linux utility, needs compiled to run in windows. Provides more detail, more advanced domain info. The more information the better.

Host: Unix based. Replacement for nslookup with more options and detail. 

nslookup: Windows and Unix based. No longer supported in Unix-based systems. Provides basic info. for name queries. 

Dig is highly recommended given its options and detailed information if you require such info. For simple queries, nslookup or host will provide you the info. you need.

#### Difference between find and locate:

find searches in the real system. Is slower but always up-to-date and has more options (size, modification time,...)

locate uses a previously built database (command updatedb). Is much faster, but uses an 'older' database and searches only names or parts of them.


