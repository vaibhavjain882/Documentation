###MongoDb Replication
All MongoDB deployments support authentication. By default, MongoDB does not require authorization checking. You can enforce authorization checking when deploying MongoDB, or on an existing deployment; however, you cannot enable authorization checking on a running deployment without downtime.

1.Start one member of the replica set.
This mongod should not enable auth.

2.Create administrative users:
The following operations will create two users: a user administrator that will be able to create and modify users (siteUserAdmin), and a root user (siteRootAdmin) that you will use to complete the remainder of the tutorial
```
use admin
db.createUser( {
    user: "siteUserAdmin",
    pwd: "<password>",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  });
db.createUser( {
    user: "siteRootAdmin",
    pwd: "<password>",
    roles: [ { role: "root", db: "admin" } ]
  });
```

3.Stop the mongod instance.
4.Create the key file to be used by each member of the replica set.

Create the key file your deployment will use to authenticate servers to each other.

To generate pseudo-random data to use for a keyfile, issue the following openssl command:
```
openssl rand -base64 741 > mongodb-keyfile
chmod 600 mongodb-keyfile
```
You may generate a key file using any method you choose. Always ensure that the password stored in the key file is both long and contains a high amount of entropy. Using openssl in this manner helps generate such a key.

5.Copy the key file to each member of the replica set:

Copy the mongodb-keyfile to all hosts where components of a MongoDB deployment run. Set the permissions of these files to 600 so that only the owner of the file can read or write this file to prevent other users on the system from accessing the shared secret.

6.Start each member of the replica set with the appropriate options:
For each member, start a mongod and specify the key file and the name of the replica set. Also specify other parameters as needed for your deployment. For replication-specific parameters, see Replication Options required by your deployment.

If your application connects to more than one replica set, each set should have a distinct name. Some drivers group replica set connections by replica set name.

The following example specifies parameters through the --keyFile and --replSet command-line options:

```
mongod --keyFile /mysecretdirectory/mongodb-keyfile --replSet "rs0"
```
The following example specifies parameters through a configuration file:
```
mongod --config $HOME/.mongodb/config
```
In production deployments, you can configure a control script to manage this process. Control scripts are beyond the scope of this document.

7.Connect to the member of the replica set where you created the administrative users:
```
use admin
db.auth("siteRootAdmin", "<password>");
```
8.Initiate the replica set:

```
rs.initiate()
```
9.Verify the initial replica set configuration:

$ rs.conf()

10.Add the remaining members to the replica set:
```
rs.add("mongodb1.example.net")
rs.add("mongodb2.example.net")
```
11.Check the status of the replica set:

rs.status()

12.Create additional users to address operational requirements:
You can use built-in roles to create common types of database users, such as the dbOwner role to create a database administrator, the readWrite role to create a user who can update data, or the read role to create user who can search data but no more. You also can define custom roles.

For example, the following creates a database administrator for the products database:
```
use products
db.createUser(
  {
    user: "productsDBAdmin",
    pwd: "password",
    roles:
    [
{
        role: "dbOwner",
        db: "products"
      }
    ]
  }
)
```
### MongoDB Backup And Restore:

To backup data from a mongod or mongos instance running on the same machine and on the default port of 27017 use the following command:
```
mongodump
```

To limit the amount of data included in the database dump, you can specify --db and --collection as options to the mongodump command. For example:
```
mongodump --dbpath /data/db/ --out /data/backup/
mongodump --host mongodb.example.net --port 27017
```

mongodump will write BSON files that hold a copy of data accessible via the mongod listening on port 27017 of the mongodb.example.net host.
```
mongodump --collection collection --db test
```
This command creates a dump of the collection named collection from the database test in a dump/ subdirectory of the current working directory.

####Point in Time Operation Using Oplogs:
Use the --oplog option with mongodump to collect the oplog entries to build a point-in-time snapshot of a database within a replica set. With --oplog, mongodump copies all the data from the source database as well as all of the oplog entries from the beginning of the backup procedure to until the backup procedure completes. This backup procedure, in conjunction with mongorestore --oplogReplay, allows you to restore a backup that reflects a consistent and specific moment in time.

####Create Backups Without a Running mongod Instance:
If your MongoDB instance is not running, you can use the --dbpath option to specify the location to your MongoDB instance’s database files. mongodump reads from the data files directly with this operation. This locks the data directory to prevent conflicting writes. The mongod process must not be running or attached to these data files when you run mongodump in this configuration. Consider the following example:
```
mongodump --dbpath /srv/mongodb
```

#### Create Backups from Non-Local mongod Instances:
The --host and --port options for mongodump allow you to connect to and backup from a remote host. Consider the following example:
```
mongodump --host mongodb1.example.net --port 3017 --username user --password pass --out /opt/backup/mongodump-2012-10-24
```

####Restore a Database with mongorestore:
The mongorestore utility restores a binary backup created by mongodump. By default, mongorestore looks for a database backup in the dump/ directory.

The mongorestore utility can restore data either by:

connecting to a running mongod or mongos directly, or
writing to a local database path without use of a running mongod.
The mongorestore utility can restore either an entire database backup or a subset of the backup.

A mongorestore command that connects to an active mongod or mongos has the following prototype form:
```
mongorestore --port <port number> <path to the backup>
```
A mongorestore command that writes to data files without using a running mongod has the following prototype form:
```
mongorestore --dbpath <local database path> <path to the backup>
```
Consider the following example:
```
mongorestore dump-2012-10-25/
```
####Restore Point in Time Oplog Backup:
```
mongorestore --oplogReplay
```
You may also consider using the mongorestore --objcheck option to check the integrity of objects while inserting them into the database, or you may consider the mongorestore --drop option to drop each collection from the database before restoring from backups.

####Restore a Subset of data from a Binary Database Dump:
mongorestore also includes the ability to a filter to all input before inserting it into the new database. Consider the following example:
```
mongorestore --filter '{"field": 1}'
```
Here, mongorestore only adds documents to the database from the dump located in the dump/ folder if the documents have a field name field that holds a value of 1. Enclose the filter in single quotes (e.g. ') to prevent the filter from interacting with your shell environment.

###Restore without a Running mongod:
mongorestore can write data to MongoDB data files without needing to connect to a mongod directly.
```
mongorestore --dbpath /srv/mongodb --journal
```
Here, mongorestore restores the database dump located in dump/ folder into the data files located at /srv/mongodb. Additionally, the --journal option ensures that mongorestore records all operation in the durability journal. The journal prevents data file corruption if anything (e.g. power failure, disk failure, etc.) interrupts the restore operation.

####Restore Backups to Non-Local mongod Instances:

By default, mongorestore connects to a MongoDB instance running on the localhost interface (e.g. 127.0.0.1) and on the default port (27017). If you want to restore to a different host or port, use the --host and --port options.
```
mongorestore --host mongodb1.example.net --port 3017 --username user --password pass /opt/backup/mongodump-2012-10-24
```
### Repair Database: Recover Data after an Unexpected Shutdown:-

A. There are two processes to repair data files that result from an unexpected shutdown:
Use the --repair option in conjunction with the --repairpath option. mongod will read the existing data files, and write the existing data to new data files.
You do not need to remove the mongod.lock file before using this procedure.

B. Use the --repair option. mongod will read the existing data files, write the existing data to new files and replace the existing, possibly corrupt, files with new files.
You must remove the mongod.lock file before using this procedure.

```
mongod --dbpath /data/db --repair --repairpath /data/db0

When this completes, the new repaired data files will be in the /data/db0 directory.

mongod --dbpath /data/db0
```

Once you confirm that the data files are operational you may delete or archive the old data files in the /data/db directory. You may also wish to move the repaired files to the old database location or update the dbPath to indicate the new location.

B. Remove the stale lock file
```
rm /data/db/mongod.lock
```
Start mongod using the option to replace the original files with the repaired files.
```
mongod --dbpath /data/db --repair
```
Start mongod as usual:
```
mongod --dbpath /data/db
```
In normal operation, you should never remove the mongod.lock file and start mongod. Instead consider the one of the above methods to recover the database and remove the lock files. In dire situations you can remove the lockfile, and start the database using the possibly corrupt files, and attempt to recover data from the database; however, it’s impossible to predict the state of the database in these situations.

If you are not running with journaling, and your database shuts down unexpectedly for any reason, you should always proceed as if your database is in an inconsistent and likely corrupt state. If at all possible restore from backup or, if running as a replica set, restore by performing an initial sync using data from an intact member of the set, as described in Resync a Member of a Replica Set.

####Resync a Member of a Replica Set:

MongoDB provides two options for performing an initial sync:

A. Restart the mongod with an empty data directory and let MongoDB’s normal initial syncing feature restore the data. This is the more simple option but may take longer to replace the data.
See Procedures.

B. Restart the machine with a copy of a recent data directory from another member in the replica set. This procedure can replace the data more quickly but requires more manual steps.
See Sync by Copying Data Files from Another Member.

Later...
