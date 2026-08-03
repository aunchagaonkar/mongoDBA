# MongoDB Associate DBA — Practice Set 2

**30 exam-style questions**, same format and difficulty band as Set 1, with **no overlap** — every question targets a different fact or behaviour than the first set. Three questions are *select all that apply* (marked); the rest are select one.

**How to use it:** answer all 30 before opening the key. If you scored well on Set 1 but drop below 70% here, the gap is breadth rather than depth — check the coverage map at the end to see which units to revisit.

---

## Questions

### Question 1

An inventory service stores one document per SKU in a `stock` collection. A nightly job runs the following operation for each SKU in a supplier feed. The SKU `"BRK-7741"` does not yet exist in the collection.

```js
db.stock.updateOne(
  { sku: "BRK-7741" },
  { $set: { onHand: 40, updatedAt: new Date() } },
  { upsert: true }
)
```

What is the result of this operation?

- **a.** The operation completes without modifying the collection, because no document matches the filter.
- **b.** A new document is inserted containing `sku`, `onHand`, `updatedAt`, and a generated `_id`.
- **c.** The operation fails, because `upsert` can be used only with `replaceOne()`.
- **d.** A new document is inserted containing only `onHand` and `updatedAt`, because filter fields are not carried into an upsert.

---

### Question 2

A `vendors` collection contains the following document:

```js
{
  _id: ObjectId("64f1a2b3c4d5e6f708192a3b"),
  name: "Nashik Metal Works",
  gstin: "27AABCU9603R1ZM",
  category: "fabrication",
  contact: { email: "ops@nmw.example", phone: "555-0142" }
}
```

An administrator runs:

```js
db.vendors.replaceOne(
  { _id: ObjectId("64f1a2b3c4d5e6f708192a3b") },
  { name: "Nashik Metal Works Pvt Ltd", category: "fabrication" }
)
```

What is the state of the document after this operation?

- **a.** It contains `_id`, `name`, and `category` only; `gstin` and `contact` have been removed.
- **b.** It contains all five original fields, with `name` updated to the new value.
- **c.** The operation fails, because a replacement document must include every field present in the original.
- **d.** It contains `_id`, `name`, and `category`, and `gstin` and `contact` are retained with null values.

---

### Question 3

A reporting query on an `employees` collection must return the `name` and `department` fields, must not return the `salary` field, and must not return `_id`. A developer runs:

```js
db.employees.find({}, { name: 1, department: 1, salary: 0, _id: 0 })
```

The command returns an error. What is the reason?

- **a.** A projection document cannot exclude a field that is not also present in the query filter.
- **b.** `_id` must be excluded in a separate call to `cursor.project()`.
- **c.** A projection cannot combine inclusion and exclusion of fields, with `_id` as the only exception.
- **d.** Exclusions must be expressed as `false` rather than `0` when inclusions are also present.

---

### Question 4

A `media` collection stores items where the `format` field holds a single string for some documents and an array of strings for others:

```js
{ _id: 1, title: "Annual Report",  format: "pdf" }
{ _id: 2, title: "Launch Deck",    format: ["pdf", "pptx"] }
{ _id: 3, title: "Brand Guide",    format: ["indd", "pdf"] }
```

An administrator runs `db.media.find({ format: "pdf" })`.

Which documents are returned?

- **a.** Document 1 only, because equality matching does not traverse arrays.
- **b.** Documents 2 and 3 only, because array fields require an array-aware operator to match a scalar.
- **c.** Documents 1, 2, and 3, because equality matches both a scalar value and an array containing that value.
- **d.** No documents, because the `format` field has an inconsistent type across the collection.

---

### Question 5

A legacy hospital system has been ported to MongoDB, producing a `patients` collection in which every field is flat:

```js
{
  patient_id: "P-88214",
  full_name: "Amruta Deshpande",
  blood_group: "B+",
  primary_diagnosis: "Type 2 diabetes",
  street: "14 Gangapur Rd",
  city: "Nashik",
  home_phone: "5550188",
  mobile_phone: "5550199"
}
```

The clinical team queries diagnosis and blood group constantly. The billing team queries address and phone numbers, and never needs clinical fields. Every field is in a one-to-one relationship with the patient.

Which restructuring best fits MongoDB's data modeling principles?

- **a.** Keep one collection, but move every field into a single embedded `details` subdocument.
- **b.** Split into a `patients` collection for clinical fields and a `patient_contacts` collection for address and phone fields, linked by a reference, with the phone numbers embedded as a subdocument.
- **c.** Split into two collections that share no fields, so that neither team can read the other team's data.
- **d.** Create one collection per field group in a separate database for each team.

---

### Question 6

A `deliveries` collection contains documents with two array fields, `stops` and `drivers`. An administrator attempts to create the index `db.deliveries.createIndex({ stops: 1, drivers: 1 })` and the command fails.

What is the reason for the failure?

- **a.** Multikey indexes cannot be compound; an array field may be indexed only as a single-field index.
- **b.** A multikey index can contain at most one array field, so a compound index cannot index two arrays in parallel.
- **c.** Array fields must be indexed with the `$**` wildcard syntax rather than by name.
- **d.** Both array fields must be given the same sort direction for a compound multikey index to be valid.

---

### Question 7

A `subscribers` collection already contains 500,000 documents, and the `email` field currently holds duplicate values for a small number of documents. An administrator runs:

```js
db.subscribers.createIndex({ email: 1 }, { unique: true })
```

What is the outcome?

- **a.** The index is created, and the duplicate documents are dropped from the collection.
- **b.** The index is created, and the existing duplicates are retained but not indexed.
- **c.** The index creation fails, because MongoDB builds a unique index only when the field values contain no duplicates.
- **d.** The index is created as a non-unique index, and MongoDB logs a warning.

---

### Question 8

A `customers` collection has the compound index `{ active: 1, birthdate: -1, name: 1 }`. An administrator runs the following and reviews the winning plan:

```js
db.customers.find(
  { active: true, birthdate: { $gte: ISODate("1977-01-01") } },
  { name: 1, birthdate: 1, _id: 0 }
).sort({ birthdate: -1, name: 1 })
```

The plan shows `IXSCAN` followed by `PROJECTION_COVERED`, with no `FETCH` stage.

What does the absence of a `FETCH` stage indicate?

- **a.** The query is covered: every field it needs is present in the index, so MongoDB never reads the documents from the collection.
- **b.** The result set was served from the WiredTiger cache rather than from disk.
- **c.** The projection was applied on the client side by the driver rather than on the server.
- **d.** The query matched no documents, so there was nothing to fetch.

---

### Question 9 *(Select all that apply)*

An `audit_events` collection has the compound index `{ tenantId: 1, status: 1, createdAt: 1 }`.

Which of the following queries can use this index?

- **a.** A query filtering on `tenantId` only
- **b.** A query filtering on `tenantId` and `status`
- **c.** A query filtering on `status` and `createdAt` only
- **d.** A query filtering on `tenantId` and `createdAt`

---

### Question 10

A `tickets` collection already has the index `{ status: 1, createdAt: -1 }`. A developer asks the database administrator to add `{ status: 1 }` as well, because many queries filter on `status` alone.

How should the administrator respond?

- **a.** Add the index — a single-field index is always faster than a compound index for a single-field filter.
- **b.** Decline — `status` is the prefix of the existing compound index, so those queries are already supported; the extra index would only add write and memory overhead.
- **c.** Add the index, but mark it `sparse: true` to limit its size.
- **d.** Decline — MongoDB rejects a new index whose key pattern is a prefix of an existing index.

---

### Question 11

A database administrator is investigating why the query planner selected one index over another for a frequently run query, and wants to see the execution statistics MongoDB gathered for **every** candidate plan, including the plans that were rejected.

Which `explain()` verbosity mode should the administrator use?

- **a.** `queryPlanner`
- **b.** `executionStats`
- **c.** `allPlansExecution`
- **d.** `rejectedPlans`

---

### Question 12

A manufacturing team creates a time series collection to store readings from several thousand machines:

```js
db.createCollection("readings", {
  timeseries: {
    timeField: "timestamp",
    metaField: "machine",
    granularity: "minutes"
  }
})
```

What is the purpose of the `metaField` option?

- **a.** It identifies the field MongoDB uses to order documents chronologically within each bucket.
- **b.** It holds metadata that identifies the source of the measurements, which MongoDB uses to group related measurements into the same bucket.
- **c.** It specifies the field on which MongoDB automatically creates a TTL index for the collection.
- **d.** It defines the maximum size of each bucket before a new bucket is created.

---

### Question 13

A database administrator is examining the following log entry from `mongod.log`:

```json
{
  "t": { "$date": "2026-07-14T04:22:11.088+00:00" },
  "s": "I",
  "c": "ACCESS",
  "id": 20250,
  "ctx": "conn1187",
  "msg": "Authentication succeeded",
  "attr": { "user": "reportsvc", "db": "admin" }
}
```

Which field identifies the **component** of the server that issued this message?

- **a.** The `"s"` field
- **b.** The `"c"` field
- **c.** The `"ctx"` field
- **d.** The `"id"` field

---

### Question 14

A database administrator wants the Linux `logrotate` service to manage rotation of `mongod.log` on a self-managed host, rather than rotating logs manually from `mongosh`. The rotation must not lose log entries, and `mongod` must keep writing to the same file path after each rotation.

Which configuration supports this?

- **a.** Set `systemLog.logRotate: reopen` with `systemLog.logAppend: true`, and have the `logrotate` script send `SIGUSR1` to `mongod` after rotating.
- **b.** Set `systemLog.logRotate: rename` and have the `logrotate` script send `SIGTERM` to `mongod` after rotating.
- **c.** Set `systemLog.verbosity: 1` and have the `logrotate` script restart the `mongod` service after rotating.
- **d.** Set `systemLog.destination: syslog` and configure retention in `logrotate` only.

---

### Question 15

A replica set consists of one primary and three data-bearing secondaries. The team cannot provision a fifth data-bearing host, but is concerned that the even number of voting members could prevent a primary from being elected.

What should the administrator add, and what is its role?

- **a.** A hidden member, which replicates data but is invisible to applications.
- **b.** A delayed member, which maintains a time-lagged copy of the data.
- **c.** An arbiter, which holds no data but casts a vote to break ties in elections.
- **d.** A second primary, which shares write traffic and votes independently.

---

### Question 16

A secondary in a replica set has been offline far longer than expected. When it restarts, it cannot resume replicating from where it stopped and instead performs a full initial sync.

Which characteristic of the oplog explains this behaviour?

- **a.** The oplog is a capped collection, so once it reaches its size limit the oldest entries are overwritten — and the entries the secondary needed were among them.
- **b.** The oplog is truncated automatically whenever a secondary is unreachable, to avoid unbounded growth.
- **c.** The oplog stores only the most recent operation per document, so intermediate operations were lost.
- **d.** The oplog is stored on the secondary rather than the primary, so it was cleared when the host was shut down.

---

### Question 17 *(Select all that apply)*

A database administrator is investigating sustained replication lag on the secondaries of a self-managed replica set.

Which of the following are plausible causes?

- **a.** Increased network latency between the primary and the secondaries
- **b.** Insufficient disk throughput on the secondaries relative to the primary
- **c.** A large bulk load performed with unacknowledged writes
- **d.** Enabling access control on the deployment

---

### Question 18

An administrator suspects that the storage subsystem is the bottleneck on a MongoDB host and needs to determine whether disk operations are waiting to be serviced.

Which metric answers this question?

- **a.** Swap Usage
- **b.** Disk Space Percent Free
- **c.** Disk Queue Depth
- **d.** Normalized System CPU

---

### Question 19

A long-running aggregation is consuming resources on a production `mongod` instance and must be stopped. The administrator is connected with `mongosh`.

Which sequence identifies and terminates the operation?

- **a.** Run `db.runCommand({ serverStatus: 1 })` to find the operation, then `db.adminCommand({ killOp: 1, op: <opid> })`.
- **b.** Run `db.adminCommand({ currentOp: true, "$all": true })` to find the operation's `opid`, then `db.adminCommand({ killOp: 1, op: <opid> })`.
- **c.** Run `db.adminCommand({ currentOp: true })` to find the operation, then restart the `mongod` service to clear it.
- **d.** Run `db.collection.stats()` to find the operation, then `db.adminCommand({ killCursors: 1 })`.

---

### Question 20

An application team plans to upgrade the MongoDB Node.js driver from an old major version to the current release. The MongoDB server itself is not being changed.

What should the team verify before performing the driver upgrade?

- **a.** That the oplog has been cleared so the driver can establish a fresh connection.
- **b.** That the feature compatibility version has been raised to match the new driver.
- **c.** That the new driver version is compatible with the deployment's MongoDB server version.
- **d.** That each host server has been gracefully shut down before the driver package is replaced.

---

### Question 21

A database administrator is planning a maintenance window for a replica set and is identifying which changes must be applied one member at a time.

Which of the following changes does **not** require rolling maintenance?

- **a.** Upgrading the host operating system
- **b.** Creating a new database user
- **c.** Building a new index on a large collection
- **d.** Upgrading the MongoDB server version

---

### Question 22

A team is enabling authentication on a self-managed deployment and wants to use MongoDB's default authentication mechanism, without provisioning any certificates or external directory services.

Which mechanism will they be using?

- **a.** x.509 certificate authentication
- **b.** LDAP proxy authentication
- **c.** SCRAM
- **d.** Kerberos

---

### Question 23

An administrator adds `security.authorization: enabled` to `mongod.conf` on a fresh self-managed deployment and restarts the service. The administrator now needs to provision users for three applications.

What must be done first?

- **a.** Create a user administrator, which the localhost exception permits before any other user exists.
- **b.** Disable authorization temporarily so the application users can be created, then re-enable it.
- **c.** Create the three application users first, then create an administrative user to manage them.
- **d.** Add each application user to the `local` database, because the `admin` database is reserved until an administrator exists.

---

### Question 24

A `hr` database contains a role named `payrollReader`. The role must be granted the `find` privilege on the `payslips` collection in that database.

Which command uses the correct syntax?

- **a.** `db.runCommand({ grantPrivilegesToRole: "payrollReader", privileges: { resource: { db: "hr", collection: "payslips" }, actions: [ "find" ] } })`
- **b.** `db.runCommand({ grantPrivilegesToRole: "payrollReader", privileges: [ { resource: { db: "hr", collection: "payslips" }, actions: [ "find" ] } ] })`
- **c.** `db.runCommand({ grantPrivilegesToRole: "payrollReader", privileges: [ { db: "hr", collection: "payslips", actions: [ "find" ] } ] })`
- **d.** `db.runCommand({ grantPrivilegesToRole: "payrollReader", privileges: [ { resource: { db: "hr", collection: "payslips" }, actions: "find" } ] })`

---

### Question 25

While deploying a three-member replica set, an administrator generates a file with `openssl rand -base64 756`, sets its permissions to `0400`, gives the `mongodb` user ownership, copies it to all three hosts, and references it from `security.keyFile` in each `mongod.conf`.

What does this file provide?

- **a.** The master key used by the Encrypted Storage Engine to encrypt data files on disk.
- **b.** Internal authentication between the members of the replica set, so that each member can verify the others' identity.
- **c.** The TLS certificate presented to clients when they connect over an encrypted channel.
- **d.** The seed used to generate ObjectId values consistently across all members.

---

### Question 26

An organization runs MongoDB Community Edition on its own servers and wants MongoDB to natively encrypt its data files on disk.

What should the administrator tell the organization?

- **a.** Encryption at rest is enabled by default in the WiredTiger storage engine and requires no configuration.
- **b.** The Encrypted Storage Engine is a MongoDB Enterprise feature and is not available in Community Edition.
- **c.** Encryption at rest is configured by setting `net.tls.mode` to `requireTLS` on each host.
- **d.** Encryption at rest is available in Community Edition, but only for collections created after the feature is enabled.

---

### Question 27

A business states that if its order-processing database fails, the organization can accept losing no more than 15 minutes of committed transactions.

Which planning value has the business defined?

- **a.** Recovery Point Objective (RPO)
- **b.** Recovery Time Objective (RTO)
- **c.** The oplog window
- **d.** The `wtimeout` value for majority writes

---

### Question 28

A `mongodump` backup was created in a `dump/` directory for an entire deployment. An administrator now needs to restore only the `grades` collection from the `sample_training` database.

Which command should the administrator use?

- **a.** `mongorestore --db sample_training --collection grades dump/`
- **b.** `mongorestore --nsInclude=grades dump/`
- **c.** `mongorestore --nsInclude=sample_training.grades dump/`
- **d.** `mongorestore --nsExclude=sample_training.grades dump/`

---

### Question 29

A nightly feed arrives as a JSON file containing partial records for a `products` collection. Each record in the file carries the `_id` and only the fields that changed. The import must apply those changed fields to the matching documents while leaving all other existing fields intact.

Which `mongoimport` mode should be used?

- **a.** `--mode=insert`
- **b.** `--mode=upsert`
- **c.** `--mode=merge`
- **d.** `--mode=delete`

---

### Question 30

A team is sharding an `events` collection. The candidate shard key is a monotonically increasing timestamp, and the workload is insert-heavy. The administrator recommends hashed sharding instead of ranged sharding on that field.

What is the reason for this recommendation?

- **a.** With ranged sharding on a steadily increasing value, every new insert falls into the chunk holding the highest range, concentrating writes on one shard; hashing the key spreads inserts evenly.
- **b.** Ranged sharding does not support date or timestamp values as shard keys.
- **c.** Hashed sharding prevents the balancer from migrating chunks, which eliminates write contention.
- **d.** Hashed sharding allows the shard key to be changed later, whereas ranged sharding does not.

---

## Answer Key

### 1 — **b**

With `upsert: true` and no matching document, MongoDB inserts a new document built from the **equality conditions in the filter** plus the fields in the update expression, and generates an `_id`. So the new document holds `sku`, `onHand`, `updatedAt`, and `_id`.

- **a** describes the behaviour without `upsert`.
- **c** is false; `upsert` is an option on `updateOne()`, `updateMany()`, `replaceOne()`, and `findAndModify()`.
- **d** is the trap: filter equality fields *are* carried into the inserted document, which is what makes upsert useful.

*Unit: CRUD — Updating Documents with updateOne()*

### 2 — **a**

`replaceOne()` substitutes the **entire** document with the replacement, preserving only `_id`. Any field absent from the replacement document is gone. This is the key difference from `updateOne()` with `$set`, which modifies only the named fields.

- **b** describes `$set` behaviour.
- **c** is false; a replacement document has no obligation to match the original's shape.
- **d** invents a null-retention behaviour that does not exist.

*Unit: CRUD — Replacing a Document in MongoDB*

### 3 — **c**

A projection document may either include fields or exclude fields, not both — with `_id` as the sole exception, which can be suppressed with `_id: 0` in an otherwise inclusive projection. Here `salary: 0` conflicts with the inclusions. The correct projection is `{ name: 1, department: 1, _id: 0 }`; excluding `salary` is unnecessary because inclusion already omits it.

- **a**, **b**, and **d** invent rules that do not exist.

*Unit: CRUD — Returning Specific Data from a Query*

### 4 — **c**

An equality match on a field whose value is an array succeeds when **any element** of the array equals the value, and it also matches documents where the field holds that value as a scalar. All three documents are returned. This is why `$elemMatch` exists — to constrain multiple conditions to a single element.

- **a** and **b** each cover only half of the behaviour.
- **d** is false; MongoDB's flexible schema permits differing types for the same field.

*Unit: CRUD — Querying on Array Elements*

### 5 — **b**

Two collections separate the two access patterns, so each team reads only what it queries — the practical form of "data that is accessed together should be stored together." A reference (the patient identifier) preserves the one-to-one relationship, and the two phone numbers become an embedded subdocument because they are always read together with the rest of the contact data.

- **a** nests everything more deeply without separating the access patterns; both teams still read the whole document.
- **c** is the anti-pattern: two collections with no shared reference cannot be related at all.
- **d** confuses access control with schema design and adds needless operational overhead.

*Unit: Data Modeling — Modeling Data Relationships; Embedding Data in Documents*

### 6 — **b**

An index becomes multikey when an indexed field holds an array. MongoDB allows **at most one array field per index**, because indexing two arrays in parallel would require a key for every combination of their elements — a combinatorial explosion. A compound index may combine one array field with any number of scalar fields.

- **a** is false; compound multikey indexes are supported.
- **c** describes wildcard indexes, which address unknown field *names*, not arrays.
- **d** invents a sort-direction rule.

*Unit: Indexes — Creating a Multikey Index in MongoDB*

### 7 — **c**

MongoDB creates a unique index only if the indexed field values contain no duplicates. With duplicates already present, the build fails and the administrator must resolve the duplicate values first. Once the index exists, later inserts or updates that would introduce a duplicate fail with a duplicate key error and the document is not written.

- **a** is dangerous and false — MongoDB never deletes data to satisfy an index build.
- **b** and **d** describe silent degradation that MongoDB does not perform.

*Unit: Indexes — Creating a Single Field Index in MongoDB*

### 8 — **a**

`PROJECTION_COVERED` with no `FETCH` means the query is **covered**: the filter fields, the sort fields, and every projected field all exist in the index, and `_id` is excluded, so MongoDB answers entirely from index keys without touching the documents. Covered queries are the fastest read pattern available.

- **b** confuses cache residency with plan stages; a cached document read still shows `FETCH`.
- **c** is false; projections are applied server-side.
- **d** would show `nReturned: 0`, not a covered projection.

*Unit: Indexes — Working with Compound Indexes (Cover a Query by the Index)*

### 9 — **a**, **b**, and **d** *(3 correct)*

A compound index can serve any query that includes a **prefix** of its key pattern. `tenantId` alone (a) and `tenantId` + `status` (b) are prefixes. Option (d) also uses the index: `tenantId` bounds the scan, and because `status` is skipped, `createdAt` cannot bound it further — so MongoDB scans every key under that tenant and applies `createdAt` as a filter. The index is used, but `totalKeysExamined` will be much higher than `nReturned`.

- **c** is the exception: skipping the leading field means there is no usable bound, so the planner falls back to a collection scan.

*Unit: Indexes — Working with Compound Indexes; Indexes II — Optimized Compound Indexes*

### 10 — **b**

`status` is the prefix of `{ status: 1, createdAt: -1 }`, so queries filtering on `status` alone already use that index. Adding `{ status: 1 }` is redundant: it must be updated on every write, consumes cache and disk, and gives the planner an extra candidate to evaluate — the definition of over-indexing.

- **a** is false; a compound index serves prefix queries essentially as well as a dedicated single-field index.
- **c** does not fix redundancy; sparseness is unrelated.
- **d** is false — MongoDB permits redundant indexes, which is exactly why administrators must catch them.

*Units: Indexes — Creating a Single Field Index; Indexes II — How to Monitor Indexes*

### 11 — **c**

`allPlansExecution` runs the query and returns execution statistics for **all candidate plans**, including the scores and partial results MongoDB used to choose the winner. That is what makes it the right mode for understanding a planner decision.

- **a** `queryPlanner` (the default) shows the winning plan and lists rejected plans, but does not execute the query or report their statistics.
- **b** `executionStats` reports statistics for the winning plan only.
- **d** is not a verbosity mode; `rejectedPlans` is a field inside the output.

*Unit: Indexes II — Index Usage Details via Explain*

### 12 — **b**

`metaField` names the field holding the metadata that identifies the *source* of each measurement — a machine ID, sensor ID, or ticker symbol. MongoDB uses it, together with time, to bucket related measurements together, which is what makes time series storage and querying efficient.

- **a** describes `timeField`, which is required.
- **c** is wrong; automatic removal is configured with `expireAfterSeconds`.
- **d** is wrong; bucketing is driven by `granularity` and time range, not by a size set through `metaField`.

*Unit: Indexes II — Time Series Collections*

### 13 — **b**

The `"c"` field carries the **component** that produced the message — for example `ACCESS`, `REPL`, `INDEX`, `NETWORK`, or `STORAGE`. Grepping on the component is the standard way to isolate a class of events in `mongod.log`.

- **a** `"s"` is severity (`I` for informational, `W` warning, `E` error, `F` fatal).
- **c** `"ctx"` is the context — the thread that issued the message, here connection 1187.
- **d** `"id"` is the message's unique numeric identifier.

*Unit: Logging Basics — MongoDB Log Events*

### 14 — **a**

The `reopen` behaviour closes and reopens the log file at the same path after an external tool has renamed it, which is exactly how `logrotate` works. It requires `logAppend: true` so that `mongod` appends to the reopened file rather than truncating it. `logrotate` then signals `mongod` with `SIGUSR1` in its `postrotate` script to trigger the reopen.

- **b** `SIGTERM` terminates `mongod`; `rename` is MongoDB's own rotation behaviour and is not what an external rotator needs.
- **c** confuses verbosity with rotation, and restarting the service to rotate a log is unnecessary downtime.
- **d** switching to syslog changes the destination entirely and does not satisfy "keep writing to the same file path."

*Unit: Logging Basics — Server Log Rotation and Retention*

### 15 — **c**

An arbiter exists precisely for this case: it participates in elections with a vote but stores no data, restoring an odd number of voting members so that a majority can always be reached. MongoDB recommends an odd number of voting members.

- **a** a hidden member holds data and votes, but adding one keeps the count even.
- **b** a delayed member serves recovery scenarios, not election arithmetic.
- **d** is not a MongoDB concept — a replica set has exactly one primary.

*Unit: Replication — Replication in MongoDB; Automatic Failover and Elections*

### 16 — **a**

The oplog is a **capped collection** that behaves like a circular buffer: when it reaches its configured size, the oldest entries are overwritten. A secondary that stays offline longer than the oplog window loses the entries it needed to resume, so its only path back is a full initial sync. This is why `rs.printReplicationInfo()` matters before any maintenance.

- **b** is false; the oplog is not truncated in response to member availability.
- **c** is false; the oplog records every operation that modifies data, in order, and its entries are idempotent.
- **d** is false; every member maintains its own oplog, and it persists across restarts.

*Unit: Replication — The MongoDB Operation Log*

### 17 — **a**, **b**, and **c** *(3 correct)*

Network latency delays oplog transfer. Insufficient disk throughput on a secondary means it cannot flush writes as fast as the primary produces them. And heavy bulk loads with unacknowledged writes let the primary race ahead, because nothing forces it to wait for secondaries to keep up — a suitable write concern is the corrective lever.

- **d** is unrelated; access control enforces authentication and role checks and is not a source of replication lag.

*Unit: Replication — The MongoDB Operation Log*

### 18 — **c**

Disk Queue Depth is the average length of the queue of requests issued to the disk partition MongoDB uses. A persistently rising queue means operations are waiting to be serviced — the direct signal that storage is the constraint.

- **a** Swap Usage shows how much memory has spilled to the swap device.
- **b** Disk Space Percent Free is a capacity metric, not a latency or queueing metric.
- **d** CPU tells you about compute saturation, not disk service time.

*Unit: Database Metrics & Monitoring — Core Metrics*

### 19 — **b**

`currentOp` returns in-progress operations, including each one's `opid`; passing `"$all": true` widens the output to include idle connections and system operations. `killOp` then terminates the chosen operation by its `opid`.

- **a** `serverStatus` gives an overview of the instance's state and does not list active operations.
- **c** restarting `mongod` is disruptive and unnecessary when `killOp` exists.
- **d** `stats()` returns collection storage statistics.

*Unit: Database Metrics & Monitoring — Command Line Metrics*

### 20 — **c**

Driver and server versions have a documented compatibility matrix. Checking it first is the step that prevents a driver upgrade from breaking the application. Afterwards, test thoroughly in a pre-production environment, since a new driver can introduce behavioural regressions even when it is nominally compatible.

- **a** is actively harmful — clearing the oplog forces an initial sync.
- **b** feature compatibility version applies to **server** upgrades, not driver upgrades.
- **d** is unnecessary; a driver lives in the application, so the database hosts stay up.

*Unit: Upgrades & Maintenance — MongoDB Client Driver Upgrades*

### 21 — **b**

Creating a user is a write to the `admin` database, so it **replicates** to every member automatically — one operation, no rolling procedure.

- **a**, **c**, and **d** all require touching each host in turn: an OS upgrade restarts each `mongod`, a server version upgrade replaces each binary, and an index build is performed member by member in a rolling fashion to avoid loading the whole set at once.

*Unit: Upgrades & Maintenance — Zero Downtime Maintenance*

### 22 — **c**

SCRAM (Salted Challenge Response Authentication Mechanism) is MongoDB's default mechanism. It verifies identity through a challenge-response exchange protected by a cryptographic key, and needs no certificates or external services.

- **a** x.509 is supported but requires a TLS connection and issued certificates.
- **b** LDAP is a MongoDB Enterprise feature requiring an external directory.
- **d** Kerberos is likewise Enterprise and requires a KDC.

*Unit: Self-Managed Security — Enabling Authentication*

### 23 — **a**

With access control enabled and no users yet, MongoDB's localhost exception lets you connect from the local host and create exactly one user — which must be the **user administrator** (typically with `userAdminAnyDatabase`). Once that user exists the exception closes, and all further users are created by authenticating as that administrator.

- **b** is an unnecessary security regression.
- **c** inverts the required order; the exception permits only one user, and it must be the administrator.
- **d** is wrong; `admin` is the correct database for administrative users and is not reserved.

*Unit: Self-Managed Security — Establishing Authorization*

### 24 — **b**

`privileges` must be an **array** of privilege documents, and each document needs a `resource` document (naming the database and collection) plus an `actions` **array**.

- **a** passes a single document instead of an array.
- **c** omits the `resource` wrapper, putting `db` and `collection` at the top level of the privilege document.
- **d** passes `actions` as a string rather than an array.

*Unit: Self-Managed Security — Establishing Authorization (`grantPrivilegesToRole`)*

### 25 — **b**

A keyfile referenced by `security.keyFile` provides **internal authentication** among replica set members: every member presents the shared secret to prove it belongs to the set. It must be identical on all members, owned by the `mongodb` user, and readable only by that user (`0400`) — MongoDB refuses to start with looser permissions.

- **a** is the other, unrelated use of the word "keyfile" — local key management for the Encrypted Storage Engine's master key. Read the setting name to tell them apart.
- **c** describes `net.tls.certificateKeyFile`.
- **d** is not a MongoDB concept; ObjectId generation needs no shared seed.

*Unit: Replication — Deploying a Replica Set; Self-Managed Security — Authentication*

### 26 — **b**

MongoDB's native encryption at rest, the Encrypted Storage Engine, is available only in **MongoDB Enterprise** (and in Atlas). A Community deployment must look elsewhere: full-disk or filesystem-level encryption provided by the operating system or storage layer, and CSFLE for field-level protection.

- **a** is false; WiredTiger does not encrypt data files by default.
- **c** confuses transport encryption with at-rest encryption.
- **d** invents a per-collection limitation.

*Unit: Self-Managed Security — Encryption in Self-Managed MongoDB Deployments*

### 27 — **a**

The **Recovery Point Objective** is the maximum amount of data loss the business will accept, expressed as a span of time. "No more than 15 minutes of transactions" is an RPO, and it dictates backup frequency — here, continuous backup with point-in-time recovery rather than a daily dump.

- **b** RTO is the maximum tolerable *downtime*, which this statement does not address.
- **c** the oplog window is a MongoDB implementation detail, not a business objective.
- **d** `wtimeout` bounds how long a write waits for acknowledgement.

*Unit: Self-Managed Backup & Recovery — Backup Plans on a MongoDB Server*

### 28 — **c**

`mongorestore` selects what to restore with `--nsInclude`, which takes a fully qualified namespace: `database.collection`. Pointing it at the top of the dump directory lets `mongorestore` locate the right files itself.

- **a** uses `mongodump`'s options; `--db` and `--collection` are not how `mongorestore` filters a restore.
- **b** omits the database portion of the namespace.
- **d** `--nsExclude` restores everything *except* that collection — the opposite of the requirement.

*Units: DBA Tools — Restore Tools; Self-Managed Backup & Recovery — Restoring a Deployment*

### 29 — **c**

`--mode=merge` merges the fields in each imported record into the existing document that matches, leaving fields absent from the file untouched. That is exactly the partial-update requirement.

- **a** `insert` is the default and fails on documents whose `_id` already exists.
- **b** `upsert` **replaces** the whole matching document with the record from the file, so fields missing from the file would be lost.
- **d** `delete` removes documents matching the imported records.

*Unit: DBA Tools — Data Import Tools*

### 30 — **a**

Ranged sharding assigns contiguous shard key ranges to chunks. With a monotonically increasing key, every insert has a higher value than the last, so all inserts land in the single chunk holding the top of the range — one shard absorbs the entire write load while the others idle. Hashed sharding distributes documents by a hash of the key value, so sequential inserts scatter across shards evenly.

- **b** is false; timestamps are valid ranged shard keys, just a poor choice for insert-heavy workloads.
- **c** is false; the balancer continues to migrate chunks with a hashed shard key.
- **d** is false and unrelated to distribution.

*Units: Getting Started with Atlas — Architecture Overview; sharding fundamentals (see docs on Hashed vs Ranged Sharding)*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| CRUD operations & projections | 1, 2, 3, 4 | 4 |
| Data modeling | 5 | 1 |
| Indexing & explain | 6, 7, 8, 9, 10, 11, 12 | 7 |
| Logging | 13, 14 | 2 |
| Replication & the oplog | 15, 16, 17 | 3 |
| Metrics & monitoring | 18, 19 | 2 |
| Upgrades & maintenance | 20, 21 | 2 |
| Security: authn, authz, encryption | 22, 23, 24, 25, 26 | 5 |
| Backup & recovery | 27, 28 | 2 |
| DBA tools | 29 | 1 |
| Sharded cluster architecture | 30 | 1 |

**Scoring guide:** 27+ = strong; 22–26 = solid, review the missed areas; below 22 = revisit those units before a timed mock.

## Traps Worth Remembering From This Set

Five distinctions in this set account for a disproportionate share of exam mistakes:

- **`replaceOne()` vs `updateOne()` with `$set`** — replacement discards every unlisted field (Q2), and `--mode=upsert` in `mongoimport` behaves the same way, which is why `merge` exists (Q29).
- **Compound index prefixes** — an index is usable whenever the query includes its leading field, even if a middle field is skipped; only skipping the *leading* field forces a collection scan (Q9).
- **The word "keyfile" means two different things** — `security.keyFile` for replica set internal authentication, versus a local key management keyfile for the Encrypted Storage Engine (Q25).
- **Feature compatibility version applies to server upgrades only** — it has no role in driver upgrades (Q20).
- **RPO is data loss, RTO is downtime** — the exam tests these in scenarios that describe one and offer the other as the first distractor (Q27).