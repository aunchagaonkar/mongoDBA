# Self-Managed Backup & Recovery

---

## Lesson 1: Backup Plans on a MongoDB Server

### Question 1
**A business has determined it can tolerate the database being down for no more than one hour. What is this value called?** *(Select one)*

- **a. Recovery Time Objective**
  - **Correct**: This is an example of a **Recovery Time Objective (RTO)**, which is the maximum amount of time that a business can tolerate after an outage before the disruption makes normal business operations intolerable.

- **b. Recovery Point Objective**
  - **Incorrect**: This is not an example of a Recovery Point Objective. A **Recovery Point Objective (RPO)** is the maximum acceptable amount of data loss that a business is willing to tolerate in the event of a disruption, expressed as an amount of time.

- **c. Backup Time Objective**
  - **Incorrect**: This is not an example of a Backup Time Objective. A Backup Time Objective is not part of a backup plan.

- **d. Backup Point Objective**
  - **Incorrect**: This is not an example of a Backup Point Objective. A Backup Point Objective is not part of a backup plan.

---

## Lesson 2: Filesystem Snapshots on a MongoDB Server

### Question 1
**Before you create a snapshot, you need to lock your MongoDB deployment.** *(True or False)*

- **a. True**
  - **Correct**: You need to lock your MongoDB deployment by using the `db.fsyncLock()` command. This prevents additional write operations while creating the snapshot. After creating the snapshot, remember to unlock your deployment by using `db.fsyncUnlock()`.

- **b. False**
  - **Incorrect**: You **do** need to lock your MongoDB deployment by using `db.fsyncLock()`. This prevents additional write operations while creating the snapshot. If you don't lock the deployment, you risk having an incomplete snapshot. After creating the snapshot, remember to unlock your deployment by using `db.fsyncUnlock()`.

---

### Question 2
**You want to create a complete copy of your data at a specific point in time. How can you accomplish this?** *(Select one)*

- **a. Create a dump file of the data only**
  - **Incorrect**: You can back up your data in a dump file by using `mongodump`, but you would also need the oplog to create a point-in-time backup.

- **b. Export your data to a CSV file**
  - **Incorrect**: Exporting your data to a CSV does not create an efficient point-in-time backup.

- **c. Create a snapshot of your data**
  - **Correct**: You should create a **snapshot** to have a complete copy of your data at a specific point in time.

---

## Lesson 3: Filesystem Snapshot Volumes on a MongoDB Server

### Code Summary: Filesystem Snapshot Volumes on a MongoDB Server

Review the following code, which demonstrates how to create and restore a volume snapshot.

#### Lock the Database

Lock the database using `fsyncLock()`. This prevents write operations and flushes any pending write operations to disk:

```javascript
mongosh
db.fsyncLock();
exit
```

#### Create a Snapshot Volume

Create a snapshot volume with a maximum size of 100 MB, named `mdb-snapshot`, backed by the MongoDB data store volume:

```bash
sudo lvcreate --size 100M --snapshot --name mdb-snapshot /dev/vg0/mdb;
```

#### Unlock the Database

Unlock the database using `fsyncUnlock()`. Forgetting this step will prevent the database from performing write operations:

```javascript
mongosh
db.fsyncUnlock();
```

#### Archive the Snapshot

Perform a complete copy of the snapshot volume, compress it with `gzip`, and redirect the output to an archive file:

```bash
exit
sudo dd status=progress if=/dev/vg0/mdb-snapshot | gzip > mdb-snapshot.gz
```

#### Restore the Archived Snapshot

**1. Create a new logical volume:**

```bash
sudo lvcreate --size 1G --name mdb-new vg0;
```

**2. Extract the snapshot and write it to the new logical volume:**

```bash
gzip -d -c mdb-snapshot.gz | sudo dd status=progress of=/dev/vg0/mdb-new
```

**3. Stop the MongoDB service:**

```bash
sudo systemctl stop -l mongod; sudo systemctl status -l mongod;
```

**4. Delete any existing MongoDB data files (demonstration only):**

```bash
sudo rm -r /var/lib/mongodb/*
```

**5. Unmount the MongoDB deployment:**

```bash
sudo umount /var/lib/mongodb
```

**6. Mount the restored logical volume on the MongoDB database directory:**

```bash
sudo mount /dev/vg0/mdb-new /var/lib/mongodb
```

**7. Start MongoDB and verify the restoration:**

```bash
sudo systemctl start -l mongod; sudo systemctl status -l mongod;
mongosh
show dbs
```

---

### Question 1
**Which Linux command can be used to restore an archived volume snapshot?** *(Select one)*

- **a. `dd`**
  - **Correct**: You can use the `dd` command to restore a volume snapshot. `dd` is a command-line utility in Linux that allows you to convert and copy data.

- **b. `cat`**
  - **Incorrect**: You cannot use `cat` to restore a volume snapshot. The `cat` command in Linux prints the contents of a file to the terminal.

- **c. `scp`**
  - **Incorrect**: You cannot use `scp` to restore a volume snapshot. The `scp` command in Linux allows you to securely transfer files to a remote host.

- **d. `man`**
  - **Incorrect**: You cannot use `man` to restore a volume snapshot. The `man` command is used to display the user manual for commands you can run in Linux.

---

### Question 2
**Volume snapshots should be stored separately from the MongoDB deployment.** *(True or False)*

- **a. True**
  - **Correct**: It's a good idea to store your backups on a **separate server** from the MongoDB deployment. This allows you to easily access your backups if your MongoDB deployment server becomes unavailable, and saves server resources for your deployment server.

- **b. False**
  - **Incorrect**: It **is** a good idea to store your backups on a separate server from the MongoDB deployment. This allows you to easily access your backups in case your MongoDB deployment server becomes unavailable and saves server resources.

---

## Lesson 4: Filesystem Archives on a MongoDB Server

### Code Summary: Filesystem Archives on a MongoDB Server

Review the following code, which demonstrates how to create and restore a filesystem archive.

#### Lock the Database

```javascript
mongosh
db.fsyncLock();
exit
```

#### Create a Snapshot Volume

```bash
sudo lvcreate --size 100M --snapshot --name mdb-snapshot /dev/vg0/mdb;
```

#### Unlock the Database

```javascript
mongosh
db.fsyncUnlock();
```

#### Archive the Snapshot

**1. Create a new temporary directory:**

```bash
exit
mkdir /tmp/mongodbsnap
```

**2. Mount the snapshot volume as read-only:**

```bash
sudo mount -t xfs -o nouuid,ro /dev/vg0/mdb-snapshot /tmp/mongodbsnap/
```

**3. Use `tar` to create a compressed archive:**

```bash
sudo tar -czvf mdb-snapshot.tar.gz -C /tmp/mongodbsnap/ .
```

#### Restore the Archived Snapshot

**1. Create a new directory and extract the compressed archive:**

```bash
sudo mkdir /mdb
sudo tar -xzf mdb-snapshot.tar.gz -C /mdb
```

**2. Stop the MongoDB service and set ownership of the restored directory:**

```bash
sudo systemctl stop -l mongod; sudo systemctl status -l mongod;
sudo chown -R mongodb:mongodb /mdb
```

**3. Update `dbPath` in the MongoDB configuration file:**

```bash
sudo nano /etc/mongod.conf
```

```yaml
storage:
  dbPath: /mdb
```

**4. Start MongoDB and verify the restoration:**

```bash
sudo systemctl start -l mongod; sudo systemctl status -l mongod;
mongosh
show dbs
```

---

### Question 1
**The snapshot volume does not need to be mounted to create a filesystem archive from it.** *(True or False)*

- **a. True**
  - **Incorrect**: To create a filesystem archive, the snapshot volume **must** be mounted.

- **b. False**
  - **Correct**: To create a filesystem archive, the snapshot volume **must** be mounted.

---

### Question 2
**Which command is used to create a new archive of all the files in a directory?** *(Select one)*

- **a. `tar`**
  - **Correct**: The `tar` command is used to create a new archive of all the files in a directory.

- **b. `cp`**
  - **Incorrect**: The `cp` command is not used to create an archive. It is used to **copy files or directories** from one location to another.

- **c. `ls`**
  - **Incorrect**: The `ls` command is not used to create an archive. It is used to **list the contents** of a directory.

- **d. `lsblk`**
  - **Incorrect**: The `lsblk` command is not used to create an archive. It is used to **list all block storage devices**.

---

## Lesson 5: Backing Up a MongoDB Deployment

### Code Summary: Backing Up a MongoDB Deployment

Review the following code, which demonstrates how to create a backup by using `mongodump`.

#### Create a User with the Backup Role

Connect to the `admin` database and create a new user with the `backup` role, which provides the minimum privileges needed for backing up data:

```javascript
mongosh admin

db.createUser({
  user: "backup-admin",
  pwd: "backup-pass",
  roles: ["backup"]
})
```

#### Use `mongodump` to Create a Backup

The following command creates a backup using these options:

| Option | Description |
|--------|-------------|
| `--oplog` | Captures incoming write operations during the dump |
| `--gzip` | Compresses the output file |
| `--archive` | Specifies the file location for the dump file |

```bash
mongodump \
  --oplog \
  --gzip \
  --archive=mongodump-april-2023.gz \
  "mongodb://backup-admin@mongod0.repleset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset&readPreference=secondary"
```

#### Create a Backup for a Specific Collection

Use `--db` and `--collection` to create a backup of a specific collection:

```bash
mongodump \
  --collection=neighborhoods \
  --gzip \
  --archive=mongodump-neighborhoods-2023.gz \
  "mongodb://backup-admin:@mongod0.repleset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/sample_restaurants?authSource=admin&replicaSet=replset"
```

---

### Question 1
**You want to create a backup of a replica set with access control enabled. What command should you use?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongodump --gzip --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset"` |
| B | `mongodump --gzip --archive=backup/mongodump-april "mongodb://mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?replicaSet=replset"` |
| C | `mongorestore --gzip --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?replicaSet=replset"` |

- **a. Option A**
  - **Correct**: This option provides a username, password, and `authSource` in the connection string, which enables access control.

- **b. Option B**
  - **Incorrect**: This option does **not** provide a username, password, and `authSource` in the connection string, which are all required to create a backup of a replica set with access control enabled.

- **c. Option C**
  - **Incorrect**: This option uses `mongorestore`, which **restores** a database from a backup, not creates one.

---

### Question 2
**You want to create backups in an archive file that include the date of the backup. What command should you use?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongodump --out=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |
| B | `mongodump --gzip --db=owners --collection=pets --file=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |
| C | `mongodump --gzip --db=owners --collection=pets --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |

- **a. Option A**
  - **Incorrect**: The `--out` option dumps the data into directories that correspond to the database and collection name — it does **not** create a named archive file.

- **b. Option B**
  - **Incorrect**: The `--file` option is **not** a valid `mongodump` option and will not create an archive file that includes the date.

- **c. Option C**
  - **Correct**: The `--archive` option creates backups in a named archive file. Combined with `--gzip`, the data is compressed for efficient storage.

---

### Question 3
**If you want to create a backup of a specific collection in the `sales` database, what command should you use?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongodump --gzip --db=owners --collection=pets --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |
| B | `mongodump --gzip --db=sales --collection=items --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |
| C | `mongodump --gzip --archive=backup/mongodump-april "mongodb://backup-admin:backup-pass@.../?authSource=admin&replicaSet=replset"` |

- **a. Option A**
  - **Incorrect**: This command backs up a database named **`owners`** and a collection named **`pets`**, not the `sales` database.

- **b. Option B**
  - **Correct**: This command backs up the **`sales`** database and the **`items`** collection within it.

- **c. Option C**
  - **Incorrect**: This command backs up **all** databases and collections stored in the replica set.

---

## Lesson 6: Restoring a MongoDB Deployment

### Code Summary: Restoring a MongoDB Deployment

Review the following code, which demonstrates how to restore a database from a backup using `mongorestore`.

#### Create a User with the Restore Role

```javascript
mongosh admin

db.createUser({
  user: "restore-admin",
  pwd: "restore-pass",
  roles: ["restore"]
})
```

#### Use `mongorestore` to Restore a Database

The following command restores a database using these options:

| Option | Description |
|--------|-------------|
| `--drop` | Removes any existing collections from the database before restoring |
| `--gzip` | Restores from a compressed file |
| `--oplogReplay` | Replays the oplog entries from `oplog.bson` |
| `--noIndexRestore` | Skips index restoration to reduce system impact |
| `--archive` | Specifies the file location of the dump file |

```bash
mongorestore \
  --drop \
  --gzip \
  --oplogReplay \
  --noIndexRestore \
  --archive=mongodump-april-2023.gz \
  "mongodb://restore-admin@mongod0.repleset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset"
```

---

### Question 1
**You want to restore a MongoDB replica set on three separate hosts by using `mongorestore`. What command should you use?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongorestore --drop --gzip --oplogReplay --archive=backup/mongodump-april-2023` |
| B | `mongodump --drop --gzip --oplogReplay --archive=backup/mongodump-april-2023 "mongodb://restore-admin:restore-pass@localhost:27017/?authSource=admin"` |
| C | `mongorestore --drop --gzip --archive=backup/mongodump-april-2023 "mongodb://restore-admin:restore-pass@mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset"` |

- **a. Option A**
  - **Incorrect**: This command does **not** specify the seedlist of hosts and the replica set in the URI, which are needed to restore a MongoDB replica set.

- **b. Option B**
  - **Incorrect**: This command uses `mongodump`, which **creates** a backup rather than restoring one. It also does not use a URI with three separate hosts.

- **c. Option C**
  - **Correct**: This command specifies the **seedlist of hosts** and the **replica set** in the URI, which are needed to restore a MongoDB replica set using `mongorestore`.

---

### Question 2
**What will the following operation do?** *(Select one)*

```bash
mongorestore --drop --gzip --archive=backup/mongodump-april-2023 \
  "mongodb://restore-admin:restore-pass@mongod0.replset.com:27017,mongod1.replset.com:27017,mongod2.replset.com:27017/?authSource=admin&replicaSet=replset"
```

- **a. Restore all databases and collections in the backup**
  - **Correct**: This operation will restore **all databases and collections**, as it does not use the `--db` and `--collection` options.

- **b. Restore only a database named "pets"**
  - **Incorrect**: This operation does **not** use the `--db` option, which is required to restore a specific database.

- **c. Back up all databases and collections**
  - **Incorrect**: The operation would need to use `mongodump` to **back up** databases and collections.

- **d. Restore all databases and collections in the backup to a standalone instance**
  - **Incorrect**: This operation will restore a **replica set** because a seedlist of hosts and the replica set name are specified in the connection string.

---

## Conclusion

### Self-Managed Backup & Recovery

In this unit, you learned how to:

- Identify key elements of backup plans
- Distinguish between **Recovery Point Objective (RPO)** and **Recovery Time Objective (RTO)**
- Define what a snapshot is
- Create a snapshot volume on Linux
- Create a filesystem archive on Linux
- Use `mongodump` to create a backup of a replica set that has access control enabled
- Use `mongorestore` to restore a MongoDB replica set

---

## Resources

Use the following resources to learn more about backup and recovery with MongoDB:

- **Lesson 1: Backup Plans on a MongoDB Server**
  - [MongoDB Backup Methods](https://www.mongodb.com/docs/manual/core/backups/)

- **Lesson 2: Filesystem Snapshots on a MongoDB Server**
  - [Snapshots Overview](https://www.mongodb.com/docs/manual/core/backups/#back-up-with-filesystem-snapshots)
  - [Snapshot Considerations](https://www.mongodb.com/docs/manual/core/backups/#snapshot-considerations)

- **Lesson 3: Filesystem Snapshot Volumes on a MongoDB Server**
  - [Back Up and Restore Using LVM on Linux](https://www.mongodb.com/docs/manual/tutorial/backup-and-restore-using-lvm-on-linux/)

- **Lesson 4: Filesystem Archives on a MongoDB Server**
  - [Back Up and Restore with Filesystem Snapshots](https://www.mongodb.com/docs/manual/tutorial/backup-and-restore-tools/)

- **Lesson 5: Backing Up a MongoDB Deployment**
  - [mongodump](https://www.mongodb.com/docs/database-tools/mongodump/)

- **Lesson 6: Restoring a MongoDB Deployment**
  - [mongorestore](https://www.mongodb.com/docs/database-tools/mongorestore/)
