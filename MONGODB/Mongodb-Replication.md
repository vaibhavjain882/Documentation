###MongoDb Replication
All MongoDB deployments support authentication. By default, MongoDB does not require authorization checking. You can enforce authorization checking when deploying MongoDB, or on an existing deployment; however, you cannot enable authorization checking on a running deployment without downtime.

1. Start one member of the replica set.
This mongod should not enable auth.

2. Create administrative users:
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

3. Stop the mongod instance.
4. Create the key file to be used by each member of the replica set.

Create the key file your deployment will use to authenticate servers to each other.

To generate pseudo-random data to use for a keyfile, issue the following openssl command:
```
openssl rand -base64 741 > mongodb-keyfile
chmod 600 mongodb-keyfile
```
You may generate a key file using any method you choose. Always ensure that the password stored in the key file is both long and contains a high amount of entropy. Using openssl in this manner helps generate such a key.

5. Copy the key file to each member of the replica set:

Copy the mongodb-keyfile to all hosts where components of a MongoDB deployment run. Set the permissions of these files to 600 so that only the owner of the file can read or write this file to prevent other users on the system from accessing the shared secret.

6. Start each member of the replica set with the appropriate options:
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

7. Connect to the member of the replica set where you created the administrative users:
```
use admin
db.auth("siteRootAdmin", "<password>");
```
8. Initiate the replica set:

```
rs.initiate()
```
9. Verify the initial replica set configuration:

$ rs.conf()

10. Add the remaining members to the replica set:
```
rs.add("mongodb1.example.net")
rs.add("mongodb2.example.net")
```
11. Check the status of the replica set:

rs.status()

12. Create additional users to address operational requirements:
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

