# MongoDB Associate DBA — Practice Set 3

**30 exam-style questions**, no overlap with Sets 1 or 2. This set deliberately shifts weight toward the units the first two sets sampled lightly: the MongoDB Shell, connection troubleshooting, Atlas monitoring and alerting, the document model, and the Database Tools. Two questions are *select all that apply* (marked).

**How to use it:** answer all 30 before opening the key. Across all three sets you now have 90 questions; the closing section suggests how to use them together.

---

## Questions

### Question 1

A `sessions` collection stores one document per user session, each with a `durationMinutes` field. An administrator needs the five longest sessions, ordered from longest to shortest, and needs the same five documents returned in the same order every time the query runs.

Which query should the administrator use?

- **a.** `db.sessions.find().sort({ durationMinutes: -1, _id: 1 }).limit(5)`
- **b.** `db.sessions.find({}, { sort: { durationMinutes: -1 }, limit: 5 })`
- **c.** `db.sessions.find().limit(5).sort({ durationMinutes: -1 })`
- **d.** `db.sessions.find().sort({ durationMinutes: 1 }).limit(5)`

---

### Question 2

A `trips` collection contains records of bike-share trips. An administrator needs the **number** of trips whose `durationSeconds` value exceeds 1800.

Which operation returns that number?

- **a.** `db.trips.find({ durationSeconds: { $gt: 1800 } })`
- **b.** `db.trips.countDocuments({ durationSeconds: { $gt: 1800 } })`
- **c.** `db.trips.countDocuments({ durationSeconds: 1800 })`
- **d.** `db.trips.find({ durationSeconds: { $gt: 1800 } }).limit(1)`

---

### Question 3

A `routes` collection stores flight routes with the fields `src_airport`, `dst_airport`, `stops`, and `airline.name`. An administrator needs every route that **either departs from or arrives at** `"BOM"`, **and** that is **either nonstop or operated by** `"Air India"`.

Which query expresses this correctly?

- **a.** `db.routes.find({ $or: [ { src_airport: "BOM" }, { dst_airport: "BOM" }, { stops: 0 }, { "airline.name": "Air India" } ] })`
- **b.** `db.routes.find({ $and: [ { $or: [ { src_airport: "BOM" }, { dst_airport: "BOM" } ] }, { $or: [ { stops: 0 }, { "airline.name": "Air India" } ] } ] })`
- **c.** `db.routes.find({ $or: [ { src_airport: "BOM", dst_airport: "BOM" }, { stops: 0, "airline.name": "Air India" } ] })`
- **d.** `db.routes.find({ src_airport: "BOM", dst_airport: "BOM", stops: 0, "airline.name": "Air India" })`

---

### Question 4

A `vendors` collection contains documents shaped like this:

```js
{
  _id: 1,
  name: "Godavari Supplies",
  contact: { email: "sales@godavari.example", phone: "555-0117", fax: "555-0118" }
}
```

An administrator must change only the `phone` value, leaving `email` and `fax` untouched.

Which update document should be used?

- **a.** `{ $set: { contact: { phone: "555-0200" } } }`
- **b.** `{ $set: { "contact.phone": "555-0200" } }`
- **c.** `{ $push: { "contact.phone": "555-0200" } }`
- **d.** `{ $set: { contact.phone: "555-0200" } }`

---

### Question 5

A developer inserts the following document into a `readings` collection, which already contains several thousand documents:

```js
db.readings.insertOne({ sensor: "S-14", celsius: 31.8 })
```

What happens to the `_id` field for this document?

- **a.** The insert fails, because every document must supply an `_id` value explicitly.
- **b.** The document is stored without an `_id` field, since none was provided.
- **c.** MongoDB adds an `_id` field and populates it with a generated ObjectId value.
- **d.** MongoDB adds an `_id` field and populates it with the next integer in sequence for the collection.

---

### Question 6

A university application tracks students, the courses they enrol in, each course's assigned lecture hall, and each student's transcript.

Which of these represents a **many-to-many** relationship?

- **a.** A student and that student's transcript
- **b.** A course and the lecture hall assigned to it
- **c.** Students and the courses they enrol in
- **d.** A course and the assignments submitted for it

---

### Question 7

A team currently embeds a growing array of review subdocuments inside each product document. To control document growth, they move the reviews into a separate `reviews` collection that references the product by `_id`.

Which tradeoff are they accepting?

- **a.** Product documents will duplicate review data across collections, increasing total storage.
- **b.** Displaying a product together with its reviews now requires an additional query, so that read path becomes slower.
- **c.** Reviews can no longer be indexed, because indexes cannot span two collections.
- **d.** The `reviews` collection cannot enforce a relationship, because MongoDB does not permit storing one document's `_id` in another document.

---

### Question 8

A team running an M0 free-tier cluster reports that several queries have become slow. They want MongoDB to analyse their slow queries and recommend indexes to create.

What should the database administrator tell them?

- **a.** Performance Advisor analyses slow queries and suggests indexes, but it requires an M10 or higher cluster tier.
- **b.** Performance Advisor is available on all cluster tiers, but only through the Atlas CLI on shared tiers.
- **c.** Performance Advisor requires MongoDB Enterprise and is not part of Atlas.
- **d.** Performance Advisor is unavailable in Atlas; index recommendations come from `$indexStats`.

---

### Question 9

An administrator writes a maintenance script to be run with `load()` inside `mongosh`. The script begins with `use sample_analytics` and then queries a collection. When the script is loaded, it throws an error at the first line.

How should the script select the database?

- **a.** Replace `use sample_analytics` with `db.getSiblingDB("sample_analytics")`.
- **b.** Replace `use sample_analytics` with `db.getMongo("sample_analytics")`.
- **c.** Replace `use sample_analytics` with `db.getDb("sample_analytics")`.
- **d.** Leave the script as written and pass the database name to `load()` as a second argument.

---

### Question 10

An administrator needs to start `mongosh`, connect to an Atlas cluster, and have the `sample_analytics` database selected on connection.

Which command works?

- **a.** `mongosh "mongodb+srv://cluster0.abcde.mongodb.net/" --username dbaUser --db sample_analytics`
- **b.** `mongosh "mongodb+srv://cluster0.abcde.mongodb.net/?database=sample_analytics" --username dbaUser`
- **c.** `mongosh "mongodb+srv://cluster0.abcde.mongodb.net/sample_analytics" --username dbaUser`
- **d.** `mongosh --host cluster0.abcde.mongodb.net --db sample_analytics --username dbaUser`

---

### Question 11

A developer working from a new office location attempts to connect an application to an Atlas cluster and receives:

```text
MongoServerSelectionError: connection <monitor> to 34.239.188.169:27017 closed
```

The same connection string works from the developer's home network.

What should the developer do?

- **a.** Update the database user's password in the Atlas Database Access panel.
- **b.** Add the new location's IP address to the allowlist in the Atlas Network Access panel.
- **c.** Create a new database on the cluster so the connection string resolves.
- **d.** Change the connection string from `mongodb+srv://` to `mongodb://`.

---

### Question 12

An application that has been running for months begins failing at startup with:

```text
MongoServerError: bad auth : Authentication failed.
```

Which checks address this specific error?

- **a.** Confirm the current IP address is on the Atlas allowlist, and confirm the cluster is not paused.
- **b.** Confirm the username and password are spelled correctly in the connection string, and confirm the connection targets the correct database deployment.
- **c.** Confirm the cluster tier supports the number of concurrent connections the application opens.
- **d.** Confirm the driver version is compatible with the server version.

---

### Question 13 *(Select all that apply)*

MongoDB stores indexed field values in a B-tree structure.

Which statements about B-trees are accurate?

- **a.** A B-tree is a self-balancing tree data structure.
- **b.** B-trees store their data sorted in ascending order from left to right.
- **c.** A node in a B-tree can have more than two child nodes.
- **d.** "B-tree" is an abbreviation of "binary tree".

---

### Question 14

An administrator reviews the following `explain("executionStats")` output for a query on a `logins` collection. The winning plan uses an index and contains no `SORT` stage.

```js
{
  executionStats: {
    nReturned: 120,
    executionTimeMillis: 640,
    totalKeysExamined: 48000,
    totalDocsExamined: 48000
  }
}
```

What does this pattern indicate?

- **a.** The query is well optimised, because the number of keys examined equals the number of documents examined.
- **b.** An index is being used, but its bounds are not selective for this query: 48,000 index entries were scanned to return 120 documents.
- **c.** The index is multikey, which always causes more keys to be examined than documents returned.
- **d.** The query is covered, because `totalKeysExamined` and `totalDocsExamined` are equal.

---

### Question 15

An `ingest` collection has documents with unpredictable field names, plus two very large fields, `payload` and `rawText`, that are never used as query filters. The administrator wants a wildcard index over every field **except** those two.

Which command creates it?

- **a.** `db.ingest.createIndex({ "$**": 1 }, { wildcardProjection: { payload: 0, rawText: 0 } })`
- **b.** `db.ingest.createIndex({ "$**": 1, payload: 0, rawText: 0 })`
- **c.** `db.ingest.createIndex({ "payload.$**": 0, "rawText.$**": 0 })`
- **d.** `db.ingest.createIndex({ "$**": 1 }, { partialFilterExpression: { payload: 0, rawText: 0 } })`

---

### Question 16

A clustered collection named `weather` has its internal clustered index on `_id` and a secondary index on `metadata.sensorId`. An administrator runs `db.weather.find({ "metadata.sensorId": 5578 })` and wants to know which index will be used.

What should the administrator expect?

- **a.** The clustered index, because a clustered collection's internal index always takes precedence.
- **b.** The secondary index, because the query planner selects the eligible secondary index unless it is overridden with `hint()`.
- **c.** Neither index; queries on clustered collections always perform a collection scan.
- **d.** Both indexes, because MongoDB intersects the clustered index with each eligible secondary index.

---

### Question 17

A security team asks the database administrator to produce the `mongod` logs from an Atlas M30 cluster covering an incident that occurred 45 days ago.

What should the administrator report?

- **a.** The logs are available; Atlas retains log messages indefinitely for M10 and above clusters.
- **b.** The logs are no longer available through Atlas; Atlas retains the most recent 30 days of log messages.
- **c.** The logs are available only through the Atlas CLI, because the UI exposes just the last 24 hours.
- **d.** The logs are available after enabling the audit log retroactively for that period.

---

### Question 18

An administrator is troubleshooting index build behaviour on a self-managed deployment and needs more detailed debug messages from the `INDEX` component specifically, without flooding the log with debug output from every other component.

What should the administrator configure?

- **a.** Set `systemLog.verbosity: 5` in `mongod.conf`.
- **b.** Set a verbosity level for the index component under `systemLog.component` in `mongod.conf`.
- **c.** Run `db.setProfilingLevel(2)` so all index operations are captured.
- **d.** Lower `slowms` to `0` so every index operation is logged as slow.

---

### Question 19

An administrator needs to add a fourth member to a running three-member replica set. The administrator has already run `config = rs.conf()` and appended the new member to `config.members`.

What must be run next for the change to take effect?

- **a.** `rs.initiate(config)`
- **b.** `rs.reconfig(config)`
- **c.** `rs.status(config)`
- **d.** `rs.stepDown()`

---

### Question 20

A payments application must never read a value that could later be rolled back after a failover. Reads must return only data that has been acknowledged by a majority of the replica set members.

What should be configured?

- **a.** Set the read preference to `secondary`.
- **b.** Set the write concern to `majority`.
- **c.** Set the read concern to `majority`.
- **d.** Set `maxStalenessSeconds` to `90`.

---

### Question 21

A monitoring script connects to one node of a replica set and must determine whether that specific node is currently able to accept write operations, so the script can route accordingly.

Which method returns this information?

- **a.** `rs.conf()`
- **b.** `db.hello()`
- **c.** `rs.printReplicationInfo()`
- **d.** `db.serverStatus().mem`

---

### Question 22

An Atlas alert fires reporting that Query Targeting for a cluster has reached a ratio of roughly 1500 scanned objects per document returned.

What does this indicate, and what is the target value?

- **a.** The workload is scanning far more data than it returns, indicating missing or unselective indexes; the ideal ratio is about 1:1.
- **b.** 1500 concurrent operations are queued; the ideal value is under 100.
- **c.** Documents average 1500 bytes; the ideal value depends on the cluster tier.
- **d.** The oplog is 1500 seconds behind; the ideal value is 0.

---

### Question 23

A team configures a Query Targeting alert on an M5 shared-tier cluster and sets a threshold they know is being exceeded. The alert never triggers, and the alert configuration shows as enabled.

What explains this?

- **a.** Alert configurations must be created through the Atlas CLI to take effect on shared-tier clusters.
- **b.** Shared-tier clusters trigger alerts only for the metrics they support — Network, Connections, Logical Size, and Opcounters.
- **c.** Query Targeting alerts cannot be configured on any cluster tier.
- **d.** The alert requires a notification interval of at least 60 minutes before it will evaluate.

---

### Question 24

An administrator receives a Disk Queue Depth alert from Atlas, investigates, and acknowledges the alert with a comment while the remediation work is planned.

What is the effect on the alert's status?

- **a.** The status changes to `CLOSED`, because acknowledging an alert resolves it.
- **b.** The status remains open; an alert changes to `CLOSED` only once the condition that triggered it is resolved.
- **c.** The status changes to `CLOSED` after the notification interval elapses.
- **d.** The status remains open until a Project Owner manually sets it to `CLOSED`.

---

### Question 25

A company monitors its Atlas clusters with Prometheus through the Atlas integration. It now wants the same Prometheus server to collect metrics from a self-managed MongoDB deployment in its own data centre.

What should be configured as the Prometheus target?

- **a.** Grafana, configured to poll the self-managed deployment and expose the metrics
- **b.** The Percona MongoDB Exporter, running against the deployment and exposing a metrics endpoint
- **c.** A custom webhook configured in the Atlas project settings
- **d.** The Atlas CLI, run on a schedule with `atlas metrics processes`

---

### Question 26

A business requires a Recovery Point Objective of 15 minutes and a Recovery Time Objective of 1 hour for its order database. The current strategy is a single `mongodump` run nightly at 02:00. A failure occurs at 16:00.

Which objective does the current strategy fail to meet, and why?

- **a.** The RPO, because up to 14 hours of writes since the last backup would be lost.
- **b.** The RTO, because `mongodump` archives always take longer than one hour to restore.
- **c.** Both, because logical backups cannot be used to satisfy any recovery objective.
- **d.** Neither, because `mongodump` captures the oplog by default and therefore supports point-in-time recovery.

---

### Question 27

An administrator must create a dedicated user for a nightly `mongodump` job on a self-managed replica set. Security policy requires the minimum privileges necessary for the task.

Which built-in role should be granted?

- **a.** `root`
- **b.** `backup`
- **c.** `dbOwner`
- **d.** `readWriteAnyDatabase`

---

### Question 28

An administrator is performing a rolling MongoDB server upgrade on a three-member replica set and has finished upgrading both secondaries, verifying each one is healthy.

What is the next step?

- **a.** Set the feature compatibility version to the new value, then upgrade the primary.
- **b.** Remove the primary from the replica set, upgrade it, and add it back.
- **c.** Step down the primary to trigger an election, then upgrade the former primary.
- **d.** Upgrade the primary in place without stepping it down, since a majority is already upgraded.

---

### Question 29

A data science team asks for an export of the `customers` collection in YAML format so it can be loaded into their pipeline.

What should the database administrator tell them?

- **a.** `mongoexport` supports YAML output using the `--type=yaml` option.
- **b.** `mongoexport` produces JSON or CSV output only; YAML is not a supported format.
- **c.** `mongoexport` produces YAML only when combined with `--jsonFormat canonical`.
- **d.** `bsondump` should be used instead, because it converts BSON directly to YAML.

---

### Question 30

A production deployment is a sharded cluster holding several terabytes of data. A junior administrator proposes backing it up with a nightly `mongodump` and restoring with `mongorestore` if needed.

Why is this proposal inappropriate?

- **a.** `mongodump` and `mongorestore` are intended for standalone deployments and replica sets, and are not recommended for sharded clusters.
- **b.** `mongodump` cannot connect through `mongos`, so it can only ever back up the config servers.
- **c.** `mongodump` requires the `--oplog` option on sharded clusters, and that option is unavailable in the Database Tools package.
- **d.** `mongorestore` can restore only to a cluster with the identical number of shards, which cannot be guaranteed.

---

## Answer Key

### 1 — **a**

`sort()` and `limit()` are **cursor methods** and must be appended to `find()`. Sorting descending on `durationMinutes` returns the longest first, and adding `_id: 1` as a tiebreaker guarantees a stable order when several sessions share the same duration — without a unique field in the sort, MongoDB makes no ordering promise among ties.

- **b** passes sort and limit inside a second document, where `find()` expects a projection. This is the most common syntax distractor on the exam.
- **c** is not an error, but it is the wrong intent: MongoDB applies `sort` before `limit` regardless of chaining order, so this returns the same five documents as (d) — the five *shortest* ordered ascending would come from (d); (c) actually returns the five longest but without a tiebreaker. It fails the stated requirement of repeatable ordering.
- **d** sorts ascending, returning the five shortest sessions.

*Unit: CRUD — Sorting and Limiting Query Results*

### 2 — **b**

`countDocuments()` takes a query document and returns the count of matching documents. It accepts operators such as `$gt` freely.

- **a** returns a cursor over the matching documents, not a number.
- **c** counts only documents where `durationSeconds` equals exactly 1800.
- **d** returns a cursor capped at one document, which tells you only that at least one match exists.

*Unit: CRUD — Counting Documents in a Collection*

### 3 — **b**

The requirement is a conjunction of two disjunctions, which needs an explicit `$and` whose array holds two `$or` expressions. Implicit `$and` cannot be used here, because two `$or` keys cannot coexist in a single document.

- **a** flattens everything into one `$or`, so a nonstop flight between two unrelated airports would match.
- **c** uses implicit `$and` inside each `$or` clause, which asks for routes where `src_airport` and `dst_airport` are *both* "BOM" — an impossible condition for a real route.
- **d** requires all four conditions simultaneously, which is far narrower than the requirement.

*Unit: CRUD — Finding Documents by Using Logical Operators*

### 4 — **b**

Dot notation addresses a single field inside a subdocument, so only `contact.phone` is modified. The path must be quoted, because `contact.phone` is not a valid unquoted JavaScript key.

- **a** is the trap: assigning a new object to `contact` **replaces the entire subdocument**, discarding `email` and `fax`.
- **c** `$push` applies to array fields; `contact.phone` is a string.
- **d** is a syntax error — the dotted path is unquoted.

*Units: CRUD — Updating Documents with updateOne(); Finding Documents by Using Comparison Operators (dot notation)*

### 5 — **c**

`_id` is required in every MongoDB document and must be unique. When an inserted document omits it, MongoDB creates the field and populates it with a generated **ObjectId**.

- **a** is false; supplying `_id` is optional.
- **b** is false; a document cannot be stored without `_id`.
- **d** is false; MongoDB does not maintain auto-incrementing sequences. ObjectId is a 12-byte value, not a counter.

*Unit: Document Model — Data Types in MongoDB*

### 6 — **c**

A student enrols in many courses, and each course has many students — many entities on both sides, which is the definition of many-to-many.

- **a** is one-to-one: one transcript per student.
- **b** is one-to-one in the stated model: one hall assigned per course.
- **d** is one-to-many: a course has many assignments, but each assignment belongs to one course.

*Units: Document Model — Data Relationships; Data Modeling — Types of Data Relationships*

### 7 — **b**

Referencing avoids duplication and keeps documents small and bounded, but it does **not** improve read performance for data that is read together: retrieving a product with its reviews now needs a second query. That is the tradeoff — bounded documents and cheaper writes in exchange for a more expensive combined read.

- **a** describes embedding in a many-to-many relationship, not referencing.
- **c** is false; each collection is indexed independently, and the reviews collection would be indexed on the product reference.
- **d** is false — storing one document's `_id` in another document is exactly how references work.

*Unit: Data Modeling — Referencing Data in Documents; Scaling a Data Model*

### 8 — **a**

Performance Advisor identifies slow queries and recommends indexes, and it is available on **M10 and above**. Free and shared tiers do not have it, along with the Real-Time Performance Panel and Online Archive. The team's options are to upgrade the tier, or to analyse queries manually with `explain()`.

- **b** invents a CLI carve-out; the limitation is by tier, not by interface.
- **c** is wrong; Performance Advisor is an Atlas feature.
- **d** confuses two things: `$indexStats` reports usage of *existing* indexes and never suggests new ones.

*Units: Data Modeling — Using Atlas Tools for Schema Help; Getting Started with Atlas — Exploring the Atlas UI*

### 9 — **a**

`use` is a `mongosh` **helper**, not JavaScript, so it throws when a script is executed through `load()`. `db.getSiblingDB("<name>")` returns a database object for any database without switching the shell's current context, which makes it the correct construct inside scripts.

- **b** `db.getMongo()` returns the current connection, not a database.
- **c** `db.getDb()` does not exist.
- **d** `load()` accepts only a file path.

*Unit: The MongoDB Shell — Using the MongoDB Shell*

### 10 — **c**

The database name is supplied as the **path component of the connection URI**. `--username` then prompts for the password.

- **a** and **d** use `--db`, which `mongosh` does not recognise; the shell would connect to `test` instead.
- **b** passes the database as a query parameter, which is not a valid connection string option and produces an error.

*Unit: The MongoDB Shell — Installing and Connecting to the MongoDB Shell*

### 11 — **b**

A `MongoServerSelectionError` reporting a closed connection is the signature of a **network access** problem: Atlas is refusing the connection because the client's IP address is not on the project's allowlist. The clue is that the same credentials work from another network.

- **a** would address a `bad auth` error, not this one.
- **c** is irrelevant; Atlas creates databases implicitly on first write.
- **d** changes the resolution mechanism but not the firewall rule.

*Unit: Connecting to a MongoDB Database — Troubleshooting Atlas Connection Errors*

### 12 — **b**

`bad auth : Authentication failed` is an **authentication** error. The checks that apply are the credentials themselves — most often a typo, an unescaped special character in the password, or a rotated secret — and whether the connection string points at the intended deployment and authentication database.

- **a** describes remedies for a network access error.
- **c** would surface as connection-limit errors, not an auth failure.
- **d** would produce compatibility or protocol errors, not `bad auth`.

*Unit: Connecting to a MongoDB Database — Troubleshooting Atlas Connection Errors*

### 13 — **a**, **b**, and **c** *(3 correct)*

MongoDB stores indexed values in a B-tree: a self-balancing structure whose keys are held in ascending order from left to right, and whose nodes may have many children. That high branching factor is what keeps the tree shallow, so a lookup touches few nodes.

- **d** is the distractor: "B-tree" is not short for "binary tree", and a B-tree is not a binary tree — binary trees permit at most two children per node.

*Unit: Indexes II — How Indexes Work*

### 14 — **b**

Keys examined equalling documents examined is normal for a non-covered query — each matching index entry causes one document fetch. The problem is the ratio to `nReturned`: 48,000 entries scanned to return 120 documents means the index bounds are far too wide, so most of the filtering happens after the scan. The remedy is to extend or reorder the compound index so more of the query's predicates become index bounds.

- **a** inverts the diagnostic; the meaningful comparison is examined-versus-returned, and the ideal is close to 1:1.
- **c** is false; multikey indexes examine extra keys per document, but nothing here indicates a multikey index, and it would not explain a 400:1 ratio.
- **d** is false; a covered query shows `totalDocsExamined: 0` and a `PROJECTION_COVERED` stage.

*Unit: Indexes II — Index Usage Details via Explain*

### 15 — **a**

`wildcardProjection` is the option that includes or excludes specific field paths from a wildcard index, and it is valid only with the all-fields form `{"$**": 1}`. Excluding `payload` and `rawText` indexes every other field, including field names that do not exist yet.

- **b** puts the projection inside the key pattern, which is not valid index syntax.
- **c** attempts to create an index with a value of `0`, which is not a valid sort direction.
- **d** confuses `wildcardProjection` with `partialFilterExpression`, which filters *documents*, not fields — and `{ payload: 0 }` is not a valid filter expression.

*Unit: Indexes II — Wildcard Indexes*

### 16 — **b**

When a clustered collection also has an eligible secondary index, the query optimiser chooses the **secondary index** for a query that it supports. Forcing the internal clustered index requires an explicit `hint()`.

- **a** inverts the actual precedence.
- **c** is false; clustered collections support secondary indexes and index scans.
- **d** describes index intersection, which is not what happens here.

*Unit: Indexes II — Clustered Indexes*

### 17 — **b**

Atlas retains the most recent **30 days** of log messages per service (`mongod`, `mongos`, and others) and makes them downloadable from the UI or with `atlas logs download` on M10 and above. Logs from 45 days ago are outside the retention window. The operational lesson: incident-response processes should export logs to long-term storage rather than relying on Atlas retention.

- **a** overstates retention.
- **c** invents a UI limitation.
- **d** is impossible; auditing cannot be applied retroactively.

*Unit: Logging Basics — Server Log Rotation and Retention; MongoDB Logs in Atlas*

### 18 — **b**

Verbosity can be set globally or **per component**. Adding a verbosity value for the index component under `systemLog.component` in `mongod.conf` raises debug detail for index messages only. On a self-managed deployment `db.setLogLevel()` does the same at runtime — it is not available on Atlas clusters.

- **a** raises verbosity for every component to the maximum, which is exactly the flooding the administrator wants to avoid.
- **c** profiling level 2 records every operation into `system.profile`; it is not a log-verbosity control.
- **d** would classify every operation as slow, filling the log with slow-query entries rather than index debug messages. (For reference, `slowms` defaults to 100 ms.)

*Unit: Logging Basics — MongoDB Server Log Customizations*

### 19 — **b**

Editing the local `config` object changes nothing on the deployment. `rs.reconfig(config)` submits the modified configuration to the replica set and applies it. The `rs.add("host:port")` wrapper does the same thing in one step for the common case of adding a member.

- **a** `rs.initiate()` creates a new replica set and would be an error against a running one.
- **c** `rs.status()` reports state and takes no configuration argument.
- **d** `rs.stepDown()` triggers an election and does not apply configuration changes.

*Unit: Replication — Configuring a Replica Set in a MongoDB Deployment*

### 20 — **c**

**Read concern** specifies the durability guarantee for data returned by a read. At level `"majority"`, a read returns only data that has been acknowledged by a majority of members, which means it cannot be rolled back by a subsequent failover.

- **a** read preference controls *which members* serve reads, not what durability guarantee applies.
- **b** write concern governs acknowledgement of writes; it does not constrain what a read may return.
- **d** `maxStalenessSeconds` bounds how far behind a secondary may be, which is a recency bound rather than a rollback guarantee.

*Unit: Replication — Read and Write Concerns*

### 21 — **b**

`db.hello()` returns the connected node's view of the topology, including `isWritablePrimary`, `primary`, `secondary`, `hosts`, and `setName`. Drivers use it for exactly this purpose — determining the role of the node they are connected to.

- **a** `rs.conf()` returns the configuration document, which describes intended settings rather than current role.
- **c** `rs.printReplicationInfo()` reports oplog size and window.
- **d** returns memory statistics.

*Unit: Replication — Configuring a Replica Set (db.hello())*

### 22 — **a**

Query Targeting expresses the ratio of objects scanned to documents returned. The ideal is roughly **1:1**; a ratio near 1500:1 means the workload reads about 1500 objects for every document it returns, the classic signature of a missing or insufficiently selective index. The follow-up is to identify the offending queries with the profiler or Performance Advisor and index them.

- **b** describes queued operations, closer to Disk Queue Depth or ticket exhaustion.
- **c** and **d** describe metrics that Query Targeting does not measure.

*Unit: Database Metrics & Monitoring — Core Metrics*

### 23 — **b**

Alert *conditions* can be configured on any tier, but a shared-tier cluster only collects a limited metric set — Network, Connections, Logical Size, and Opcounters — so an alert on any other metric, Query Targeting included, will never evaluate against data and never fires.

- **a** is false; the interface used to create the alert is irrelevant.
- **c** is false; Query Targeting alerts work normally on M10 and above.
- **d** invents a constraint; the notification interval controls how often notifications repeat.

*Unit: Database Metrics & Monitoring — Configure Alerts*

### 24 — **b**

Acknowledging an alert suppresses further notifications for a period and records who is handling it. It does **not** resolve the alert. An alert transitions to `CLOSED` only when the underlying condition no longer holds.

- **a** and **c** confuse acknowledgement and notification behaviour with resolution.
- **d** is false; there is no manual close, even for a Project Owner.

*Unit: Database Metrics & Monitoring — Respond to Alerts*

### 25 — **b**

The Percona MongoDB Exporter runs against a self-managed deployment and exposes its metrics on an HTTP endpoint that Prometheus can scrape as a target. It needs a MongoDB user with `clusterMonitor` on `admin` and `read` on `local`.

- **a** Grafana **visualises** data from Prometheus; it does not collect metrics from MongoDB.
- **c** webhooks deliver Atlas alert notifications and cannot serve as a Prometheus scrape target.
- **d** the Atlas CLI reports on Atlas deployments, not self-managed ones.

*Unit: Database Metrics & Monitoring — Self-Managed Monitoring*

### 26 — **a**

RPO is the tolerable **data loss**. With one backup per night, a failure at 16:00 leaves everything written since 02:00 unrecoverable — as much as 14 hours, against a 15-minute objective. Meeting a 15-minute RPO requires continuous backup with point-in-time recovery, or far more frequent backups combined with oplog capture.

- **b** makes an unsupported claim about restore duration; nothing in the scenario establishes it.
- **c** overstates the case — logical backups can satisfy modest objectives; the schedule is the problem here.
- **d** is factually wrong: `mongodump` captures oplog entries only when `--oplog` is specified, and even then it covers just the duration of the dump.

*Unit: Self-Managed Backup & Recovery — Backup Plans on a MongoDB Server*

### 27 — **b**

The built-in `backup` role provides the minimum privileges required to back up data with `mongodump`. Its counterpart, `restore`, covers `mongorestore`. Granting a dedicated role per job is the least-privilege pattern.

- **a** `root` is a superuser role, wildly beyond what a backup job needs.
- **c** `dbOwner` grants write and administrative privileges on the database.
- **d** `readWriteAnyDatabase` grants write access across the deployment, which a backup job never needs.

*Units: Self-Managed Backup & Recovery — Backing Up a Deployment; Self-Managed Security — Authorization*

### 28 — **c**

Secondaries are upgraded first. To upgrade the last remaining member — the primary — you first run `rs.stepDown()` so an already-upgraded secondary is elected, which converts the old primary into a secondary that can then be shut down gracefully and upgraded. This keeps a writable primary available throughout.

- **a** reverses the order: `setFeatureCompatibilityVersion` is the **final** step, after every member runs the new binary.
- **b** removing members is unnecessary and risky; a graceful shutdown is sufficient.
- **d** taking down the primary without stepping it down forces an unplanned election and a window with no primary.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 29 — **b**

`mongoexport` writes **JSON or CSV** only, selected with `--type`. For JSON it offers `--jsonFormat relaxed` or `canonical`; YAML is not supported. The team can take CSV, or JSON and convert it in their pipeline.

- **a** invents an option value.
- **c** conflates the JSON format flag with an output type.
- **d** is wrong on both counts: `bsondump` converts BSON to JSON, and it operates on `.bson` dump files rather than live collections.

*Unit: DBA Tools — Data Export Tools*

### 30 — **a**

`mongodump` and `mongorestore` are supported for standalone deployments and replica sets, and are **not recommended for sharded clusters** — they cannot guarantee a consistent view across shards while the balancer moves chunks, and at multi-terabyte scale a logical dump and restore is impractically slow. Sharded clusters should be backed up with coordinated filesystem snapshots or a managed backup service such as Ops Manager, Cloud Manager, or Atlas backup.

- **b** is false; `mongodump` can connect through `mongos`.
- **c** invents a missing option.
- **d** invents a shard-count restriction.

*Units: DBA Tools — Backup and Restore Tools; Self-Managed Backup & Recovery*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| CRUD, query results & operators | 1, 2, 3, 4 | 4 |
| Document model & BSON | 5, 6 | 2 |
| Data modeling & Atlas schema tooling | 7, 8 | 2 |
| The MongoDB Shell (mongosh) | 9, 10 | 2 |
| Connecting & troubleshooting | 11, 12 | 2 |
| Indexing & explain | 13, 14, 15, 16 | 4 |
| Logging | 17, 18 | 2 |
| Replication & configuration | 19, 20, 21 | 3 |
| Metrics, monitoring & alerts | 22, 23, 24, 25 | 4 |
| Backup & recovery | 26, 27 | 2 |
| Upgrades & maintenance | 28 | 1 |
| DBA tools | 29, 30 | 2 |

**Scoring guide:** 27+ = strong; 22–26 = solid, review the missed areas; below 22 = revisit those units.

## Traps Worth Remembering From This Set

- **`sort()` and `limit()` are cursor methods.** Any option that passes them inside a second document to `find()` is wrong — that slot is the projection. The exam reuses this distractor constantly (Q1).
- **Dot notation vs. subdocument assignment.** `{ $set: { "a.b": v } }` changes one field; `{ $set: { a: { b: v } } }` replaces the whole subdocument (Q4).
- **Three different "majority" settings.** Write concern majority governs write acknowledgement; read concern majority governs what a read may return; read preference governs which member serves the read (Q20).
- **Tier gating in Atlas.** Performance Advisor, the Real-Time Performance Panel, Online Archive, and the full 40+ metric set all require M10 or above — and on shared tiers, alerts on unsupported metrics silently never fire (Q8, Q23).
- **Acknowledging an alert is not resolving it.** Only the condition clearing moves an alert to `CLOSED` (Q24).

## Using All Three Sets Together

You now have 90 questions. Cumulative coverage across the sets:

| Topic area | Set 1 | Set 2 | Set 3 | Total |
|---|---|---|---|---|
| Indexing & explain | 9 | 7 | 4 | 20 |
| Security | 5 | 5 | — | 10 |
| CRUD & query results | 3 | 4 | 4 | 11 |
| Replication | 4 | 3 | 3 | 10 |
| Monitoring & metrics | 1 | 2 | 4 | 7 |
| Backup & recovery | 2 | 2 | 2 | 6 |
| Data modeling & document model | 2 | 1 | 4 | 7 |
| Logging | 1 | 2 | 2 | 5 |
| DBA tools | 1 | 1 | 2 | 4 |
| Upgrades & maintenance | 1 | 2 | 1 | 4 |
| Shell & connecting | — | — | 4 | 4 |
| Sharded clusters | 1 | 1 | — | 2 |

Two suggestions for what to do with them:

1. **Re-test the misses only, 48 hours later.** Pull every question you got wrong across all three sets into one pass. Items missed twice indicate a genuine knowledge gap rather than a careless read.
2. **Then take a timed mixed mock.** Working question-by-question through themed sets is easier than the real exam, where topics arrive shuffled and unlabelled. Ask for a 60-question timed mock drawing on all three sets plus new items, with the answers withheld until the end.

Security is the one area Set 3 does not touch at all — if you want the next set weighted differently, say so and I will rebalance.