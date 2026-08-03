# Replication in MongoDB

---

## Lesson 1: Introduction to Replication

### Question 1
**Which of the following are benefits of using replication?** *(Select all that apply)*

- **a. High availability**
  - **Correct**: Replication provides **high availability**, which means that our data can be continuously accessed, even if there is a lack of availability in a system.

- **b. Indexing**
  - **Incorrect**: Indexes can be used alongside replication, but indexing is **not** a benefit of replication itself.

- **c. Data partitioning**
  - **Incorrect**: Data partitioning is a part of **sharding**, not replication.

- **d. Data durability**
  - **Correct**: With replication, we can increase the **durability** of our data.

---

### Question 2
**Which term best matches the following definition?** *(Select one)*

> *The concept of making sure that our data can be continuously accessed, even if there is an interruption in a system.*

- **a. Data durability**
  - **Incorrect**: Data durability guarantees that data has been committed and will not be lost in the case of a system failure.

- **b. High availability**
  - **Correct**: **High availability** is the concept of making sure that our data can be continuously accessed, even if there is an interruption in a system. In MongoDB, replication is the process of storing multiple copies of the same data on different servers, providing fault tolerance and high availability.

- **c. Data partitioning**
  - **Incorrect**: Data partitioning is a method for distributing data across multiple machines.

- **d. Reduced latency**
  - **Incorrect**: Reduced latency refers to the decrease in the time it takes for a database operation to complete.

---

## Lesson 2: Replication in MongoDB

### Question 1
**Which replica set member accepts all write operations?** *(Select one)*

- **a. Arbiter**
  - **Incorrect**: Arbiters are usually introduced if there is an even number of voting members. Arbiters do **not** hold any data.

- **b. Secondary**
  - **Incorrect**: The secondary replicates data from the primary. The secondary does **not** receive write operations.

- **c. Primary**
  - **Correct**: The **primary** is the only member that accepts write operations. MongoDB applies write operations on the primary and records the operations in the primary's oplog (operation log).

- **d. Main**
  - **Incorrect**: "Main" is not a component of replication.

---

### Question 2
**Which replica set member is responsible for replicating data?** *(Select one)*

- **a. Secondary**
  - **Correct**: The **secondary** is responsible for replicating data from the primary. It does this by duplicating the primary's oplog entries and applying the operations to its own dataset, so the secondary's dataset reflects the primary's dataset.

- **b. Arbiter**
  - **Incorrect**: Arbiters are usually introduced if there is an even number of voting members. Arbiters do **not** hold any data.

- **c. Main**
  - **Incorrect**: "Main" is not a component of replication.

- **d. Primary**
  - **Incorrect**: The primary is the only member responsible for **accepting write operations**, not for replicating data.

---

## Lesson 3: Automatic Failover and Elections with MongoDB Deployments

### Question 1
**In a three-member replica set, how many of the members are voting members by default?** *(Select one)*

- **a. 1**
  - **Incorrect**: By default, a three-member replica set has **three** voting members. MongoDB recommends having an odd number of voting members to ensure a primary will be elected in the event of a network partition.

- **b. 2**
  - **Incorrect**: By default, a three-member replica set has **three** voting members.

- **c. 3**
  - **Correct**: By default, a three-member replica set has **three** voting members.

- **d. 0**
  - **Incorrect**: By default, a three-member replica set has **three** voting members.

---

### Question 2
**Which of the following scenarios will initiate an election?** *(Select all that apply)*

- **a. The primary becomes unavailable.**
  - **Correct**: If the primary becomes unavailable, it will initiate an election.

- **b. The secondaries lose connectivity to primary for longer than the configured timeout.**
  - **Correct**: An election is initiated if the secondary members lose connectivity to the primary for longer than the configured timeout, which is **10 seconds** by default.

- **c. The primary has been the primary for too long.**
  - **Incorrect**: The length of time that a member has been the primary is **not** a factor when it comes to initiating elections.

---

### Question 3
**Given the replica set shown, which member is most likely to be elected if an election takes place?** *(Select one)*

- **a. Priority 99**
  - **Correct**: The member with a priority value of **99** has the highest priority in this replica set, which means it's most likely to become the primary. Priority values can be between 0 and 1000 — a higher value makes a member more likely to be elected.

- **b. Priority 50**
  - **Incorrect**: The member with a priority value of 50 has the second-highest priority. A higher priority value makes a member more eligible to become primary.

- **c. Priority 1**
  - **Incorrect**: The member with a priority value of 1 has the lowest priority, making it the least likely to become the primary.

---

## Lesson 4: The MongoDB Operation Log

### Code Summary: The MongoDB Operation Log

Review the following code, which demonstrates how to access and retrieve the status of the oplog.

#### Retrieve the Most Recent Entries in the oplog

First, insert multiple documents into the `sample_supplies` database to populate the oplog:

```javascript
use sample_supplies
db.sales.updateMany({}, {$inc: {"customer.satisfaction": 1}});
```

Switch to the `local` database and examine its collections:

```javascript
use local
show collections
```

Query the `oplog.rs` collection, filtering by namespace (`ns`), sorted by natural descending order, limited to 5 results:

```javascript
db.oplog.rs.find({"ns" : "sample_supplies.sales"}).sort({$natural: -1}).limit(5)
```

#### Retrieve Information About the oplog

```javascript
rs.printReplicationInfo()
```

#### Retrieve Information About the Secondaries' oplog

```javascript
rs.printSecondaryReplicationInfo()
```

---

### Question 1
**What is the role of the oplog in relation to replication?** *(Select one)*

- **a. It keeps a running record of operations on a given member.**
  - **Correct**: The oplog is a special **capped collection** that behaves similarly to a circular buffer. The oldest entries are overwritten once it reaches capacity.

- **b. It keeps a record of all votes that have been cast in elections.**
  - **Incorrect**: The oplog does **not** keep a record of election votes. You can find election-related information using `rs.status()`.

- **c. It holds configuration information about the replica set.**
  - **Incorrect**: The oplog does **not** hold configuration information. Configuration information can be found in `mongod.conf` and the `rs.conf()` object.

---

### Question 2
**You want to retrieve the status of your oplog, including details such as the configured size and the first recorded event time. Which command should you use?** *(Select one)*

- **a. `rs.printSecondaryReplicationInfo()`**
  - **Incorrect**: `rs.printSecondaryReplicationInfo()` checks the secondaries' oplog against the primary's oplog — it does **not** retrieve the configured size or first recorded event time.

- **b. `rs.status()`**
  - **Incorrect**: `rs.status()` returns a document outlining the status of the entire replica set — not the oplog size or timestamps.

- **c. `rs.printReplicationInfo()`**
  - **Correct**: `rs.printReplicationInfo()` returns the actual size and configured size of the oplog, the length of the oplog in time, and the timestamps of the first and last entries.

- **d. `rs.conf()`**
  - **Incorrect**: `rs.conf()` retrieves the configuration object for a running replica set — not oplog size or timestamps.

---

### Question 3
**Which of the following are causes of replication lag?** *(Select all that apply)*

- **a. Network latency**
  - **Correct**: Increased network latency can be a cause of replication lag.

- **b. Disk throughput**
  - **Correct**: If the file system and disk device on the secondary cannot flush data to disk as quickly as the primary, the secondary will have difficulty keeping state.

- **c. Write concerns**
  - **Correct**: Not having appropriate write concerns can cause replication lag. Large data ingestion or bulk load operations requiring many writes — particularly unacknowledged writes — can cause secondaries to fall behind.

- **d. Enabling access control**
  - **Incorrect**: Enabling access control is **not** typically a cause of replication lag. It enforces authentication, requiring users to identify themselves and act within their assigned roles.

---

## Lesson 5: Read and Write Concerns with MongoDB Deployments

### Code Summary: Read and Write Concerns with MongoDB Deployments

Review the following code, which demonstrates how to:

- Specify the write concern on an individual operation
- Set the default read and write concerns
- Set the read preference in the connection string

#### Specify the Write Concern on an Individual Operation

Use `insertOne()` with a `writeConcern` option specifying `"majority"` and a timeout of 3000 ms:

```javascript
db.cats.insertOne(
  { name: "Mac", color: "black", age: 6 },
  { writeConcern: { w: "majority", wtimeout: 3000 } }
);
```

#### Set the Default Read and Write Concerns

Use `adminCommand()` to set the default read and write concern to `"majority"`:

```javascript
db.adminCommand({
  setDefaultRWConcern: 1,
  defaultReadConcern: { level: "majority" },
  defaultWriteConcern: { w: "majority" }
})
```

#### Set the Read Preference

Append the read preference to the connection string options. This example reads from the secondary with a max staleness of 120 seconds:

```
mongodb://db0.example.com,db1.example.com,db2.example.com/?replicaSet=myRepl&readPreference=secondary&maxStalenessSeconds=120
```

---

### Question 1
**You want data to be read only from your secondary nodes. How do you achieve this?** *(Select one)*

- **a. Set the write concern to `secondary`.**
  - **Incorrect**: Write concern describes the **level of acknowledgement** requested for a write operation, not where reads come from.

- **b. Set the read concern to `secondary`.**
  - **Incorrect**: Read concern lets your application specify a **durability guarantee** for documents returned by a read operation — it does not control which members serve reads.

- **c. Set the read preference to `secondary`.**
  - **Correct**: **Read preference** allows you to determine from which replica set members read operations will be served. When set to `secondary`, all read operations will be served from secondary nodes.

- **d. Set the read preference to `primary`.**
  - **Incorrect**: Setting read preference to `primary` will send **all reads to the primary**.

---

### Question 2
**In a replica set, you want to require acknowledgement that write operations have been durably committed to a calculated majority of the data-bearing voting members. How do you achieve this?** *(Select one)*

- **a. Set the write concern to `majority`.**
  - **Correct**: Setting the write concern to `"majority"` means that a **majority of members** must acknowledge write operations before they are considered successful.

- **b. Set the write concern to `0`.**
  - **Incorrect**: A write concern of `0` means **no members** are required to acknowledge write operations.

- **c. Set the read concern to `local`.**
  - **Incorrect**: The `local` read concern returns the most recent data the local instance has, with no guarantee it has been written to a majority of replica set members. Read concerns do not govern write acknowledgement.

- **d. Set the read preference to `primaryPreferred`.**
  - **Incorrect**: Read preference controls **where reads are sent** — it does not affect write acknowledgement.

---

## Lesson 6: Deploying a Replica Set in a MongoDB Deployment

### Code Summary: Deploying a Replica Set in a MongoDB Deployment

Review the following code, which demonstrates how to deploy a three-member replica set and initiate an election.

> **Note**: Three Ubuntu servers have been provisioned for this demonstration, with firewall rules opened between them and the latest version of MongoDB installed on each. You can also use Windows, Mac, or a different Linux distribution.

#### Step 1: Update the `mongod` Configuration Files

Update the `mongod.conf` file on each of the three servers, changing only the `bindIp` value to the respective server's domain.

**Server One (`mongod.conf`):**

```yaml
# mongod.conf
storage:
  dbPath: /var/lib/mongodb

systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

net:
  port: 27017
  bindIp: 127.0.0.1,<mongodb.repl.member.one.domain>

processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  keyFile: /etc/mongodb/pki/mongod-keyfile
  authorization: enabled

replication:
  replSetName: mongodb-repl-example
```

**Server Two (`mongod.conf`):** — same as above with `bindIp: 127.0.0.1,<mongodb.repl.member.two.domain>`

**Server Three (`mongod.conf`):** — same as above with `bindIp: 127.0.0.1,<mongodb.repl.member.three.domain>`

#### Step 2: Create Security Files

Run the following commands on **Server One** only to generate and distribute the keyfile:

```bash
# Create directory to hold the security key
sudo mkdir -p /etc/mongodb/pki

# Generate the security key
openssl rand -base64 756 > /tmp/keyfile

# Set correct permissions
chmod 0400 /tmp/keyfile

# Move the keyfile to the pki directory
sudo mv /tmp/keyfile /etc/mongodb/pki/mongod-keyfile

# Give mongodb user ownership of the pki directory
sudo chown -R mongodb. /etc/mongodb/pki

# Restart mongod
sudo systemctl restart mongod
```

#### Copy the Key to Other Servers

```bash
scp /tmp/keyfile mongod1.replset.com:/tmp
scp /tmp/keyfile mongod2.replset.com:/tmp
```

#### Add the Security Key to Server Two and Server Three

Run the following on each of the remaining servers:

```bash
# Create directory to hold the security key
sudo mkdir -p /etc/mongodb/pki/

# Give mongodb user ownership of the pki directory
sudo chown -R mongodb. /etc/mongodb/pki/

# Set correct permissions
chmod 0400 /etc/mongodb/pki/

# Restart mongod
sudo systemctl restart mongod
```

#### Step 3: Initiate the Replica Set

Connect on **Server One** and run `rs.initiate()`:

```javascript
mongosh
use admin

rs.initiate(
  {
    _id: "mongodb-repl-example",
    version: 1,
    members: [
      { _id: 0, host: "mongod0.replset.com" },
      { _id: 1, host: "mongod1.replset.com" },
      { _id: 2, host: "mongod2.replset.com" }
    ]
  }
)
```

#### Step 4: Create an Admin User

```javascript
db.createUser({
  user: "dba-admin",
  pwd: "dba-pass",
  roles: [
    { role: "root", db: "admin" }
  ]
})
```

Exit and reconnect to the replica set:

```javascript
exit

mongosh "mongodb://dba-admin:dba-pass@<server-one-ip:port>,<server-two-ip:port>,<server-three-ip:port>/?authSource=admin&replicaSet=mongodb-repl-example"
```

Run `rs.status()` to confirm the members array.

#### Initiate an Election

```javascript
rs.stepDown()
```

---

### Question 1
**You want to update the primary node of a replica set. How do you initiate an election?** *(Select one)*

- **a. `rs.election()`**
  - **Incorrect**: `rs.election()` is **not** a valid MongoDB command.

- **b. `rs.stepDown()`**
  - **Correct**: `rs.stepDown()` initiates an election in a replica set by stepping down the current primary.

- **c. `rs.startElection()`**
  - **Incorrect**: `rs.startElection()` is **not** a valid MongoDB command.

- **d. `rs.initiate()`**
  - **Incorrect**: `rs.initiate()` is used to **create** a replica set, not to initiate an election.

---

### Question 2
**Which of the following commands initiates a replica set?** *(Select one)*

- **a. `rs.initiate()`**
  - **Correct**: `rs.initiate()` is used to create a replica set. It accepts a document that specifies the hosts and the replica set configuration.

- **b. `rs.start()`**
  - **Incorrect**: `rs.start()` is **not** a valid MongoDB command.

- **c. `rs.stepDown()`**
  - **Incorrect**: `rs.stepDown()` initiates an **election**, not a replica set.

- **d. `rs.printReplicationInfo()`**
  - **Incorrect**: `rs.printReplicationInfo()` returns the actual and configured size of the oplog, its length in time, and timestamps of the first and last entries.

---

## Lesson 7: Configuring a Replica Set in a MongoDB Deployment

### Code Summary: Configuring a Replica Set in a MongoDB Deployment

Review the following code, which demonstrates how to:

- Retrieve the status of a `mongod` instance
- Reconfigure a running replica set
- Set the priority of a replica set member
- Add and remove members of a replica set

#### Retrieve the Status of a `mongod` Instance

Use `db.hello()` to retrieve information about a replica set, including host names, replica set name, primary, election ID, and timestamps:

```javascript
db.hello()
```

Example output:

```json
{
  "topologyVersion": { ... },
  "hosts": [
    "mongod0.replset.com:27017",
    "mongod1.replset.com:27017",
    "mongod2.replset.com:27017"
  ],
  "setName": "mongodb-repl-example",
  "isWritablePrimary": true,
  "secondary": false,
  "primary": "mongod0.replset.com:27017",
  "me": "mongod0.replset.com:27017",
  "electionId": "..."
}
```

#### Reconfigure a Running Replica Set

Assign `rs.conf()` to a variable to retrieve the configuration object:

```javascript
config = rs.conf()
```

#### Set the Priority of a Member

```javascript
config.members[2].priority = 10
```

#### Add a Member to a Replica Set

```javascript
member = { "_id": 3, "host": "mongod3.replset.com:27017" }
config.members.push(member)
```

Alternatively, use the `rs.add()` wrapper:

```javascript
rs.add("mongod3.replset.com:27017")
```

#### Remove a Member from a Replica Set

```javascript
config.members.splice(1, 1)
```

Alternatively, use the `rs.remove()` wrapper:

```javascript
rs.remove("mongod1.replset.com:27017")
```

#### Apply Configuration Changes

```javascript
rs.reconfig(config)
```

#### Retrieve the Status of a Replica Set

```javascript
rs.status()
```

Returns per-member info including: health, primary/secondary state, optime, and sync source.

---

### Question 1
**You run the `rs.status()` method on your replica set. Given the output, which host is the primary?** *(Select one)*

> In the `members` array, look for `"stateStr": "PRIMARY"`.

- **a. `host0.mongodb.net:27017`**
  - **Incorrect**: `host0.mongodb.net:27017` is currently a **secondary**, according to the `stateStr` field.

- **b. `host1.mongodb.net:27017`**
  - **Correct**: `host1.mongodb.net:27017` is currently the **primary**, according to `"stateStr": "PRIMARY"`. `rs.status()` allows you to view information about your replica set including member health, role, and operation information.

- **c. `host2.mongodb.net:27017`**
  - **Incorrect**: `host2.mongodb.net:27017` is currently a **secondary**, according to the `stateStr` field.

---

### Question 2
**You run `rs.conf()` on a replica set. Currently `mongod0.replset.com:27017` has the highest priority (10), but you want `mongod2.replset.com:27017` to have the highest priority. After assigning `rs.conf()` to `config`, which command should you use?** *(Select one)*

- **a. `config.members[2].priority = 100`**
  - **Correct**: Priority is in a nested object in the `members` array. `mongod2.replset.com` is at index 2. Setting it to `100` gives it the highest priority and the best chance of winning elections.

- **b. `config.members[1].priority = 100`**
  - **Incorrect**: Index 1 corresponds to `mongod1.replset.com`, not `mongod2.replset.com`.

- **c. `config.members[2].priority = 0`**
  - **Incorrect**: A priority value of `0` will result in this member **never being elected** as primary.

---

### Question 3
**You run the `db.hello()` method on your replica set. Given the output, which host is the primary?** *(Select one)*

> In the output, look for the `"primary"` field.

- **a. `mongod1.replset.com:27017`**
  - **Incorrect**: `mongod1.replset.com:27017` is **not** the primary, according to the `primary` field in the output.

- **b. `mongod2.replset.com:27017`**
  - **Incorrect**: `mongod2.replset.com:27017` is **not** the primary, according to the `primary` field in the output.

- **c. `mongod0.replset.com:27017`**
  - **Correct**: `mongod0.replset.com:27017` is the primary, as shown by `"primary": "mongod0.replset.com:27017"` in the `db.hello()` output. `db.hello()` is useful for monitoring cluster status via MongoDB drivers.

---

## Conclusion

### Replication in MongoDB

In this unit, you learned how to:

- Define replication and high availability
- Identify the architecture of replication in MongoDB
- Identify how automatic failovers work
- Explain the purpose of the oplog
- Set read and write concerns
- Direct reads by using read preferences
- Deploy a three-member replica set
- Reconfigure a running replica set
- Add and remove members from a replica set

---

## Resources

Use the following resources to learn more about Replication with MongoDB:

- **Lesson 1: Introduction to Replication**
  - [Replication](https://www.mongodb.com/docs/manual/replication/)

- **Lesson 2: Replication in MongoDB**
  - [Replication in MongoDB](https://www.mongodb.com/docs/manual/replication/)
  - [Replica Set Primary](https://www.mongodb.com/docs/manual/core/replica-set-primary/)
  - [Replica Set Secondary Members](https://www.mongodb.com/docs/manual/core/replica-set-secondary/)

- **Lesson 3: Automatic Failover and Elections with MongoDB Deployments**
  - [Replica Set Elections](https://www.mongodb.com/docs/manual/core/replica-set-elections/)

- **Lesson 4: The MongoDB Operation Log**
  - [Replica Set Oplog](https://www.mongodb.com/docs/manual/core/replica-set-oplog/)
  - [Check the Replication Lag](https://www.mongodb.com/docs/manual/tutorial/troubleshoot-replica-sets/#check-the-replication-lag)

- **Lesson 5: Read and Write Concerns with MongoDB Deployments**
  - [Write Concern](https://www.mongodb.com/docs/manual/reference/write-concern/)
  - [Read Concern](https://www.mongodb.com/docs/manual/reference/read-concern/)
  - [Read Preference](https://www.mongodb.com/docs/manual/core/read-preference/)

- **Lesson 6: Deploying a Replica Set in a MongoDB Deployment**
  - [Deploy a Replica Set](https://www.mongodb.com/docs/manual/tutorial/deploy-replica-set/)

- **Lesson 7: Configuring a Replica Set in a MongoDB Deployment**
  - [db.hello()](https://www.mongodb.com/docs/manual/reference/method/db.hello/)
  - [Adjust Priority for a Replica Set Member](https://www.mongodb.com/docs/manual/tutorial/adjust-replica-set-member-priority/)
  - [rs.conf()](https://www.mongodb.com/docs/manual/reference/method/rs.conf/)
  - [Add Members to a Replica Set](https://www.mongodb.com/docs/manual/tutorial/expand-replica-set/)
  - [Remove Members from a Replica Set](https://www.mongodb.com/docs/manual/tutorial/remove-replica-set-member/)
  - [rs.status()](https://www.mongodb.com/docs/manual/reference/method/rs.status/)
