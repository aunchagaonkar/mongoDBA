# MongoDB Associate DBA — Practice Set 5

**30 exam-style questions**, no overlap with Sets 1–4. This set is pitched slightly harder than the previous four in two ways: several questions require combining facts from two different units to reach the answer, and a few present a set of symptoms and ask for the single explanation that accounts for all of them. It also closes the remaining gaps in the uploaded material — the Atlas CLI, cluster tiers and project structure, the feature compatibility version, and user management commands.

**How to use it:** answer all 30 before opening the key. There are no *select all that apply* questions in this set; every question has exactly one best answer, and in several cases more than one option is defensible in isolation — pick the one the scenario actually calls for.

---

## Questions

### Question 1

A team is moving an application from a proof of concept to production. Their requirements include a dedicated cluster with isolated resources, the Performance Advisor for index recommendations, the Real-Time Performance Panel, and Online Archive for infrequently accessed data.

What is the minimum Atlas cluster tier that satisfies all of these?

- **a.** M0
- **b.** Serverless
- **c.** M10
- **d.** M30

---

### Question 2

An engineering department has one Atlas organization. It needs development, testing, and production environments with separate resources, separate database users, and separate network access rules, while keeping billing and organization-level settings unified.

How should the department structure this in Atlas?

- **a.** Create three separate organizations, one per environment.
- **b.** Create three projects within the existing organization, one per environment.
- **c.** Deploy all three environments as clusters in a single project, distinguished by cluster names.
- **d.** Create three databases within one cluster, one per environment.

---

### Question 3

A database administrator is asked to produce a summary of a cluster's performance over the previous month and opens the Real-Time Performance Panel for an M30 cluster.

Why is this the wrong tool for the request?

- **a.** The panel reports current and very recent activity — live operations, hardware statistics, and network traffic — and does not present historical data over extended periods.
- **b.** The panel is available only on M0 and serverless clusters, so it cannot be opened for an M30.
- **c.** The panel reports only network traffic, so hardware and operation data must come from another source.
- **d.** The panel aggregates data weekly, so a monthly view requires four separate exports.

---

### Question 4

During a design review, a developer argues that because MongoDB has a flexible schema, the team does not need to spend time on data modeling — documents can simply be inserted in whatever shape the application produces.

What is the accurate response?

- **a.** The developer is correct; a flexible schema removes the need for schema design, because MongoDB optimises document layout automatically.
- **b.** A flexible schema means the database does not enforce a structure by default; it does not remove the need to model data around the application's access patterns.
- **c.** The developer is correct for collections under 16 MB in total size, beyond which a fixed schema becomes mandatory.
- **d.** A flexible schema applies only to documents within a single collection, so modeling is needed only when a database has more than one collection.

---

### Question 5

A database administrator must drop a compound index on a `shipments` collection but does not know the index's auto-generated name.

Which approach removes the index?

- **a.** Run `db.shipments.getIndexes()` to read the index name, then `db.shipments.dropIndex("<name>")` — or pass the index's key document to `dropIndex()` directly.
- **b.** Run `db.shipments.stats()` to read the index name, then `db.shipments.deleteIndex("<name>")`.
- **c.** Run `db.shipments.dropIndexes()`, which is the only way to remove an index whose name is unknown.
- **d.** Run `db.shipments.hideIndex()` first, since an index must be hidden before it can be dropped.

---

### Question 6

A database administrator drops an index that a frequently run reporting query had been using. The application team reports that the report still produces the correct figures, but now takes far longer to run.

What explains this?

- **a.** The query is returning cached results from before the index was dropped, which will expire shortly.
- **b.** The query now uses a collection scan, examining every document to find matches, which yields the same results with far more work.
- **c.** Dropping the index invalidated the query's plan cache, and performance will recover automatically once a new plan is cached.
- **d.** The query is being rejected and silently retried by the driver until it succeeds.

---

### Question 7

A database administrator suspects a handful of operations on a self-managed deployment are exceeding 50 milliseconds and wants to capture those operations in a form that can be queried and analysed afterwards, rather than only reading them from the log file.

Which approach achieves this?

- **a.** Run `db.setProfilingLevel(1, { slowms: 50 })`, then query the `system.profile` collection.
- **b.** Run `db.setProfilingLevel(0, { slowms: 50 })`, then query the `system.profile` collection.
- **c.** Run `db.setLogLevel(2)`, then query the `system.profile` collection.
- **d.** Run `db.setProfilingLevel(2, { slowms: 50 })`, then run `show log global`.

---

### Question 8

A time series collection named `readings` was created with `timeField: "timestamp"` and `metaField: "sensor"`. Queries filter by a specific sensor over a time range, and they are slower than the team expects.

What should the database administrator do?

- **a.** Create a secondary index on the `metaField` and `timeField`.
- **b.** Convert the collection to a clustered collection, since time series collections cannot be indexed.
- **c.** Increase the `granularity` setting to `hours`, which rebuilds the existing buckets.
- **d.** Create a wildcard index on the collection, since time series field names are dynamic.

---

### Question 9

A database administrator is about to begin a rolling MongoDB upgrade on a replica set. Each member will be offline for roughly three hours. Pre-upgrade checks return the following:

- `rs.printReplicationInfo()` reports an oplog window of 6 hours.
- `rs.printSecondaryReplicationInfo()` shows one secondary is currently 4 hours behind the primary.

What should the administrator do?

- **a.** Proceed with the lagging secondary first, since it is already behind and has the least to lose.
- **b.** Postpone the upgrade until the lagging secondary has caught up, because taking it down for three hours would push it beyond the oplog window and force a full initial sync.
- **c.** Proceed as planned, because the 6-hour oplog window exceeds the 3-hour outage per member.
- **d.** Increase the oplog size on the lagging secondary only, then proceed immediately.

---

### Question 10

A three-member replica set is being reduced to two data-bearing members plus an arbiter. The administrator needs to remove one existing secondary, `mongod3.replset.com:27017`, from the set.

Which command removes it?

- **a.** `rs.stepDown("mongod3.replset.com:27017")`
- **b.** `rs.remove("mongod3.replset.com:27017")`
- **c.** `rs.freeze("mongod3.replset.com:27017")`
- **d.** `db.adminCommand({ dropMember: "mongod3.replset.com:27017" })`

---

### Question 11

A read-only dashboard is deployed in several regions and reads from a globally distributed replica set. The priority is the lowest possible read latency for each regional deployment; slightly stale data is acceptable, and reads from the primary are also acceptable if the primary happens to be closest.

Which read preference fits?

- **a.** `primary`
- **b.** `secondary`
- **c.** `nearest`
- **d.** `secondaryPreferred`

---

### Question 12

Before beginning an upgrade, a database administrator must record both the MongoDB version currently running and the feature compatibility version currently in effect.

Which pair of commands provides this?

- **a.** `db.version()` and `db.adminCommand({ getParameter: 1, featureCompatibilityVersion: 1 })`
- **b.** `db.serverStatus().version` and `rs.conf().featureCompatibilityVersion`
- **c.** `db.version()` and `db.adminCommand({ setFeatureCompatibilityVersion: 1 })`
- **d.** `show dbs` and `db.stats().fcv`

---

### Question 13

A replica set was upgraded to 6.0 several weeks ago, and `setFeatureCompatibilityVersion` was set to `"6.0"` at the time. An application incompatibility has now been discovered, and the team wants to downgrade the binaries to 5.0.

What must happen before the binaries can be downgraded?

- **a.** Nothing additional; binaries can be downgraded at any time and the feature compatibility version adjusts automatically.
- **b.** The feature compatibility version must be set back to `"5.0"` first, because 6.0 may have persisted data using features that 5.0 cannot read.
- **c.** The oplog must be resized to match the 5.0 default before the downgrade.
- **d.** Every member must be removed from the replica set and re-added after the downgrade.

---

### Question 14

A database administrator has developed a new compound index and a revised query for a production reporting workload. The change needs realistic validation — against representative data volumes and a copy of the production configuration — before users are exposed to it.

Which environment is intended for this?

- **a.** The development environment, where the index was written
- **b.** The pre-production environment
- **c.** The production environment, using a hidden index
- **d.** A local `mongod` instance on the administrator's workstation

---

### Question 15

A database administrator reviewing an Atlas cluster observes three things at once, beginning at the same time yesterday:

- Query Targeting has risen to roughly 900 scanned objects per document returned.
- Tickets Available for reads is falling toward zero during business hours.
- The Connections count is climbing steadily.

Which single explanation accounts for all three observations?

- **a.** The oplog has been undersized, so secondaries are lagging and consuming read capacity.
- **b.** A newly deployed query is running without a supporting index: it scans far more data than it returns, holds storage-engine tickets for longer, and causes clients to open additional connections as operations queue.
- **c.** The cluster's disk is nearly full, which throttles all three subsystems proportionally.
- **d.** A network partition is causing the driver to retry reads against multiple members simultaneously.

---

### Question 16

A database administrator needs to download the `mongod` logs for a specific host in an Atlas project from a terminal, as part of a scripted incident-response routine.

Which Atlas CLI command does this?

- **a.** `atlas logs download <hostname> mongodb.gz`
- **b.** `atlas logs export <hostname> --type=mongod`
- **c.** `atlas processes logs get <hostname>`
- **d.** `mongosh --eval "show log global" > mongodb.log`

---

### Question 17

A database administrator wants to retrieve measurement data for a specific Atlas host from the command line, restricted to a defined time period and granularity.

Which Atlas CLI command is appropriate?

- **a.** `atlas metrics processes <hostname:port> --period <period> --granularity <granularity> --type <metric>`
- **b.** `atlas clusters describe <clusterName> --metrics`
- **c.** `atlas alerts list --metrics --period <period>`
- **d.** `atlas monitoring export --host <hostname:port>`

---

### Question 18

A compliance team requests a JSON file containing only the documents from a `claims` collection where `status` is `"denied"`.

Which command produces this?

- **a.** `mongoexport --collection=claims --db=insurance --query='{"status":"denied"}' --out=denied.json "<uri>"`
- **b.** `mongoexport --collection=claims --db=insurance --filter='{"status":"denied"}' --out=denied.json "<uri>"`
- **c.** `mongodump --collection=claims --db=insurance --query='{"status":"denied"}' --out=denied.json "<uri>"`
- **d.** `mongoexport --collection=claims --db=insurance --out=denied.json "<uri>"` followed by manual filtering

---

### Question 19

A database administrator must produce a backup of a `sample_supplies` database as a **single compressed file** so it can be transferred to another host over a slow link.

Which combination of `mongodump` options achieves this?

- **a.** `--out` with `--gzip`
- **b.** `--archive` with `--gzip`
- **c.** `--oplog` with `--out`
- **d.** `--archive` with `--noIndexRestore`

---

### Question 20

A `mongorestore` operation must load a dump into a three-member replica set. The administrator wants the tool to write to the current primary and to continue working correctly if an election occurs during the restore.

What must the connection string include?

- **a.** The hostname of the current primary only, to avoid writes being misrouted
- **b.** A seedlist of the replica set members together with the `replicaSet` parameter
- **c.** The `directConnection=true` parameter with any one member's hostname
- **d.** The `readPreference=primary` parameter with any one member's hostname

---

### Question 21

An administrator restores a dump into a cluster whose collections already contain data from a previous partial restore. The restore fails with duplicate key errors.

Which `mongorestore` option addresses this?

- **a.** `--drop`, which drops each collection before restoring it
- **b.** `--noIndexRestore`, which prevents unique index conflicts
- **c.** `--oplogReplay`, which reconciles the existing documents
- **d.** `--writeConcern=0`, which suppresses the errors

---

### Question 22

A new application requires read and write access to the `sales` database and nothing else. The administrator is connected to the `admin` database and intends to create the user there so that `admin` serves as its authentication database.

Which command creates this user correctly?

- **a.** `db.createUser({ user: "salesApp", pwd: passwordPrompt(), roles: [ { role: "readWrite", db: "sales" } ] })`
- **b.** `db.createUser({ user: "salesApp", pwd: passwordPrompt(), roles: [ "readWriteAnyDatabase" ] })`
- **c.** `db.createUser({ user: "salesApp", pwd: passwordPrompt(), privileges: [ { role: "readWrite", db: "sales" } ] })`
- **d.** `db.addUser("salesApp", passwordPrompt(), { readWrite: "sales" })`

---

### Question 23

An existing user, `analytics_svc`, currently holds the `readWrite` role on the `reporting` database. Policy now requires the account to be read-only, but the account itself must continue to exist with its other roles intact.

Which command should the administrator use?

- **a.** `db.dropUser("analytics_svc")`, then recreate the user with only the roles required
- **b.** `db.revokeRolesFromUser("analytics_svc", [ { role: "readWrite", db: "reporting" } ])`, then grant `read` on `reporting`
- **c.** `db.updateUser("analytics_svc", { readOnly: true })`
- **d.** `db.revokePrivilegesFromRole("readWrite", [ { db: "reporting" } ])`

---

### Question 24

An organisation wants to authenticate database clients with x.509 certificates instead of passwords.

What is a prerequisite for this?

- **a.** The deployment must use TLS, since x.509 authentication relies on the TLS handshake to present certificates.
- **b.** The deployment must have auditing enabled so certificate presentations are recorded.
- **c.** SCRAM must be disabled at the operating system level before x.509 can be configured.
- **d.** Each member of the replica set must share a single certificate to keep identities consistent.

---

### Question 25

A data analyst needs read access to exactly two databases, `sales` and `marketing`, and must not be able to read any other database on the deployment.

Which approach follows the principle of least privilege?

- **a.** Grant the `readAnyDatabase` role, which is the standard role for analyst accounts.
- **b.** Grant the `read` role scoped to `sales` and the `read` role scoped to `marketing`, as two entries in the user's roles array.
- **c.** Grant the `dbOwner` role on `sales` and `marketing`.
- **d.** Grant `clusterMonitor`, which permits reads without permitting writes.

---

### Question 26

An administrator inherits a self-managed Linux deployment installed from the MongoDB packages and needs to locate the `mongod` log file. No custom configuration was documented.

Where should the administrator look first, and how is the location controlled?

- **a.** `/var/log/mongodb/mongod.log`, controlled by `systemLog.path` with `systemLog.destination: file` in the configuration file
- **b.** `/etc/mongod.log`, controlled by `storage.dbPath` in the configuration file
- **c.** `/var/lib/mongodb/mongod.log`, controlled by `systemLog.verbosity` in the configuration file
- **d.** The system journal only; MongoDB packages do not write a log file by default

---

### Question 27

An administrator greps `mongod.log` and finds an entry whose severity field reads `"s": "W"`.

What does this indicate, and what are the more severe levels?

- **a.** A warning; the more severe levels are Error and Fatal.
- **b.** A write operation; the more severe levels are Read and Command.
- **c.** A debug message at level 1; the more severe levels are Informational and Warning.
- **d.** A WiredTiger storage message; severity is expressed only as a numeric verbosity elsewhere.

---

### Question 28

In a sharded cluster, a database administrator needs to know which component holds the authoritative record of which chunks live on which shards.

Which component is it?

- **a.** `mongos`
- **b.** The primary shard
- **c.** The config servers
- **d.** The balancer

---

### Question 29

A team is prototyping an internal tool. Its traffic is intermittent and unpredictable — idle for days, then busy for a few hours — and the team wants to pay only for the resources it actually consumes, without choosing a fixed cluster size.

Which Atlas deployment option fits?

- **a.** M0
- **b.** Serverless
- **c.** M10
- **d.** A dedicated cluster with auto-scaling disabled

---

### Question 30

A database administrator provisions a new workstation to run `mongodump` and `mongorestore` against an Atlas cluster, and finds the commands are not available even though `mongosh` connects successfully.

What explains this?

- **a.** The MongoDB Database Tools are bundled with the Community and Enterprise server packages, but must be downloaded and installed separately when working with Atlas.
- **b.** `mongodump` and `mongorestore` cannot be used with Atlas clusters and have no installable package.
- **c.** The tools are installed with `mongosh` but require a separate Atlas licence key to become available.
- **d.** The tools must be run from inside the Atlas UI's embedded terminal and are never installed locally.

---

## Answer Key

### 1 — **c**

Performance Advisor, the Real-Time Performance Panel, and Online Archive all require **M10 or above**, and M10 is the entry point for dedicated clusters, where resources are not shared with other tenants. M10 is therefore the *minimum* that satisfies every requirement.

- **a** M0 is the free shared tier: no dedicated resources and none of the three features.
- **b** serverless bills for consumption and does not provide dedicated cluster resources or these features.
- **d** M30 satisfies the requirements but is not the minimum — read the question.

*Unit: Getting Started with Atlas — Deploying an Atlas Cluster; Exploring the Atlas UI*

### 2 — **b**

Projects are the unit of resource isolation inside an organization: each has its own clusters, database users, and network access rules, while the organization above them retains billing and organization-wide settings. Separate projects per environment is the documented pattern.

- **a** separate organizations fragment billing and organization settings, which the requirement rules out.
- **c** one project for everything is explicitly discouraged — it weakens access control and resource organisation.
- **d** databases within a cluster share the cluster's users and network rules, so nothing is isolated.

*Unit: Getting Started with Atlas — Deploying an Atlas Cluster*

### 3 — **a**

The Real-Time Performance Panel is a live view: current operations, hardware statistics such as CPU, disk IOPS and memory, and network traffic including current connections and bytes in and out per second. It is not a historical reporting tool, so a month-long summary must come from the cluster's metrics charts instead.

- **b** inverts the tier requirement — the panel needs M10 or above, so an M30 qualifies.
- **c** understates its scope; hardware and operation data are included.
- **d** invents a weekly aggregation.

*Unit: Getting Started with Atlas — Exploring the Atlas UI*

### 4 — **b**

A flexible schema means MongoDB does not *enforce* a document structure by default, so documents in one collection may differ. It does not mean structure is unimportant. MongoDB's central modeling principle — data that is accessed together should be stored together — is a design decision the team still has to make, driven by the application's query and update patterns. Flexibility changes when you can revise the model, not whether you need one.

- **a** is the misconception being tested; MongoDB does not reorganise documents for you.
- **c** invents a size threshold.
- **d** invents a scope limitation.

*Units: Document Model — Overview of the Document Model; Data Modeling — Introduction to Data Modeling*

### 5 — **a**

`getIndexes()` returns every index on the collection with its name and key pattern. `dropIndex()` then accepts either the name or the key document, so once you have the output you can use whichever is convenient.

- **b** `deleteIndex()` does not exist, and `stats()` does not list index names.
- **c** `dropIndexes()` would remove every index rather than the one intended.
- **d** hiding is a way to *test* removal, not a prerequisite for it.

*Unit: Indexes — Deleting MongoDB Indexes*

### 6 — **b**

An index is a performance structure, not a correctness one. Without it, the query planner falls back to a `COLLSCAN` and examines every document in the collection, producing identical results with far more work. This is why dropping an index is a performance risk rather than an availability risk — and why `hideIndex()` exists to test the impact reversibly.

- **a** MongoDB does not cache query *results*.
- **c** the plan cache is invalidated, but a new plan for a collection scan is still a collection scan; performance does not recover on its own.
- **d** no retry occurs; nothing failed.

*Unit: Indexes — Deleting MongoDB Indexes; Working with Compound Indexes*

### 7 — **a**

Profiling level **1** writes operations slower than `slowms` into the `system.profile` collection, which can then be queried like any other collection — sorted by duration, filtered by namespace, and so on. `system.profile` is a capped collection, so older entries are eventually overwritten.

- **b** level 0 disables the profiler; `slowms` would still govern the diagnostic log, but nothing is written to `system.profile`.
- **c** `setLogLevel()` changes log verbosity and does not populate `system.profile`.
- **d** level 2 profiles every operation regardless of `slowms`, and `show log global` reads log messages rather than profiler documents.

*Units: Indexes II — How to Monitor Indexes; Logging Basics — Server Log Customizations*

### 8 — **a**

Time series collections support secondary indexes, and the recommended index for this access pattern covers the `metaField` and the `timeField` — which is exactly how the query filters. That lets MongoDB narrow to the relevant buckets rather than scanning more of the collection.

- **b** is false on its premise; time series collections can be indexed.
- **c** changing granularity does not rebuild existing buckets, and a coarser granularity is not a fix for a missing index.
- **d** wildcard indexes address unknown *field names*; the field names here are known.

*Unit: Indexes II — Time Series Collections*

### 9 — **b**

This is the integration the pre-upgrade checklist exists for. The oplog window is 6 hours, but the lagging secondary has already consumed 4 hours of it. Taking that member down for a 3-hour upgrade puts its total distance behind the primary at roughly 7 hours — past the window — so the entries it needs to resume replication will have been overwritten and it will require a full initial sync. MongoDB's guidance is to confirm secondaries are not still catching up before starting maintenance.

- **a** inverts the risk; the lagging member is the most exposed, not the least.
- **c** compares the window against the outage alone and ignores the 4 hours already spent — the trap in the question.
- **d** the oplog window is a property of each member's own oplog, and resizing one member's oplog does not recover entries the *source* has already overwritten; it also does nothing about the existing 4-hour deficit.

*Units: Upgrades & Maintenance — Zero Downtime Maintenance, MongoDB Server Upgrades; Replication — The MongoDB Operation Log*

### 10 — **b**

`rs.remove("<host>:<port>")` removes a member from the replica set configuration. The equivalent is retrieving the configuration with `rs.conf()`, deleting the member from `config.members`, and applying it with `rs.reconfig()`.

- **a** `rs.stepDown()` demotes a primary and takes no member argument of this kind.
- **c** `rs.freeze()` prevents a member from seeking election for a period; it does not remove it.
- **d** `dropMember` is not a MongoDB command.

*Unit: Replication — Configuring a Replica Set in a MongoDB Deployment*

### 11 — **c**

`nearest` routes each read to the member with the lowest network latency from the client, whether that member is the primary or a secondary. For geographically distributed readers whose only priority is latency, that is the fitting choice.

- **a** `primary` sends every read to one region, which is the opposite of the goal.
- **b** `secondary` excludes the primary even when the primary is closest — the requirement explicitly permits it.
- **d** `secondaryPreferred` prefers secondaries and only falls back to the primary when none is available; it does not select on latency.

*Unit: Replication — Read and Write Concerns*

### 12 — **a**

`db.version()` returns the running server version. The feature compatibility version is read with `db.adminCommand({ getParameter: 1, featureCompatibilityVersion: 1 })`. Both belong on the pre-upgrade checklist, along with confirming every member reports the same FCV.

- **b** `rs.conf()` holds replica set configuration, not the FCV.
- **c** `setFeatureCompatibilityVersion` **changes** the value rather than reading it — and running it during a pre-upgrade audit would be a mistake.
- **d** neither command exists in that form.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 13 — **b**

The feature compatibility version gates features that **persist data** in formats earlier releases cannot read. Once FCV is `"6.0"`, 6.0-only on-disk formats may already be present, so the FCV must be set back to `"5.0"` — allowing MongoDB to remove or rewrite anything incompatible — before the binaries are downgraded. This is the reason the guidance is to leave FCV at the old value until you are confident you will not need to downgrade.

- **a** is the dangerous assumption; nothing adjusts automatically.
- **c** and **d** invent unrelated prerequisites.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 14 — **b**

The **pre-production** environment exists for rigorous testing against representative data and configuration before a change reaches users. Testing there is what keeps a change from becoming an incident.

- **a** the development environment is for building features, and its testing is less rigorous by design.
- **c** hiding an index in production is a legitimate technique for measuring the impact of *removing* one, but it is not a substitute for validating a new index and query outside production.
- **d** a workstation instance cannot reproduce production data volumes or configuration.

*Unit: Upgrades & Maintenance — Zero Downtime Maintenance with a MongoDB Deployment*

### 15 — **b**

One unindexed query explains all three symptoms, which is why it is the answer rather than any single-metric cause. Query Targeting rises because the query scans far more objects than it returns. Tickets Available falls because each of those long-running operations holds a storage-engine ticket for the duration of its scan. Connections climb because operations queue behind the ticket shortage, so clients open more connections rather than reusing satisfied ones. The next step is Performance Advisor or the profiler to identify the query, then `explain()` and an index.

- **a** replication lag does not raise Query Targeting, which measures scanned-to-returned ratio on the queries themselves.
- **c** low disk space does not produce a scanned-to-returned ratio of 900.
- **d** retries would show in connection counts but would not change Query Targeting.

*Unit: Database Metrics & Monitoring — Core Metrics, More Metrics*

### 16 — **a**

`atlas logs download <hostname> mongodb.gz` retrieves the compressed `mongod` log for a specific host. Available log files include `mongodb.gz` and `mongos.gz`, along with their audit equivalents. The user needs at least the Project Data Access Read Only role.

- **b** and **c** are not valid Atlas CLI commands.
- **d** `show log global` returns only the recent messages held in the server's RAM cache, not the log file, and Atlas does not permit `setLogLevel`-style log administration.

*Unit: Logging Basics — MongoDB Logs in Atlas*

### 17 — **a**

`atlas metrics processes <hostname:port>` returns measurements for a specific Atlas process, narrowed with `--period`, `--granularity`, and `--type` to select the window, resolution, and metric.

- **b** `atlas clusters describe` returns cluster configuration.
- **c** `atlas alerts list` returns alerts, not measurements.
- **d** is not a valid command.

*Unit: Database Metrics & Monitoring — Command Line Metrics*

### 18 — **a**

`mongoexport` filters with `--query`, which takes a JSON query document — the same syntax as a `find()` filter. Output goes to `--out`, in JSON by default.

- **b** `--filter` is not a `mongoexport` option.
- **c** `mongodump` produces BSON, not a JSON file, and its filtering option is `--query` but the output is a dump directory rather than a readable JSON file.
- **d** works but ignores the tool's built-in capability, which is what the question tests.

*Unit: DBA Tools — Data Export Tools*

### 19 — **b**

`--archive=<file>` writes the dump to a **single file** rather than a directory of per-collection files, and `--gzip` compresses it. Together they produce one compressed artefact, which is what a slow transfer calls for.

- **a** `--out` produces a directory tree; `--gzip` would compress the individual files inside it.
- **c** `--oplog` captures oplog entries for point-in-time consistency, which is unrelated to packaging.
- **d** `--noIndexRestore` is a `mongorestore` option, not a `mongodump` one.

*Units: DBA Tools — Backup and Restore Tools; Self-Managed Backup & Recovery*

### 20 — **b**

A connection string containing a **seedlist** of members plus the `replicaSet` parameter lets the driver discover the topology and identify the current primary — and rediscover it if an election occurs mid-restore. Writes always go to the primary, so the tool needs to be able to find whichever member holds that role.

- **a** hard-coding one hostname breaks the moment that member is no longer primary.
- **c** `directConnection=true` disables topology discovery, which is the opposite of the requirement.
- **d** `readPreference` governs reads and does not help a write-only operation find the primary.

*Unit: Self-Managed Backup & Recovery — Restoring a Deployment*

### 21 — **a**

`--drop` drops each collection in the target before restoring it, so the restore starts from a clean state and the leftover documents that caused the duplicate key errors are removed. Use it deliberately — it discards whatever is currently in those collections.

- **b** `--noIndexRestore` skips index builds and does nothing about existing documents.
- **c** `--oplogReplay` applies captured oplog entries and does not reconcile pre-existing data.
- **d** lowering write concern does not suppress duplicate key errors, and `w: 0` would hide genuine failures rather than fix them.

*Units: Self-Managed Backup & Recovery — Restoring a Deployment; DBA Tools — Restore Tools*

### 22 — **a**

`db.createUser()` takes `user`, `pwd`, and a `roles` **array** whose entries name a role and the database it applies to. Because the command is run while connected to `admin`, that becomes the user's authentication database, while the granted privilege is scoped to `sales` only. `passwordPrompt()` avoids putting the password in shell history.

- **b** `readWriteAnyDatabase` grants write access to every database — far beyond the requirement.
- **c** uses a `privileges` key, which belongs to role definitions, not user creation.
- **d** `db.addUser()` is a removed legacy method.

*Unit: Self-Managed Security — Establishing Authorization*

### 23 — **b**

`db.revokeRolesFromUser()` removes specific roles from an existing user while leaving the account and its remaining roles untouched. Granting `read` on `reporting` afterwards produces the required read-only access.

- **a** achieves the outcome but destroys and recreates the account, risking the loss of roles that were meant to remain and briefly breaking anything authenticating as that user.
- **c** `updateUser()` exists, but there is no `readOnly` option — roles are the mechanism.
- **d** `revokePrivilegesFromRole` modifies a role definition, which would affect every user holding that role.

*Unit: Self-Managed Security — Establishing Authorization*

### 24 — **a**

x.509 authentication identifies a client by the certificate it presents during the **TLS handshake**, so TLS must be enabled and configured on the deployment. Without TLS there is no handshake in which to present a certificate.

- **b** auditing is unrelated to the mechanism working.
- **c** SCRAM does not need to be disabled, and there is no operating-system-level switch for it.
- **d** is the opposite of correct practice — each member and client should have its own certificate.

*Unit: Self-Managed Security — Enabling Authentication; Enabling Network Encryption*

### 25 — **b**

A user's `roles` array can hold multiple entries, each scoping a role to a specific database. Two `read` entries — one for `sales`, one for `marketing` — grant precisely the access required and nothing more.

- **a** `readAnyDatabase` grants read access to every database on the deployment, violating the requirement directly.
- **c** `dbOwner` includes write and administrative privileges.
- **d** `clusterMonitor` grants monitoring and diagnostic access, not collection reads — a common confusion, and it would not give the analyst the data.

*Unit: Self-Managed Security — Establishing Authorization*

### 26 — **a**

Package installs on Linux write to `/var/log/mongodb/mongod.log` by default. The location and behaviour are set in the configuration file under `systemLog`: `destination: file` with a `path`, or `destination: syslog` to hand messages to the system logger instead. If `destination` is omitted entirely, messages go to standard output.

- **b** `/etc` holds configuration, and `storage.dbPath` sets the data directory.
- **c** `/var/lib/mongodb` is the default data directory, and `verbosity` controls detail rather than location.
- **d** is false; the packages configure a log file.

*Unit: Logging Basics — MongoDB Logs on Self-Managed Instances*

### 27 — **a**

`"W"` is **Warning**. The severity scale from most to least severe is Fatal (`F`), Error (`E`), Warning (`W`), Informational (`I`), and Debug (`D1` through `D5`). Filtering on severity is the fastest way to triage a large log file.

- **b** invents a meaning; the severity field says nothing about operation type.
- **c** inverts the ordering — Informational is *less* severe than Warning.
- **d** is false; severity applies to every message regardless of component.

*Unit: Logging Basics — MongoDB Log Events*

### 28 — **c**

The **config servers** store the sharded cluster's metadata and configuration, including the mapping of chunk ranges to shards. `mongos` consults that metadata to route queries.

- **a** `mongos` is the router; it caches metadata but is not the source of truth.
- **b** the primary shard holds unsharded collections for a database, not the chunk map.
- **d** the balancer migrates chunks between shards and updates the metadata, but the config servers hold it.

*Units: Getting Started with Atlas — MongoDB Architecture Overview; sharding fundamentals (see docs on Sharded Cluster Components)*

### 29 — **b**

The **serverless** deployment option follows a pay-for-what-you-use model, which suits intermittent and unpredictable traffic where a fixed cluster size would be either wasteful or too small.

- **a** M0 is free and useful for experimentation, but it is a fixed shared tier with hard limits rather than a consumption model.
- **c** M10 is a fixed dedicated tier billed continuously, idle or not.
- **d** compounds the mismatch by removing the one mechanism that would help a fixed cluster adapt.

*Unit: Getting Started with Atlas — Deploying an Atlas Cluster*

### 30 — **a**

The MongoDB Database Tools — `mongodump`, `mongorestore`, `mongoexport`, `mongoimport`, `mongostat`, `mongotop`, `bsondump`, and `mongofiles` — ship with the Community and Enterprise **server** packages. A workstation that only has `mongosh` does not have them, so when the target is an Atlas cluster they must be downloaded and installed separately.

- **b** is false; the tools work against Atlas clusters.
- **c** invents a licence key.
- **d** invents an embedded terminal.

*Unit: DBA Tools — MongoDB DBA Tools Overview*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| Atlas platform: tiers, projects, UI | 1, 2, 3, 29 | 4 |
| Data modeling concepts | 4 | 1 |
| Indexing & profiling | 5, 6, 7, 8 | 4 |
| Replication & read preference | 9, 10, 11 | 3 |
| Upgrades & maintenance | 12, 13, 14 | 3 |
| Metrics, monitoring & Atlas CLI | 15, 16, 17 | 3 |
| Backup & recovery | 19, 20, 21 | 3 |
| DBA tools | 18, 30 | 2 |
| Security: users, roles, x.509 | 22, 23, 24, 25 | 4 |
| Logging | 26, 27 | 2 |
| Sharded cluster components | 28 | 1 |

**Scoring guide:** 27+ = strong; 22–26 = solid; below 22 = revisit those units. Note that Q9 and Q15 are the two hardest items — if you missed only those, your recall is fine and the gap is in combining facts under time pressure, which the mock format addresses better than more themed sets.

## Traps Worth Remembering From This Set

- **"Minimum" questions.** Q1 has two technically correct answers and only one minimum. The exam does this deliberately.
- **Oplog arithmetic is cumulative.** Existing lag plus planned downtime is what you compare against the oplog window — not downtime alone (Q9).
- **FCV is a one-way door until you reverse it.** Downgrading binaries requires lowering FCV first, which is the reason to delay raising it after an upgrade (Q13).
- **Reading versus writing configuration.** `getParameter` reads the FCV; `setFeatureCompatibilityVersion` changes it. An audit step that mutates state is always the wrong answer (Q12).
- **`clusterMonitor` keeps reappearing as a wrong answer for data access.** It grants diagnostics, never collection reads (Q25). Set 1 tested the same role from the correct side.
- **Symptom clusters have one cause.** When three metrics move together, look for the single workload change that explains all three rather than a separate cause for each (Q15).

## Cumulative Coverage — All Five Sets

You now have 150 questions.

| Topic area | Set 1 | Set 2 | Set 3 | Set 4 | Set 5 | Total |
|---|---|---|---|---|---|---|
| Indexing, explain & profiling | 9 | 7 | 4 | 4 | 4 | 28 |
| Security | 5 | 5 | — | 4 | 4 | 18 |
| Replication | 4 | 3 | 3 | 4 | 3 | 17 |
| CRUD & query results | 3 | 4 | 4 | 3 | — | 14 |
| Metrics & monitoring | 1 | 2 | 4 | 2 | 3 | 12 |
| Data modeling & document model | 2 | 1 | 4 | 3 | 1 | 11 |
| Backup & recovery | 2 | 2 | 2 | 2 | 3 | 11 |
| DBA tools | 1 | 1 | 2 | 3 | 2 | 9 |
| Logging | 1 | 2 | 2 | 2 | 2 | 9 |
| Upgrades & maintenance | 1 | 2 | 1 | — | 3 | 7 |
| Shell & connecting | — | — | 4 | 2 | — | 6 |
| Atlas platform | — | — | — | — | 4 | 4 |
| Sharded clusters | 1 | 1 | — | 1 | 1 | 4 |

Every unit in the uploaded material has now been tested, most of them several times over. The distribution above is a reasonable match for the exam's own weighting, with indexing and security carrying the most items.
