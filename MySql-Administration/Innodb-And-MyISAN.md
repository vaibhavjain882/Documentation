###Difference between InnoDB and MyISAM:

#### Referential Integrity:

Referential integrity ensures that relationships between tables remains consistent which is supported by InnoDB but does't by MyISAM.

#### Transactions & Atomicity :

MyISAM do not support transactions whereas InnoDB does.

If an operation is interrupted while using a MyISAM table, the operation is aborted immediately, and the rows (or even data within each row) that are affected remains affected, even if the operation did not go to completion.

If an operation is interrupted while using an InnoDB table, because it using transactions, which has atomicity, any transaction which did not go to completion will not take effect, since no commit is made.

#### Table Locking Vs Row Locking : Concurrency

When a query runs against a MyISAM table, the entire table in which it is querying will be locked. This means subsequent queries will only be executed after the current one is finished. If you are reading a large table, and/or there are frequent read and write operations, this can mean a huge backlog of queries.

When a query runs against an InnoDB table, only the row(s) which are involved are locked, the rest of the table remains available for CRUD operations. This means queries can run simultaneously on the same table, provided they do not use the same row.

#### Reliability :

MyISAM offers no data integrity - Hardware failures, unclean shutdowns and canceled operations can cause the data to become corrupt. This would require full repair or rebuilds of the indexes and tables.

InnoDB, on the other hand, uses a transactional log, a double-write buffer and automatic checksumming and validation to prevent corruption. Before InnoDB makes any changes, it records the data before the transactions into a system tablespace file called ibdata1. If there is a crash, InnoDB would autorecover through the replay of those logs.


