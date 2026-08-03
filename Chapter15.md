# Self-Managed Upgrades & Maintenance

---

## Lesson 1: Zero Downtime Maintenance with a MongoDB Deployment

### Question 1
**You want to add a new index to your replica set. Which type of environment should you test in before releasing to users?** *(Select one)*

- **a. Production**
  - **Incorrect**: You should push only well-tested changes to the production environment to avoid releasing suboptimal features to users.

- **b. Pre-production**
  - **Correct**: It's crucial to thoroughly test any changes before pushing to production. Using a **pre-production environment** helps minimize any potential bugs that can arise when changes are made.

- **c. Development**
  - **Incorrect**: The development environment is primarily used for implementing new features. While some testing happens in the development environment, it tends to be less rigorous than testing in a pre-production environment.

- **d. Application**
  - **Incorrect**: The application environment can sometimes be a catch-all term. It does not specifically correspond to the pre-production environment.

---

### Question 2
**Which of the following can result in rolling maintenance?** *(Select all that apply)*

- **a. Creating a new index**
  - **Correct**: Creating a new index on a replica set requires it to be built on each node in a **rolling fashion**.

- **b. Upgrading the MongoDB version**
  - **Correct**: The MongoDB version of each host must be upgraded individually in a **rolling fashion**.

- **c. Creating a new user**
  - **Incorrect**: The process of creating a new user is replicated by all members of a replica set, which eliminates the need for rolling maintenance.

- **d. Upgrading the operating system**
  - **Correct**: Upgrading the host's operating system requires you to restart each `mongod` process in a **rolling fashion**.

---

### Question 3
**Which of the following is true about MongoDB maintenance?** *(Select one)*

- **a. Upgrading and maintaining MongoDB requires scheduled downtime.**
  - **Incorrect**: Performing maintenance on MongoDB does **not** require scheduled downtime. MongoDB replica sets are upgraded in a way that allows you to keep downtime to a minimum.

- **b. MongoDB requires that each node be removed from the replica set before upgrading.**
  - **Incorrect**: Each node is upgraded one at a time. You only need to gracefully shut down each node, which doesn't require removing it from the replica set.

- **c. Nodes can be updated individually while the others continue running.**
  - **Correct**: In MongoDB replica sets, each node is upgraded one at a time. This allows you to keep downtime to a minimum when performing maintenance.

---

## Lesson 2: MongoDB Client Driver Upgrades

### Code Summary: MongoDB Client Driver Upgrades

Review the following code, which demonstrates how to upgrade a MongoDB driver. This example uses Node.js, but the process is similar with other drivers.

#### Check the Version of Node.js

```bash
node -v
```

#### Confirm the MongoDB Node.js Driver Version

Check the `package.json` file for the MongoDB Node.js driver version:

```bash
grep mongodb package.json
```

#### Check Compatibility

To determine the compatibility between MongoDB and your language driver, check the MongoDB documentation for your language driver.

#### Upgrade Node.js Version

Upgrade Node.js to version 16 by using `nvm` (node version manager):

```bash
nvm install v16
node -v
```

#### Upgrade the MongoDB Driver

Upgrade the Node.js MongoDB driver to the latest version:

```bash
npm i mongodb
```

#### Node.js Version 12 Application (Callback-style)

```javascript
const { MongoClient } = require('mongodb');
const uri = "";
const client = new MongoClient(uri);

client.connect((err) => {
  const collection = client.db("sample_supplies").collection("sales");
  console.log("err");

  collection.findOne({}).then((sale) => {
    console.log(sale);
  });

  client.close();
});
```

#### Node.js Version 16 Application (async/await)

```javascript
const { MongoClient, ServerApiVersion } = require('mongodb');
const uri = "";
const client = new MongoClient(uri, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  serverApi: ServerApiVersion.v1
});

async function run() {
  try {
    const collection = client.db("sample_supplies").collection("sales");
    const query = await collection.findOne({});
    console.log(query);
  } finally {
    await client.close();
  }
}
run().catch(console.dir);
```

---

### Question 1
**You want to upgrade your MongoDB driver. What action should you take before doing so?** *(Select one)*

- **a. Clear the oplog**
  - **Incorrect**: You should **not** clear the oplog. This will cause an initial sync and increase the downtime of the member being upgraded.

- **b. Gracefully shut down the host server**
  - **Incorrect**: You do **not** need to shut down the host server to upgrade the MongoDB driver.

- **c. Check the compatibility between the driver and MongoDB version**
  - **Correct**: To avoid breaking your application, you should ensure that the MongoDB version is **compatible** with the MongoDB driver that you plan to use.

- **d. Set the feature compatibility version**
  - **Incorrect**: You set the feature compatibility version only when upgrading **MongoDB versions**, not drivers. The feature compatibility version enables or disables features that persist data and are incompatible with earlier versions of MongoDB.

---

### Question 2
**Which characteristics affect the proper functioning of an application?** *(Select all that apply)*

- **a. Regressions in driver upgrades**
  - **Correct**: Occasionally, early driver upgrades can cause a **regression in performance**. After upgrading, it's important to thoroughly test your application before pushing it to a production environment.

- **b. Creating new users**
  - **Incorrect**: Creating a new user should **not** affect the proper functioning of your application.

- **c. New host server**
  - **Incorrect**: Changing the host server should not affect the proper functioning of your application. However, it's still a good idea to thoroughly test your application before releasing it to users.

- **d. Performance improvements**
  - **Correct**: Performance improvements can disrupt runtime triggers or similar functionality. It's crucial to test these types of functions after upgrading.

---

## Lesson 3: MongoDB Server Upgrades

### Code Summary: MongoDB Server Upgrades

Review the following code, which demonstrates how to upgrade MongoDB versions on a replica set.

#### Check the Version of a MongoDB Database

```javascript
mongosh
db.version();
```

#### Confirm the Feature Compatibility Version

```javascript
db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } ).featureCompatibilityVersion
```

#### Determine the oplog Window

Use `printReplicationInfo()` to determine the oplog window. Look for the field `log length start to end`:

```javascript
db.printReplicationInfo()
```

#### Confirm Secondaries' Replication Lag

Confirm that the secondaries are not trying to catch up from a previous event:

```javascript
rs.printSecondaryReplicationInfo()
```

#### Upgrade Secondaries

Log in to the server for one of the secondaries and run the following steps:

**1. Stop the MongoDB service:**

```bash
sudo systemctl stop mongod
```

**2. Import the GPG keys:**

```bash
curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg \
  --dearmor
```

**3. Create a list file for the new 6.0 package repository:**

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

**4. Reload the local package database:**

```bash
sudo apt-get update
```

**5. Install the latest version:**

```bash
sudo apt-get --only-upgrade install mongodb-org*
```

**6. Restart the MongoDB service:**

```bash
sudo systemctl restart mongod
```

**7. Confirm the upgrade:**

```bash
mongosh --quiet --eval 'db.version()'
```

> Repeat these steps for each secondary.

#### Elect a New Primary

**1. Log in to the primary and confirm you are on the primary:**

```javascript
print({CurrentNode: rs.hello().me, Primary: rs.hello().primary})
```

**2. Step down the primary to trigger a new election:**

```javascript
rs.stepDown()
```

**3. Confirm the new primary was elected, then exit:**

```javascript
print({CurrentNode: rs.hello().me, Primary: rs.hello().primary})
exit
```

#### Upgrade the Primary

**1. Stop the MongoDB service:**

```bash
sudo systemctl stop mongod
```

**2. Import the GPG keys:**

```bash
curl -fsSL https://pgp.mongodb.com/server-6.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg \
  --dearmor
```

**3. Create a list file for the new 6.0 package repository:**

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

**4. Reload the local package database:**

```bash
sudo apt-get update
```

**5. Install the latest version:**

```bash
sudo apt-get --only-upgrade install mongodb-org*
```

**6. Restart the MongoDB service:**

```bash
sudo systemctl restart mongod
```

**7. Confirm the upgrade:**

```bash
mongosh --quiet --eval 'db.version()'
```

#### Set the Feature Compatibility Version

Connect to your replica set and set the feature compatibility version using `adminCommand`:

```javascript
mongosh "mongodb://dba-admin@mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset"

db.adminCommand( { setFeatureCompatibilityVersion: "6.0" } )
```

---

### Question 1
**You want to upgrade your MongoDB database version to 6.0 for your three-member replica set. Which of the following should you do before upgrading?** *(Select all that apply)*

- **a. Confirm that MongoDB version 5.0 is installed**
  - **Correct**: You must install versions in **consecutive order**. For example, if you are running 4.4, you must first upgrade to 5.0 before upgrading to 6.0.

- **b. Gracefully shut down all members at once**
  - **Incorrect**: When upgrading, you should have only **one member** of a replica set down at a time to avoid system downtime.

- **c. Create a pre-production environment for your application that uses the updated version**
  - **Correct**: It's a best practice to set up a **pre-production environment** before upgrading so that you can test the changes before pushing to a production environment.

- **d. Confirm the feature compatibility version of each member**
  - **Correct**: Before upgrading, you should confirm that each member of the replica set has the **same feature compatibility version**. The feature compatibility version enables or disables features that persist data and are incompatible with earlier versions of MongoDB.

- **e. Confirm the state of each member**
  - **Correct**: Before upgrading, ensure that no replica set member is in the `ROLLBACK` or `RECOVERING` state. If the state is unclear, the risk of corrupting or losing data rises significantly.

---

### Question 2
**You have completed your pre-upgrade checklist and are ready to upgrade. What should you do first?** *(Select one)*

- **a. Install the most recent version of MongoDB**
  - **Incorrect**: After completing the preliminary steps, the next thing you need to do is **gracefully shut down the secondary member** that you will upgrade first.

- **b. Gracefully shut down the first secondary member that you plan to upgrade**
  - **Correct**: After completing the preliminary steps, you must gracefully shut down the secondary member that you will upgrade first, or you risk **corrupting data**.

- **c. Remove members from the replica set**
  - **Incorrect**: You do **not** need to remove any members from the replica set. You only need to gracefully shut down the secondary member that you will upgrade first.

---

## Conclusion

### Self-Managed Upgrades & Maintenance

In this unit, you learned how to:

- Define zero downtime maintenance
- Perform maintenance with MongoDB
- Upgrade a MongoDB language driver
- Retrieve the current version of MongoDB
- Set the feature compatibility version
- Upgrade to a newer version of MongoDB on a replica set
- Determine how much time you have to complete maintenance based on the size of the oplog

---

## Resources

Use the following resources to learn more about upgrades and maintenance with MongoDB:

- **Lesson 1: Zero Downtime Maintenance with a MongoDB Deployment**
  - [Perform Maintenance on Replica Set Members](https://www.mongodb.com/docs/manual/tutorial/perform-maintence-on-replica-set-members/)
  - [Your Ultimate Guide to Rolling Upgrades](https://www.mongodb.com/blog/post/your-ultimate-guide-to-rolling-upgrades)

- **Lesson 2: MongoDB Client Driver Upgrades**
  - [MongoDB Drivers](https://www.mongodb.com/docs/drivers/)

- **Lesson 3: MongoDB Server Upgrades**
  - [Upgrade Replica Sets](https://www.mongodb.com/docs/manual/release-notes/6.0-upgrade-replica-set/)
  - [Upgrade a Standalone to 6.0](https://www.mongodb.com/docs/manual/release-notes/6.0-upgrade-standalone/)
