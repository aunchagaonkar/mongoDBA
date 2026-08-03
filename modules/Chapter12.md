# MongoDB Database Administrator Tools

---

## Lesson 1: Get Started with DBA Tools

### Code Summary: Get Started with DBA Tools

Review the following code, which demonstrates how to install MongoDB Database Tools.

#### Install MongoDB Database Tools

Follow these steps to install MongoDB 6.0 Community Edition on LTS (long-term support) releases of Ubuntu Linux, using the `apt` package manager.

**1. Import the public key used by the package management system:**

```bash
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
```

**2. Create a list file for MongoDB:**

```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

**3. Reload the local package database:**

```bash
sudo apt-get update
```

**4. Install the latest stable version of MongoDB Community Edition:**

```bash
sudo apt-get install -y mongodb-org
```

---

### Question 1
**Which of the following statements are true about the MongoDB Database Tools suite?** *(Select all that apply)*

- **a. To gain access to the MongoDB Database Tools suite, the package must be installed separately from MongoDB Community Edition or MongoDB Enterprise Edition.**
  - **Incorrect**: The MongoDB Database Tools suite is **included** when you install MongoDB Community Edition or MongoDB Enterprise Edition. If you wish to use MongoDB Database Tools with an Atlas deployment, it must be installed separately.

- **b. MongoDB Database Tools are a suite of command-line utilities for working with MongoDB.**
  - **Correct**: MongoDB Database Tools are a suite of command-line utilities that support the management and administration of your deployment.

- **c. MongoDB Database Tools allow you to import and export data, restore backups, and view diagnostics for your deployments.**
  - **Correct**: MongoDB Database Tools fall into four categories: Backup and Restore, Data Import and Export, Diagnostic Tools, and GridFS Tools.

- **d. Version compatibility between MongoDB Database Tools and the target MongoDB server is crucial to ensure interoperability and data integrity.**
  - **Correct**: Before installing MongoDB Database Tools, be sure to refer to the compatibility table in MongoDB's documentation to verify version compatibility.

---

### Question 2
**Which of the following is an example of a task that can be accomplished with the MongoDB Database Tools?** *(Select all that apply)*

- **a. Importing JSON files from an external source into an Atlas cluster**
  - **Correct**: `mongoimport` can be used to import data into an Atlas cluster or a self-managed MongoDB deployment.

- **b. Monitoring the status of a self-managed MongoDB deployment from the MongoDB Shell**
  - **Incorrect**: `mongostat` can be used to monitor a self-managed deployment or an Atlas cluster from the **command line**, not from the MongoDB Shell.

- **c. Creating a backup of a small Atlas cluster**
  - **Correct**: `mongodump` can be used to create a backup of a small Atlas cluster or a self-managed MongoDB deployment.

- **d. Diagnosing problems when attempting to restore data from a BSON file to a self-managed MongoDB deployment**
  - **Correct**: `bsondump` can be used to diagnose issues that may arise when trying to restore data from a binary file to a self-managed MongoDB deployment.

---

## Lesson 2: Backup Tools

### Code Summary: Backup Tools

Review the following code, which demonstrates how to use the `mongodump` tool.

The following command creates a backup of the `sample_analytics` database and compresses the output into a file called `backup.gz`:

```bash
mongodump \
  -v \
  --gzip \
  --archive=backup.gz \
  "mongodb+srv://dbaTestAdmin@cluster1.xwgj1.mongodb.net/sample_analytics"
```

#### Commonly Used `mongodump` Options

| Option | Description |
|--------|-------------|
| `--out` | Specifies the output directory |
| `--db` | Specifies the database to back up |
| `--collection` | Specifies the collection to back up |
| `--readPreference` | Specifies the read preference |
| `--gzip` | Compresses the output |
| `--archive` | Outputs to a single archive file |
| `--oplog` | Includes oplog entries for point-in-time snapshots |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongodump`?** *(Select one)*

- **a. `mongodump` is a utility that is used to back up the contents of a sharded MongoDB cluster.**
  - **Incorrect**: The `mongodump` utility is used to back up the contents of simple standalone deployments and replica sets. It should **not** be used on sharded clusters.

- **b. `mongodump` is a utility that is used to restore the contents of a sharded MongoDB cluster.**
  - **Incorrect**: The `mongodump` utility is used to **back up** the contents of simple standalone deployments and replica sets. To restore a non-sharded cluster, you can use the `mongorestore` utility.

- **c. `mongodump` is a utility that is used to back up the contents of a simple MongoDB cluster.**
  - **Correct**: The `mongodump` utility is used to back up the contents of simple standalone deployments and replica sets.

- **d. `mongodump` is a utility that is used to restore the contents of a simple MongoDB cluster.**
  - **Incorrect**: The `mongodump` utility is used to **back up** the contents of simple standalone deployments and replica sets. To restore the contents of a non-sharded cluster, you can use the `mongorestore` utility.

---

### Question 2
**Which of the following commands will back up only the `grades` collection from the `sample_training` database in a dump directory?** *(Select all that apply)*

| Option | Command |
|--------|---------|
| A | `mongodump --db sample_training --collection grades` |
| B | `mongodump --collection grades "mongodb+srv://dbaTestAdmin@m0-example-cluster.iy0a1o4.mongodb.net/sample_training"` |
| C | `mongodump -v --gzip --archive=backup.gz "mongodb+srv://dbaTestAdmin@m0-example-cluster.iy0a1o4.mongodb.net/sample_training"` |
| D | `mongodump --db sample_training` |

- **a. Option A**
  - **Correct**: This command will back up the `grades` collection from the `sample_training` database in a standalone instance running on the default port.

- **b. Option B**
  - **Correct**: This command will back up the `grades` collection from the `sample_training` database in an Atlas cluster.

- **c. Option C**
  - **Incorrect**: This command will compress the data from the `sample_training` database and collapse it into a single file called `backup.gz`. This file will **not** be stored in a dump directory, and it does not filter to just the `grades` collection.

- **d. Option D**
  - **Incorrect**: This command will back up the **entire** `sample_training` database. To limit the backup to the `grades` collection, the command must include `--collection grades`.

---

## Lesson 3: Restore Tools

### Code Summary: Restore Tools

Review the following code, which demonstrates how to use the `mongorestore` tool.

The following command restores all databases from a compressed archive `backup.gz` and drops any existing data before restoring:

```bash
mongorestore \
  -v \
  --gzip \
  --archive=backup.gz \
  --drop \
  "mongodb+srv://dbaTestAdmin@cluster1.xwgj1.mongodb.net"
```

#### Commonly Used `mongorestore` Options

| Option | Description |
|--------|-------------|
| `--nsInclude` | Includes only matching namespaces |
| `--nsExclude` | Excludes matching namespaces |
| `--drop` | Drops the collection before restoring |
| `--noIndexRestore` | Skips restoring indexes |
| `--writeConcern` | Specifies write concern |
| `--gzip` | Decompresses a compressed archive |
| `--archive` | Reads from a single archive file |
| `--oplogReplay` | Replays oplog entries during restore |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongorestore`?** *(Select one)*

- **a. `mongorestore` is a utility that is used to back up the contents of a sharded MongoDB cluster.**
  - **Incorrect**: The `mongorestore` utility is used to **restore** the contents of simple standalone deployments and replica sets. It should not be used on sharded clusters. To back up a non-sharded cluster, use `mongodump`.

- **b. `mongorestore` is a utility that is used to restore the contents of a sharded MongoDB cluster.**
  - **Incorrect**: The `mongorestore` utility is used to restore the contents of simple standalone deployments and replica sets. It should **not** be used on sharded clusters.

- **c. `mongorestore` is a utility that is used to back up the contents of a simple MongoDB cluster.**
  - **Incorrect**: The `mongorestore` utility is used to **restore** the contents of simple standalone deployments and replica sets. To back up a non-sharded cluster, use `mongodump`.

- **d. `mongorestore` is a utility that is used to restore the contents of a simple MongoDB cluster.**
  - **Correct**: The `mongorestore` utility can be used to restore the contents of simple standalone deployments and replica sets.

---

### Question 2
**Which of the following commands will restore only the `grades` collection from the `sample_training` database from a dump directory?** *(Select all that apply)*

| Option | Command |
|--------|---------|
| A | `mongorestore --nsInclude=grades dump/` |
| B | `mongorestore --nsInclude=sample_training.grades dump/sample_training/school/grades` |
| C | `mongorestore --nsInclude=sample_training.grades dump/` |
| D | `mongorestore --db sample_training --collection grades` |

- **a. Option A**
  - **Incorrect**: This command uses `--nsInclude` to specify the `grades` collection, but it's missing the **database name** (`sample_training.grades`).

- **b. Option B**
  - **Incorrect**: This command uses `--nsInclude` to correctly specify the namespace, but it specifies the **incorrect folder path** for the backup.

- **c. Option C**
  - **Correct**: This command will restore the data from the `grades` collection in the `sample_training` database from the file created by the `mongodump` command.

- **d. Option D**
  - **Incorrect**: `mongorestore` uses the `--nsInclude` option to specify the database and collection, not `--db` and `--collection`.

---

## Lesson 4: Data Export Tools

### Code Summary: Data Export Tools

Review the following code, which demonstrates how to use the `mongoexport` tool.

The following command exports documents from the `transactions` collection where `transaction_count >= 50`, and writes the output to a canonical JSON file:

```bash
mongoexport \
  -v \
  --collection transactions \
  --query '{"transaction_count": {"$gte": 50}}' \
  --out export.json \
  --jsonFormat canonical \
  "mongodb+srv://dbaTestAdmin@cluster1.xwgj1.mongodb.net/sample_analytics"
```

#### Commonly Used `mongoexport` Options

| Option | Description |
|--------|-------------|
| `--collection` | Specifies the collection to export |
| `--type` | Specifies the output file type (`json` or `csv`) |
| `--out` | Specifies the output file path |
| `--jsonFormat` | Specifies the JSON format (`relaxed` or `canonical`) |
| `--query` | Filters documents to export using a query |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongoexport`?** *(Select one)*

- **a. `mongoexport` is a command-line tool that produces a binary dump of data stored in a MongoDB instance.**
  - **Incorrect**: The `mongoexport` utility produces an export of data stored in a MongoDB instance, but only in **JSON or CSV** file format, not binary.

- **b. `mongoexport` is a command-line tool that takes a JSON or CSV file and uploads it to a MongoDB instance.**
  - **Incorrect**: The `mongoexport` utility creates an export **from** a MongoDB instance. To restore or import data, you can use the `mongoimport` utility.

- **c. `mongoexport` is a command-line tool that produces a YAML export of data stored in a MongoDB instance.**
  - **Incorrect**: The `mongoexport` utility produces exports only in **JSON or CSV** file format, not YAML.

- **d. `mongoexport` is a command-line tool that produces a JSON or CSV export of data stored in a MongoDB instance.**
  - **Correct**: The `mongoexport` utility is used to produce a JSON or CSV export of data stored in a MongoDB instance. To restore the contents of a cluster, you can use the `mongoimport` utility.

---

### Question 2
**Which of the following commands will export only the `grades` collection from the `sample_training` database?** *(Select all that apply)*

| Option | Command |
|--------|---------|
| A | `mongoexport --collection=grades --db=sample_training --out=grades.json` |
| B | `mongoexport --ns=sample_training.grades --out=grades.json` |
| C | `mongoexport --collection=grades --db=sample_training --out=grades.yaml` |
| D | `mongoexport --db=sample_training --out=grades.json` |

- **a. Option A**
  - **Correct**: This command will successfully export the `grades` collection from the `sample_training` database to a file called `grades.json`.

- **b. Option B**
  - **Incorrect**: The options to specify the database and collection in `mongoexport` are `--collection` and `--db`, not `--ns`.

- **c. Option C**
  - **Incorrect**: You **cannot** export a YAML file by using `mongoexport`. Supported formats are JSON and CSV.

- **d. Option D**
  - **Incorrect**: This command will export the **entire** `sample_training` database. To limit the export to the `grades` collection, the command must include `--collection grades`.

---

## Lesson 5: Data Import Tools

### Code Summary: Data Import Tools

Review the following code, which demonstrates how to use the `mongoimport` tool.

The following command imports documents from `export.json` into a collection called `newCollection` in the `test` database, dropping existing data before inserting:

```bash
mongoimport \
  -v \
  --collection newCollection \
  --type json \
  --mode insert \
  --drop \
  --file export.json \
  "mongodb+srv://dbaTestAdmin@cluster1.xwgj1.mongodb.net/test"
```

#### Commonly Used `mongoimport` Options

| Option | Description |
|--------|-------------|
| `--db` | Specifies the target database |
| `--collection` | Specifies the target collection |
| `--type` | Specifies the input file type (`json` or `csv`) |
| `--mode` | Specifies the import mode (`insert`, `upsert`, `merge`, `delete`) |
| `--upsertFields` | Specifies fields to use for matching documents during upsert |
| `--drop` | Drops the collection before importing |
| `--file` | Specifies the input file path |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongoimport`?** *(Select all that apply)*

- **a. `mongoimport` is a command-line tool that is used to create a new database with a JSON or CSV file of data from another database.**
  - **Correct**: The `mongoimport` utility is used to add data to a new database from a CSV or JSON file.

- **b. `mongoimport` is a command-line tool that is used to restore the contents of a database with a JSON or CSV file of data from a database.**
  - **Correct**: The `mongoimport` utility is used to restore an existing database with data from a CSV or JSON file.

- **c. `mongoimport` is a command-line tool that is used to directly connect two databases together and have them share data.**
  - **Incorrect**: The `mongoimport` utility is used to **add data** to a database, but it does not connect databases.

- **d. `mongoimport` is a command-line tool that produces a JSON or CSV export of data that's stored in a MongoDB instance.**
  - **Incorrect**: The `mongoimport` utility is used to **restore** the contents of a database from a CSV or JSON file. To create that file, you can use the `mongoexport` utility.

---

### Question 2
**Which of the following commands will import data to a collection called `grades` in the `students` database from a file named `grades.json`, and replace documents in the database that match the documents in the import file?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongoimport --collection=grades --db=students --file=grades.json` |
| B | `mongoimport --collection=grades --db=students --mode=delete --file=grades.json` |
| C | `mongoimport --collection=grades --db=students --mode=upsert --file=grades.json` |
| D | `mongoimport --collection=grades --db=students --mode=merge --file=grades.json` |

- **a. Option A**
  - **Incorrect**: To replace documents in the database that match the documents in the import file, you must include `--mode=upsert`.

- **b. Option B**
  - **Incorrect**: `--mode=delete` **deletes** existing documents in the database that match a document in the import file. To **replace** matching documents, you must use `--mode=upsert`.

- **c. Option C**
  - **Correct**: With `--mode=upsert`, `mongoimport` replaces existing documents in the database that match a document in the import file with the document from the import file.

- **d. Option D**
  - **Incorrect**: `--mode=merge` **merges fields** from a new record with an existing document. To **replace** documents that match, you must use `--mode=upsert`.

---

## Lesson 6: Diagnostic Tools: mongostat

### Code Summary: Diagnostic Tools — `mongostat`

Review the following code, which demonstrates how to use the `mongostat` tool.

The following example uses `mongostat` to show the server statistics of a MongoDB cluster:

```bash
mongostat "mongodb+srv://dbaTestAdmin@cluster0.mntqoh9.mongodb.net"
```

The next example shows insert rate, query rate, and command rate in groups of three rows, polling every 2 seconds:

```bash
mongostat \
  -o='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' \
  --rowcount=3 \
  "mongodb+srv://dbaTestAdmin@m0cluster-restored.iy0a1o4.mongodb.net" \
  2
```

#### Commonly Used `mongostat` Options

| Option | Description |
|--------|-------------|
| `-o` | Specifies custom fields/expressions to display |
| `-O` | Appends custom fields to the default output |
| `--rowcount` | Limits the number of output rows |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongostat`?** *(Select one)*

- **a. `mongostat` is a diagnostic tool that provides a day-old view of a currently running MongoDB instance.**
  - **Incorrect**: The `mongostat` tool provides the **current** status of a MongoDB instance in real time, not day-old information.

- **b. `mongostat` is a diagnostic tool that provides a real-time view of a currently running MongoDB instance.**
  - **Correct**: The `mongostat` tool provides a real-time overview of a currently running MongoDB instance.

- **c. `mongostat` is a diagnostic tool that provides a list of all MongoDB instances that are running on your local machine.**
  - **Incorrect**: The `mongostat` tool is used to get statistics for a **particular** MongoDB instance that could be running on any server.

- **d. `mongostat` is a diagnostic tool that provides a history of all actions performed in a cluster.**
  - **Incorrect**: The `mongostat` tool doesn't show a history of a running instance — it shows the **current** status.

---

### Question 2
**Which of the following commands will show the status of the insert rate, query rate, and command rate of the MongoDB instance running at `mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net` with a polling time of 2 seconds?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongostat -o='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' "mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net" 2` |
| B | `mongostat -o='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' "mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net" 2000` |
| C | `mongostatus -stats='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate' "mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net" 2` |
| D | `mongostat -stats='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' "mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net" 2` |

- **a. Option A**
  - **Correct**: This command will show the insert rate, query rate, and command rate with a 2-second polling interval.

- **b. Option B**
  - **Incorrect**: The polling time should be given in **seconds**, not milliseconds. `2000` means 2000 seconds, not 2.

- **c. Option C**
  - **Incorrect**: `mongostatus` is not a valid command. You must use `mongostat` with the `-o` option to specify what statistics to show.

- **d. Option D**
  - **Incorrect**: You must use the `-o` option (not `-stats`) to specify what statistics to show in the output.

---

## Lesson 7: Diagnostic Tools: mongotop

### Code Summary: Diagnostic Tools — `mongotop`

Review the following code, which demonstrates how to use the `mongotop` tool.

The following command outputs the read and write times of a cluster, polling every 2 seconds and showing output in groups of 3 rows:

```bash
mongotop "mongodb+srv://dbaTestAdmin@cluster0.mntqoh9.mongodb.net" 2 --rowcount=3
```

#### Commonly Used `mongotop` Options

| Option | Description |
|--------|-------------|
| `--rowcount` | Limits the number of output rows |
| `--json` | Outputs data in JSON format |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongotop`?** *(Select one)*

- **a. `mongotop` provides a method to track the amount of time a MongoDB instance spends writing data, but it does not provide information about time spent reading data.**
  - **Incorrect**: The `mongotop` utility tracks both the amount of time a MongoDB instance spends **writing and reading** data.

- **b. `mongotop` is a utility that tracks the database users who spend the most time modifying the data.**
  - **Incorrect**: The `mongotop` utility is not used to track database user activity, but it can track the time spent on data operations.

- **c. `mongotop` provides a method to track the amount of time a MongoDB instance spends reading and writing data.**
  - **Correct**: The `mongotop` utility tracks the amount of time a MongoDB instance spends reading and writing data. It provides statistics **per collection**.

- **d. `mongotop` is a utility to track the amount of space left in a MongoDB instance running on Atlas.**
  - **Incorrect**: The `mongotop` utility is used to track **activity**, not space. To check the size of your database, you can use the `dbStats()` method.

---

### Question 2
**Which of the following commands will show the read and write activity of a MongoDB instance at `mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net` every 30 seconds?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongotop 30000 --uri='mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net'` |
| B | `mongotop 30 --uri='mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net'` |
| C | `mongotop --int=30` |
| D | `mongotop 30 --database='mongodb+srv://username@businesscluster.iy0a1o4.mongodb.net'` |

- **a. Option A**
  - **Incorrect**: The `mongotop` command expects the interval to be specified in **seconds**. `30000` would mean 30,000 seconds.

- **b. Option B**
  - **Correct**: This command will show the read and write activity on the cluster every **30 seconds**.

- **c. Option C**
  - **Incorrect**: This command is missing the **URI** for the database, and `--int` is not a valid option for specifying the update interval.

- **d. Option D**
  - **Incorrect**: Use the `--uri` option to specify the URI of the cluster, not `--database`.

---

## Lesson 8: Diagnostic Tools: bsondump

### Code Summary: Diagnostic Tools — `bsondump`

Review the following code, which demonstrates how to use the `bsondump` tool.

The following command outputs documents from a `.bson` file in a readable JSON format:

```bash
bsondump --pretty accounts.bson
```

The next example uses `bsondump` with the debug option to see different data types and sizes:

```bash
bsondump --type=debug accounts.bson
```

#### Commonly Used `bsondump` Options

| Option | Description |
|--------|-------------|
| `--outFile` | Specifies the output file path |
| `--pretty` | Formats the output in a human-readable indented style |
| `--type` | Specifies output type (`json` or `debug`) |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `bsondump`?** *(Select one)*

- **a. `bsondump` converts the JSON from the `mongodump` command into a binary format for storage efficiency.**
  - **Incorrect**: The `mongodump` utility outputs data as BSON (binary format). The `bsondump` utility helps make those files more **human-readable**, not more binary.

- **b. `bsondump` outputs the entire database in binary format in the MongoDB Shell.**
  - **Incorrect**: The `bsondump` utility enables users to view data in a **human-readable** format, rather than in binary format.

- **c. `bsondump` encrypts BSON files for storage on less secure servers.**
  - **Incorrect**: The `bsondump` utility does **not** encrypt files — it is meant to make files human-readable.

- **d. `bsondump` converts BSON files into human-readable formats, including JSON.**
  - **Correct**: The `bsondump` utility is used for converting BSON documents into a human-readable format, such as JSON.

---

### Question 2
**Which of the following commands will output a prettified JSON file from the `grades.bson` file?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `bsondump --outFile=grades.json --pretty grades.bson` |
| B | `bsondump grades.bson->grades.json --pretty` |
| C | `bsondump --inputFile=grades.bson --outputFile=grades.json --pretty` |
| D | `bsondump --outFile=grades.json grades.bson` |

- **a. Option A**
  - **Correct**: This command will create a prettified JSON file from the `grades.bson` file.

- **b. Option B**
  - **Incorrect**: You must specify `grades.bson` as the file to convert, and then use `--outFile` to specify the output path. The `->` redirect syntax is not valid here.

- **c. Option C**
  - **Incorrect**: `--inputFile` is **not a valid option** for the `bsondump` command. No option is necessary when specifying a BSON file — just pass it directly.

- **d. Option D**
  - **Incorrect**: This command is missing the `--pretty` option needed to format the JSON output in a human-readable indented style.

---

## Lesson 9: MongoDB as a Filesystem

### Code Summary: MongoDB as a Filesystem — `mongofiles`

Review the following code, which demonstrates how to use the `mongofiles` tool.

The following command adds a file called `myVideoFile.mp4` to the GridFS store on the `myFiles` database:

```bash
mongofiles \
  -v \
  put myVideoFile.mp4 \
  "mongodb+srv://dbaTestAdmin@cluster0.mntqoh9.mongodb.net/myFiles"
```

#### Commonly Used `mongofiles` Options

| Option | Description |
|--------|-------------|
| `--db` | Specifies the target database |
| `--local` | Specifies the local file path |
| `--replace` | Replaces an existing file with the same name |
| `--writeConcern` | Specifies write concern |
| `--readPreference` | Specifies read preference |

---

### Question 1
**Which of the following best describes the MongoDB Database Tool `mongofiles`?** *(Select one)*

- **a. `mongofiles` enables you to store BSON documents in a human-readable format instead of binary.**
  - **Incorrect**: The `mongofiles` utility doesn't change the format of your data — it enables you to **manipulate files** in GridFS.

- **b. `mongofiles` enables you to manipulate files stored in your MongoDB instance in GridFS objects from the command line.**
  - **Correct**: `mongofiles` is a utility that enables you to manipulate files stored in your MongoDB instance in GridFS objects from the command line.

- **c. `mongofiles` enables you to manipulate files stored in your MongoDB instance in the Atlas UI.**
  - **Incorrect**: The `mongofiles` utility enables you to manipulate files stored in your MongoDB instance, but **not** in the Atlas UI — it is a command-line tool.

- **d. `mongofiles` enables you to run diagnostics on files stored in your MongoDB instance to find any documents that do not match the schema.**
  - **Incorrect**: The `mongofiles` utility is **not used for diagnostics** — it is used to manipulate files stored in GridFS.

---

### Question 2
**Which of the following commands will list all of the files in the GridFS collection in the `grades` database that start with the word "final"?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `mongofiles -d=grades list final` |
| B | `mongofiles -d=grades list --files="final"` |
| C | `mongofiles -d=grades search final` |
| D | `mongofiles -d=grades put final` |

- **a. Option A**
  - **Correct**: This command will output a list of files in the `grades` database's GridFS collection that **start with** the word "final".

- **b. Option B**
  - **Incorrect**: The command to specify the word files start with does **not require a flag** — it is passed directly as an argument to the `list` command.

- **c. Option C**
  - **Incorrect**: The `search` option will list files whose names **contain** the string "final". To list files that **begin with** "final", you must use the `list` command.

- **d. Option D**
  - **Incorrect**: The `put` command is used to **add files** to GridFS, not to list them.

---

## Conclusion

### MongoDB Database Administrator Tools

In this unit, you learned how to:

- Install MongoDB Database Tools on Ubuntu Linux
- Identify the basic syntax and commonly used options for MongoDB Database Tools
- Use `mongodump` as a part of a backup strategy for small deployments
- Use `mongorestore` as a part of a recovery strategy to restore data backed up with `mongodump`
- Use `mongoexport` to move data between MongoDB and other systems
- Use `mongoimport` to import data into MongoDB
- Use `mongostat` to view server statistics for a currently running MongoDB deployment in real time
- Use `mongotop` to monitor the amount of time that a MongoDB instance spends reading and writing data per collection
- Use `bsondump` to convert BSON files into human-readable formats
- Use `mongofiles` as an interface between files stored in your local file system and GridFS

---

## Resources

Use the following resources to learn more about the MongoDB Database Tools suite:

- **Lesson 1: Get Started with DBA Tools**
  - [Install MongoDB](https://www.mongodb.com/docs/manual/installation/)
  - [The MongoDB Database Tools Documentation](https://www.mongodb.com/docs/database-tools/)

- **Lesson 2: Backup Tools**
  - [mongodump](https://www.mongodb.com/docs/database-tools/mongodump/)

- **Lesson 3: Restore Tools**
  - [mongorestore](https://www.mongodb.com/docs/database-tools/mongorestore/)

- **Lesson 4: Data Export Tools**
  - [mongoexport](https://www.mongodb.com/docs/database-tools/mongoexport/)

- **Lesson 5: Data Import Tools**
  - [mongoimport](https://www.mongodb.com/docs/database-tools/mongoimport/)

- **Lesson 6: Diagnostic Tools: mongostat**
  - [mongostat](https://www.mongodb.com/docs/database-tools/mongostat/)

- **Lesson 7: Diagnostic Tools: mongotop**
  - [mongotop](https://www.mongodb.com/docs/database-tools/mongotop/)

- **Lesson 8: Diagnostic Tools: bsondump**
  - [bsondump](https://www.mongodb.com/docs/database-tools/bsondump/)

- **Lesson 9: MongoDB as a Filesystem**
  - [mongofiles](https://www.mongodb.com/docs/database-tools/mongofiles/)
  - [GridFS](https://www.mongodb.com/docs/manual/core/gridfs/)
