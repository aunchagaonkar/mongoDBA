# MongoDB Logging Basics

---

## Lesson 1: MongoDB Logs in Atlas

### Code Summary: MongoDB Logs in Atlas

The following code demonstrates how to download logs by using the Atlas CLI.

Downloading logs requires the **"Project Data Access Read Only"** role or greater in MongoDB Atlas. To determine if your Atlas user has this role, run the following command in the Atlas CLI:

```bash
atlas project users list -o json
```

This command returns a JSON object for each member of the project. This output uses the Atlas API role names, so look for the equivalent role for **"Project Data Access Read Only,"** which is called `GROUP_DATA_ACCESS_READ_ONLY`.

To download logs by using the Atlas CLI, use the following command:

```bash
atlas logs <hostname> <file>
```

For example, if you want to download the `mongod` log file for the past 30 days from the primary node of your Atlas cluster, you would run the following:

```bash
atlas logs download uml3-shard-00-00.xwgj1.mongodb.net mongodb.gz
```

Log files will be compressed in a `.gz` archive. To extract a `.gz` archive in Linux, use the following command:

```bash
gunzip mongodb.log.gz
```

---

### Question 1
**Which of the following are valid methods to download logs from M10-and-above Atlas clusters?** *(Select all that apply)*

- **a. Using the Atlas UI**
  - **Correct**: When using the Atlas dashboard, you can download logs for a node in a given cluster by first navigating to the **Database** tab and locating the cluster in the list. Then click the ellipsis icon to expand the drop-down menu and select the **Download Logs** option.

- **b. Contacting MongoDB Support**
  - **Incorrect**: MongoDB Support can help with a lot of issues, but there are easier and quicker ways to acquire logs for your Atlas deployment.

- **c. Using the Atlas CLI**
  - **Correct**: You can download logs for several different services (`mongod`, `mongos`, `mongodbsql`, etc.) by using the `atlas logs download <hostname> <filename>` command in the Atlas CLI.

- **d. Using a service like SCP (Secure Copy Protocol) or FTP (File Transfer Protocol)**
  - **Incorrect**: While you cannot use SCP or FTP, Atlas logs can be downloaded easily by using a few different methods.

---

### Question 2
**What is the minimum privilege you need to download logs from an Atlas cluster?** *(Select one)*

- **a. Organization Read Only**
  - **Incorrect**: The Organization Read Only role gives users of an organization the ability to read settings, view users, view projects, and view some billing information within the organization.

- **b. Project Data Access Read Only**
  - **Correct**: The Project Data Access Read Only role gives users the ability to view databases and collections, view documents (in the UI only), view indexes, view and download process and audit logs, and view the Performance Advisor, profiler, and real-time stats. This role is also known by its API equivalent, `GROUP_DATA_ACCESS_READ_ONLY`.

- **c. Project Read Only**
  - **Incorrect**: This limited role grants users view-only access to the project control panel. This role does not allow users to view or edit data in the data explorer or to access log files.

- **d. Organization Member**
  - **Incorrect**: This role applies to an organization rather than a project. Organization members can access projects that they have been invited to, but they need to be explicitly added.

---

## Lesson 2: MongoDB Logs on Self-Managed Instances

### Code Summary: MongoDB Logs on Self-Managed Instances

The following sections explain how to locate logs in self-managed MongoDB instances, as well as check access permissions, access the log file, and retrieve log messages.

#### Default Location on Linux

On Linux, the MongoDB log file is found in the following directory by default and is named `mongod.log`:

```
/var/log/mongodb/mongod.log
```

#### Check Permissions of the Log File

To check the file permissions of the `mongod.log` file, use the `ls` command, which lists all files and subdirectories in the current directory. When `ls` is used with the `-l` option, long format is used. This option includes the user and group that can access the file.

```bash
ls -l /var/log/mongodb/mongod.log
```

#### Access the Log File

To access the log file, prepend the `sudo` command to the directory name. The following example uses the `head` command to print the first five lines of the `mongod.log` file:

```bash
sudo head -5 /var/log/mongodb/mongod.log
```

If the log file is not in its default location, check the `mongod.conf` file to determine if an alternate path was provided. The following example uses the `cat` command to check the value of the `systemLog.path` property:

```bash
sudo cat /etc/mongod.conf
```

#### Retrieve Log Messages

To show recent global log messages from the RAM cache in `mongosh`, use the `show log` helper and provide it with one of the available filters, such as `global` or `startupWarnings`.

```javascript
show log <type>
```

To view the available filters that can be provided to the `show log` helper, use the following helper command in `mongosh`:

```javascript
show logs
```

The `mongosh` helper `show log global` internally calls the `getLog` command to return recent log messages from the RAM cache:

```javascript
db.adminCommand( { getLog:'global'} )
```

---

## Lesson 3: MongoDB Log Events

### Code Summary: MongoDB Log Events

To filter for specific types of log messages, you can use the `tags` field, which is an array of strings.

For example, to view all the messages with a tag of `startupWarnings`, you would use the following command in `mongosh`:

```javascript
show log startupWarnings
```

---

### Question 1
**Given the following log message, identify the correct field name that relates to the operating system thread that prompted the log message.** *(Select one)*

```json
{
  "t": {
    "$date": "2023-05-12T21:09:58.661+00:00"
  },
  "s": "I",
  "c": "REPL",
  "id": 21358,
  "ctx": "conn54",
  "msg": "Replica set state transition",
  "attr": {
    "newState": "SECONDARY",
    "oldState": "PRIMARY"
  }
}
```

- **a. The `"t"` field**
  - **Incorrect**: The `"t"` field in a MongoDB log message relates to the **timestamp** of the log message.

- **b. The `"s"` field**
  - **Incorrect**: The `"s"` field in a MongoDB log message relates to the **severity** of the log message.

- **c. The `"ctx"` field**
  - **Correct**: The `"ctx"` field in a MongoDB log message relates to the **thread** that generated the log message, in this case, `"conn54"`. This field will be present in all log messages.

- **d. The `"id"` field**
  - **Incorrect**: The `"id"` field in a MongoDB log message relates to the **unique ID** of the log message.

---

## Lesson 4: MongoDB Server Log Customizations

### Code Summary: MongoDB Server Log Customizations

Review the following code, which demonstrates how to:

- Set a `slowms` threshold
- Find slow operations in a log
- Set the verbosity level
- Generate log messages related to slow operations

#### Set a `slowms` Threshold

To set a `slowms` threshold for an M10-or-above Atlas cluster or a self-managed MongoDB deployment, use the `db.setProfilingLevel()` method in `mongosh`. This method accepts two parameters: the profiler level and an options object.

- `0` — Disables profiling completely
- `1` — Profiles operations that take longer than the threshold
- `2` — Profiles all operations

In the following example, the command leaves the profiler disabled but changes the `slowms` threshold to 30 milliseconds:

```javascript
db.setProfilingLevel(0, { slowms: 30 })
```

> **Note:** When the profiler is disabled, `db.setProfilingLevel()` configures which slow operations are written to the diagnostic log.

#### Find Slow Operations in a Log

First, find a specific document in the `listingsAndReviews` collection:

```javascript
db.listingsAndReviews.findOne({ "host.host_id": '1282196'})
```

Then find all documents sorted by the number of listings. Without an index, the query will be slow:

```javascript
db.listingsAndReviews.find({}).sort( {"host.host_total_listings_count":-1})
```

To find log messages related to slow operations, use the `grep` command to find instances of the phrase `"Slow query"`, and then pipe the result into `jq`, a utility for processing and pretty-printing JSON:

```bash
sudo grep "Slow query" /var/log/mongodb/mongod.log | jq
```

#### Set the Verbosity Level

To set the verbosity level on a self-managed instance using the configuration file, first open the file in Vim:

```bash
vim /etc/mongod.conf
```

To set a **global** verbosity level for all components, add the following property under the `systemLog` section:

```yaml
...
systemLog:
  verbosity: 1
...
```

To set the verbosity level for a **single component**, add the following to the `systemLog` section:

```yaml
...
systemLog:
  ...
  component:
    index:
      verbosity: 1
...
```

Restart the `mongod` service on a Linux environment managed by `systemctl`:

```bash
sudo systemctl mongod restart
```

#### Generate Log Messages Related to Slow Operations

To generate log messages related to the index component, create an index on the `host` field to support the slow query. Use the `createIndex()` method in `mongosh` with the `--eval` parameter and `--quiet` option:

```bash
mongosh "mongodb://localhost:27017/sample_airbnb" --quiet --eval "db.listingsAndReviews.createIndex({ host: 1 })"
```

To find log messages related to the `INDEX` component:

```bash
sudo grep INDEX /var/log/mongodb/mongod.log | jq
```

---

### Question 1
**Which of the following statements are true regarding the `slowms` property?** *(Select all that apply)*

- **a. The `slowms` threshold can be set only for self-managed deployments. It cannot be changed for Atlas clusters.**
  - **Incorrect**: The `slowms` threshold can be set for both self-managed deployments and M10-or-above Atlas clusters.

- **b. The `slowms` property defines the maximum amount of time for an operation to complete before it's considered slow.**
  - **Correct**: Any operations that run longer than the `slowms` threshold will be written to the diagnostic log.

- **c. The default value for the `slowms` property is set to 100 milliseconds.**
  - **Correct**: The default value for the `slowms` property can be adjusted at any time.

- **d. The only way to set a custom threshold for the `slowms` property is by using the `db.setProfilingLevel()` method in `mongosh`.**
  - **Incorrect**: You can set a custom threshold in one of three ways: by using the `db.setProfilingLevel()` method in `mongosh`, by using the `slowms` launch parameter to the MongoDB service, or by adding the `slowOpsThreshold` property in the configuration file.

---

### Question 2
**Which of the following options would successfully set a `slowms` threshold to 50 milliseconds in the MongoDB Shell?** *(Select one)*

| Option | Command |
|--------|---------|
| A | `db.setProfilingLevel(0, { slowms: 500 })` |
| B | `db.setProperty(0, { slowms: 500 })` |
| C | `db.setProperty(0, { slowms: 50 })` |
| D | `db.setProfilingLevel(0, { slowms: 50 })` |

- **a. Option A**
  - **Incorrect**: This method would set a `slowms` threshold to **500** milliseconds, not 50 milliseconds.

- **b. Option B**
  - **Incorrect**: `db.setProperty()` is not a valid MongoDB Shell method and will return an error when run.

- **c. Option C**
  - **Incorrect**: `db.setProperty()` is not a valid MongoDB Shell method and will return an error when run.

- **d. Option D**
  - **Correct**: This command will set a `slowms` threshold to **50 milliseconds** in the MongoDB Shell.

---

### Question 3
**Which of the following statements are true regarding the verbosity of the logs?** *(Select all that apply)*

- **a. The verbosity level refers to the amount of debugging information to include in the log file.**
  - **Correct**: The verbosity level is set to `0` by default to preserve disk space.

- **b. To increase the verbosity for only one component, such as the `INDEX` component, edit the configuration file for a self-managed deployment.**
  - **Correct**: You can adjust the verbosity for a single component or for all components in the configuration file.

- **c. The `setLogLevel()` method can be used on Atlas clusters to adjust the verbosity of the logs.**
  - **Incorrect**: The `setLogLevel()` method can be used to adjust the verbosity level only on **self-managed** deployments.

- **d. The verbosity level for a self-managed deployment can be adjusted by setting the `verbosity` property under the `systemLog` section of the configuration file.**
  - **Correct**: Setting the `verbosity` property to a value of 1 to 5 determines the amount of debugging information that is included in the log.

---

## Lesson 5: MongoDB Server Log Rotation and Retention

### Code Summary: MongoDB Server Log Rotation and Retention

#### Rotating Logs

To rotate logs for a self-managed `mongod` deployment, use `db.adminCommand()` in `mongosh`:

```javascript
db.adminCommand( { logRotate : 1 } )
```

Alternatively, you can issue the `SIGUSR1` signal to the `mongod` process:

```bash
sudo kill -SIGUSR1 $(pidof mongod)
```

#### Rotating Logs Using Rename and Reopen

To start `mongod` with MongoDB's standard **rename** log rotation behavior, invoke the daemon with the `--logpath` argument (rename is the default):

```bash
mongod -v --logpath /var/log/mongodb/server1.log
```

To start the `mongod` process with the **reopen** approach, use the following arguments:

| Argument | Description |
|----------|-------------|
| `--logpath` | Sends all diagnostic logging information to a log file |
| `--logappend` | Appends new entries to the end of the existing log file |
| `--logRotate` | Determines the behavior for the `logRotate` command (`rename` or `reopen`) |

```bash
mongod -v --logpath /var/log/mongodb/server1.log --logRotate reopen --logappend
```

#### Automating Log Rotation with the `logrotate` Service

To automate the rotation of MongoDB logs using the Linux `logrotate` service, open the `mongod.conf` file:

```bash
sudo vim /etc/mongod.conf
```

Add the following lines to enable the **reopen** method of log rotation:

```yaml
...
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log
  logRotate: reopen
...
```

Create a `logrotate` script in the `etc` directory:

```bash
sudo vim /etc/logrotate.d/mongod.conf
```

> **Note:** The MongoDB configuration file and the logrotate script have the same filename. The following file should be created in `/etc/logrotate.d/` and named `mongod.conf`.

To configure `logrotate` to send a `SIGUSR1` signal to `mongod` once per day, or when the file size reaches 10 MB:

```
/var/log/mongodb/mongod.log {
   daily
   size
   rotate 10
   missingok
   compress
   compresscmd /usr/bin/bzip2        # command to compress the file
   uncompresscmd /usr/bin/bunzip2    # command to uncompress the file
   compressoptions -9                # options for the compression utility
   compressext .bz2                  # file format of the compressed archive
   delaycompress                     # wait to compress files until it's an opportune time
   notifempty                        # don't bother compressing if the log file is empty
   create 640 mongodb mongodb        # creates the log file with specific permissions
   sharedscripts                     # don't run multiple rotations at once
   postrotate                        # tell mongod to rotate, remove empty files
       /bin/kill -SIGUSR1 `cat /var/run/mongodb/mongod.pid 2>/dev/null` >/dev/null 2>&1
       find /var/log/mongodb -type f -size 0 -regextype posix-awk -regex "^\/var\/log\/mongodb\/mongod\.log\.[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}-[0-9]{2}-[0-9]{2}$" -execdir rm {} \; >/dev/null 2>&1
   endscript                         # end of the script
}
```

With the `logrotate` file in place, restart the `mongod` service:

```bash
sudo systemctl restart mongod
```

#### Testing the `logrotate` Configuration

View the `mongod.log` file in real time:

```bash
sudo tail -F /var/log/mongodb/mongod.log
```

Then tell the `mongod` process to rotate the logs:

```bash
sudo kill -SIGUSR1 $(pidof mongod)
```

In the `mongod.log` file, notice the following line, indicating that the log was reopened (language may vary by Linux distribution):

```
tail: /var/log/mongodb/mongod.log: file truncated;
```

---

### Question 1
**What is the maximum number of days that MongoDB Atlas will retain logs for?** *(Select one)*

- **a. 30 days**
  - **Correct**: MongoDB Atlas will retain the last **30 days** of log messages for each service run on your cluster (e.g., `mongod`, `mongos`, `mongodbsql`). These logs are downloadable by using the Atlas UI or Atlas CLI on M10-or-above Atlas clusters.

- **b. 90 days**
  - **Incorrect**: MongoDB Atlas retains logs, but not for 90 days.

- **c. 120 days**
  - **Incorrect**: MongoDB Atlas retains logs, but not for 120 days.

- **d. 365 days**
  - **Incorrect**: MongoDB Atlas retains logs, but not for an entire year.

---

### Question 2
**Which of the following are valid methods for rotating `mongod` log files?** *(Select all that apply)*

- **a. Using the `db.adminCommand({ logRotate: 1 })` method in `mongosh`**
  - **Correct**: Rotating logs can be done in `mongosh` by using the `db.adminCommand({ logRotate: 1 })` method. Depending on your configuration, MongoDB will either reopen the current log file or rename the existing log file and open a new one.

- **b. Forcefully ending the `mongod` process by using `pkill $(pidof mongod)`**
  - **Incorrect**: Ending the `mongod` process by issuing the `pkill` command will not cause the logs to rotate. You also run the risk of losing some messages if they were not logged before the process was ended.

- **c. Running the `rotate logs` helper in `mongosh`**
  - **Incorrect**: While it's possible to initiate a log rotation in `mongosh`, the `rotate logs` helper is not an actual command.

- **d. Issuing a `SIGUSR1` signal to the `mongod` process manually or automatically by using the Linux `logrotate` utility**
  - **Correct**: The `mongod` process will respond to a `SIGUSR1` signal in Linux by initiating a log rotation. This method allows log rotations to occur without using `mongosh`.

---

## Conclusion

### MongoDB Logging Basics

In this unit, you learned how to:

- Download logs by using the Atlas UI and CLI
- Verify user access permissions for log files on a self-managed instance
- Locate logs generated by a self-managed `mongod` instance with the file, syslog, or standard output
- Get the most recent lines in the log by using the `db.adminCommand({getLog: <type>})` method, `show log <name>`, and `show logs`
- Identify fields in log messages and their meanings
- Identify the different types of information that can be found in log messages, such as OS-level warnings, authorization attempts, and replica set elections
- Adjust the verbosity and `slowms` property of logs
- Identify a log's retention period and minimum privilege
- Use MongoDB's standard log rotation approach
- Configure the Linux `logrotate` service to rotate `mongod` logs for a self-managed deployment

---

## Resources

Use the following resources to learn more about MongoDB logging basics:

- **Lesson 1: MongoDB Logs in Atlas**
  - [Project Data Access Read Only](https://www.mongodb.com/docs/atlas/reference/user-roles/#mongodb-authrole-Project-Data-Access-Read-Only)
  - [View and Download MongoDB Logs](https://www.mongodb.com/docs/atlas/monitoring-alerts/view-logs/)
  - [atlas logs download](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-logs-download/)

- **Lesson 2: MongoDB Logs on Self-Managed Instances**
  - [Retrieve Recent Events from Log](https://www.mongodb.com/docs/manual/tutorial/retrieve-log-messages/)

- **Lesson 3: MongoDB Log Events**
  - [Log Messages](https://www.mongodb.com/docs/manual/reference/log-messages/)
  - [View Database Access History](https://www.mongodb.com/docs/atlas/security/audit-log/)
  - [Recommended Values (Operations Checklist)](https://www.mongodb.com/docs/manual/administration/production-checklist-operations/)

- **Lesson 4: MongoDB Server Log Customizations**
  - [db.setLogLevel()](https://www.mongodb.com/docs/manual/reference/method/db.setLogLevel/)
  - [db.setProfilingLevel()](https://www.mongodb.com/docs/manual/reference/method/db.setProfilingLevel/)
  - [slowOpThresholdMs](https://www.mongodb.com/docs/manual/reference/configuration-options/#mongodb-setting-operationProfiling.slowOpThresholdMs)
  - [Logging Slow Operations](https://www.mongodb.com/docs/manual/tutorial/manage-the-database-profiler/#log-all-slow-operations)
  - [Analyzing Slow Queries](https://www.mongodb.com/docs/atlas/schema-advisor/slow-query/)
  - [Log Parsing Examples](https://www.mongodb.com/docs/manual/reference/log-messages/#log-parsing-examples)

- **Lesson 5: MongoDB Server Log Rotation and Retention**
  - [Rotate Log Files](https://www.mongodb.com/docs/manual/tutorial/rotate-log-files/)
  - For more information on the `logrotate` service, try searching the web for resources on logrotate in Linux.