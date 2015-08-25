###Example Output:
The following document provides a representation of a replica set configuration document. The configuration of your replica set may include only a subset of these settings:
```
{
  _id: <string>,
  version: <int>,
  members: [
    {
      _id: <int>,
      host: <string>,
      arbiterOnly: <boolean>,
      buildIndexes: <boolean>,
      hidden: <boolean>,
      priority: <number>,
      tags: <document>,
      slaveDelay: <int>,
      votes: <number>
    },
    ...
  ],
  settings: {
    getLastErrorDefaults : <document>,
    chainingAllowed : <boolean>,
    getLastErrorModes : <document>,
    heartbeatTimeoutSecs: <int>
  }
}
```
###replSetGetConfig
Returns a document that describes the current configuration of the replica set. To invoke the command directly, use the following operation:
```
db.runCommand( { replSetGetConfig: 1 } );
```
####replSetGetConfig._id
_id must be identical to the replication.replSetName or the value of –replSet specified to mongod on the command line.

####replSetGetConfig.version
An incrementing number used to distinguish revisions of the replica set configuration object from previous iterations of the configuration.

###replSetGetConfig.members
An array of member configuration documents, one for each member of the replica set. The members array is a zero-indexed array.

Each member-specific configuration document can contain the following fields:

####replSetGetConfig.members[n]._id
A numeric identifier of every member in the replica set. Once set, you cannot change the _id of a member

####replSetGetConfig.members[n].host
The hostname and, if specified, the port number, of the set member.

The hostname name must be resolvable for every host in the replica set.

####replSetGetConfig.members[n].arbiterOnly
Optional.

Type: boolean

Default: false

A boolean that identifies an arbiter. A value of true indicates that the member is an arbiter.

When using the rs.addArb() method to add an arbiter, the method automatically sets arbiterOnly to true for the added member.

####replSetGetConfig.members[n].buildIndexes
Optional.

Type: boolean

Default: true

A boolean that indicates whether the mongod builds indexes on this member. You can only set this value when adding a member to a replica set. You cannot change buildIndexes field after the member has been added to the set. To add a member, see rs.add() and rs.reconfig().

Do not set to false for mongod instances that receive queries from clients.

Setting buildIndexes to false may be useful if all the following conditions are true:

you are only using this instance to perform backups using mongodump, and
this member will receive no queries, and
index creation and maintenance overburdens the host system.
Even if set to false, secondaries will build indexes on the _id field in order to facilitate operations required for replication.
```
Warning: If you set buildIndexes to false, you must also set priority to 0. If priority is not 0, MongoDB will return an error when attempting to add a member with buildIndexes equal to false.

To ensure the member receives no queries, you should make all instances that do not build indexes hidden.

Other secondaries cannot replicate from a member where buildIndexes is false.
```
####replSetGetConfig.members[n].hidden
Optional.

Type: boolean

Default: false

When this value is true, the replica set hides this instance and does not include the member in the output of db.isMaster() or isMaster. This prevents read operations (i.e. queries) from ever reaching this host by way of secondary read preference.

####replSetGetConfig.members[n].priority:
Optional.

Type: Number, between 0 and 1000.

Default: 1.0

A number that indicates the relative eligibility of a member to become a primary.

Specify higher values to make a member more eligible to become primary, and lower values to make the member less eligible. Priorities are only used in comparison to each other. Members of the set will veto election requests from members when another eligible member has a higher priority value. Changing the balance of priority in a replica set will trigger an election.

A priority of 0 makes it impossible for a member to become primary.

####replSetGetConfig.members[n].tags:
Optional.

Type: document

Default: none

A tag set document containing mappings of arbitrary keys and values. These documents describe replica set members in order to customize write concern and read preference and thereby allow configurable data center awareness.

This field is only present if there are tags assigned to the member. See Configure Replica Set Tag Sets for more information.

Use tags to configure write concerns in conjunction with getLastErrorModes and getLastErrorDefaults.
#### replSetGetConfig.members[n].slaveDelay
ptional.

Type: integer

Default: 0

The number of seconds “behind” the primary that this replica set member should “lag”.

Use this option to create delayed members. Delayed members maintain a copy of the data that reflects the state of the data at some time in the past.
####replSetGetConfig.members[n].votes
Optional.

Type: integer

Default: 1

The number of votes a server will cast in a replica set election. The number of votes each member has is either 1 or 0, and arbiters always have exactly 1 vote.

A replica set can have up to 50 members but only 7 voting members. If you need more than 7 members in one replica set, set votes to 0 for the additional non-voting members.

Changed in version 3.0.0: Members cannot have votes greater than 1. For details, see Replica Set Configuration Validation.

###settings:
####replSetGetConfig.settings:
Optional.

Type: document

A document that contains configuration options that apply to the whole replica set.

The settings document contain the following fields:

replSetGetConfig.settings.chainingAllowed
New in version 2.2.4.

Optional.

Type: boolean

Default: true

When chainingAllowed is true, the replica set allows secondary members to replicate from other secondary members. When chainingAllowed is false, secondaries can replicate only from the primary.

####replSetGetConfig.settings.getLastErrorDefaults
Optional.

Type: document

A document that specifies the write concern for the replica set. The replica set will use this write concern only when write operations or getLastError specify no other write concern.

If getLastErrorDefaults is not set, the default write concern for the replica set only requires confirmation from the primary.

####replSetGetConfig.settings.getLastErrorModes
Optional.

Type: document

A document used to define an extended write concern through the use of tags. The extended write concern can provide data-center awareness.

For example, the following document defines an extended write concern named eastCoast and associates with a write to a member that has the east tag.

{ getLastErrorModes: { eastCoast: { "east": 1 } } }
Write operations to the replica set can use the extended write concern, e.g. { w: "eastCoast" }.

####replSetGetConfig.settings.heartbeatTimeoutSecs
Optional.

Type: int

Default: 10

Number of seconds that the replica set members wait for a successful heartbeat from each other. If a member does not respond in time, other members mark the delinquent member as inaccessible.


