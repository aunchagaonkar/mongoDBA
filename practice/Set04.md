# MongoDB Associate DBA — Practice Set 4

**30 exam-style questions**, no overlap with Sets 1–3. Security returns with four questions, and this set covers the last substantial gaps in the syllabus: `mongosh` configuration, the diagnostic and GridFS tools, filesystem snapshot mechanics, and replication defaults. Two questions are *select all that apply* (marked).

**How to use it:** answer all 30 before opening the key. The closing section shows cumulative coverage across all 120 questions and what remains thin.

---

## Questions

### Question 1

An `orders` collection stores a `status` field holding values such as `"placed"`, `"shipped"`, `"delivered"`, `"cancelled"`, and `"refunded"`. A reporting query must return every order that is **not** cancelled and **not** refunded.

Which query document should be used?

- **a.** `{ status: { $nin: ["cancelled", "refunded"] } }`
- **b.** `{ status: { $in: ["cancelled", "refunded"] } }`
- **c.** `{ status: { $ne: ["cancelled", "refunded"] } }`
- **d.** `{ status: { $not: ["cancelled", "refunded"] } }`

---

### Question 2

A `podcasts` collection contains a document with a `hosts` array holding two names. An administrator must add a third name to that array while keeping the two existing entries.

Which update document accomplishes this?

- **a.** `{ $set: { hosts: "Priya Nair" } }`
- **b.** `{ $push: { hosts: "Priya Nair" } }`
- **c.** `{ $upsert: { hosts: "Priya Nair" } }`
- **d.** `{ $insert: { hosts: "Priya Nair" } }`

---

### Question 3 *(Select all that apply)*

A `sales` collection stores documents with a `customer` subdocument containing an integer `age` field. An administrator must return every sale made to a customer aged 65 or older.

Which query documents return the correct result set?

- **a.** `{ "customer.age": { $gte: 65 } }`
- **b.** `{ "customer.age": { $gt: 64 } }`
- **c.** `{ customer.age: { $gte: 65 } }`
- **d.** `{ "customer.age": { $lte: 65 } }`

---

### Question 4

A payments team is designing a `transactions` collection and needs to store monetary amounts. Rounding errors of even a fraction of a unit are unacceptable, because the values are summed across millions of records for reconciliation.

Which BSON type should be used for the amount field?

- **a.** `double`, because it is BSON's standard floating-point type
- **b.** `decimal128`, because it provides exact decimal representation
- **c.** `32-bit integer`, because integers cannot lose precision
- **d.** `string`, because textual values are never subject to arithmetic rounding

---

### Question 5

A team is deciding whether to embed a customer's shipping address inside the customer document or to store addresses in a separate collection and reference them. Each customer has exactly one shipping address, and the application always reads the address together with the customer record.

What is the primary advantage of embedding here?

- **a.** It avoids duplicating the address data across multiple collections.
- **b.** It retrieves the customer and the address in a single document read, with no application-side join.
- **c.** It guarantees the customer document will stay under the 16 MB BSON limit.
- **d.** It allows the address fields to be indexed, which is not possible for referenced documents.

---

### Question 6

A database administrator wants Atlas to identify schema design problems in a collection — such as unbounded arrays or excessive indexes — along with guidance on resolving them.

Which part of the Atlas interface provides this?

- **a.** The Indexes tab in Data Explorer
- **b.** The Schema Anti-Patterns tab in Data Explorer
- **c.** The Find tab in Data Explorer
- **d.** The Real-Time Performance Panel

---

### Question 7

An administrator wants `mongosh` to open Vim whenever the `edit` command is used, for the current session.

Which command sets this?

- **a.** `config.get("editor", "vim")`
- **b.** `config.set("editor", "vim")`
- **c.** `db.config.set("editor", "vim")`
- **d.** `config.apply({ editor: "vim" })`

---

### Question 8

An administrator on a Linux workstation wants `mongosh` to display 50 documents per batch when using the `it` iterator, and wants that setting to persist across every future shell session for all users on the host.

What should the administrator do?

- **a.** Add `displayBatchSize: 50` under a `mongosh:` key in `/etc/mongosh.conf`, in YAML format.
- **b.** Run `config.set('displayBatchSize', 50)` once, which writes the value to the server.
- **c.** Add `displayBatchSize=50` to `~/.mongoshrc.js`.
- **d.** Pass `--displayBatchSize 50` to `mongosh`; there is no persistent setting for this option.

---

### Question 9

A sharded `orders` collection uses the compound shard key `{ storeId: 1, orderDate: 1 }`. A database administrator is reviewing which application queries can be routed to a single shard rather than broadcast to all of them.

Which query can `mongos` route without contacting every shard?

- **a.** A query filtering on `orderDate` only
- **b.** A query filtering on `storeId`
- **c.** A query filtering on `customerEmail` only
- **d.** A query with no filter, sorted by `orderDate`

---

### Question 10

A `telemetry` collection receives a very high volume of inserts. Over time the team has created twelve indexes on it to support various reports. Insert throughput has degraded noticeably, although read queries are fast.

What explains the degradation?

- **a.** Indexes are rebuilt from scratch on every insert once a collection exceeds ten indexes.
- **b.** Every write operation must update all affected indexes, so each additional index adds write cost.
- **c.** Indexes are stored in the same B-tree as the documents, so inserts must rebalance the collection.
- **d.** MongoDB serialises inserts while any index exists, so throughput is independent of the number of indexes.

---

### Question 11

An administrator wants to remove every user-defined index from a `staging` collection before reloading it, and runs `db.staging.dropIndexes()`.

What is the result?

- **a.** All indexes are dropped, including the index on `_id`, which is recreated when the next document is inserted.
- **b.** All indexes are dropped except the default index on `_id`, which cannot be dropped.
- **c.** The command fails, because `dropIndexes()` requires an array of index names.
- **d.** All indexes are hidden from the query planner but remain on disk until the collection is dropped.

---

### Question 12

A `products` collection has a sparse index created as `db.products.createIndex({ discount: 1 }, { sparse: true })`. Roughly 10% of products carry a `discount` field. An administrator runs a query that returns **all** products sorted by `discount`, and the plan shows a collection scan with an in-memory sort.

Why did the query planner decline to use the sparse index?

- **a.** Sparse indexes cannot support sort operations, only equality matches.
- **b.** Using the sparse index would omit the documents that have no `discount` field, producing an incomplete result set.
- **c.** The index must be rebuilt with `partialFilterExpression` before the planner will consider it for a sort.
- **d.** Sparse indexes are only considered when the queried field is also the collection's shard key.

---

### Question 13

A team creates a clustered collection and specifies `expireAfterSeconds` in the clustered index definition.

What does this accomplish?

- **a.** It causes MongoDB to remove expired documents automatically, without requiring a separate TTL index.
- **b.** It limits how long the clustered index is retained in the WiredTiger cache.
- **c.** It sets the maximum age of an index entry before the index is rebuilt.
- **d.** It converts the collection to a capped collection with a time-based size limit.

---

### Question 14

A database administrator is connected to a self-managed `mongod` instance with `mongosh` but does not have shell access to the host filesystem, so cannot read `mongod.log` directly. The administrator needs to see the most recent log messages the server has produced.

What should the administrator run?

- **a.** `show log global`
- **b.** `db.adminCommand({ logRotate: 1 })`
- **c.** `db.setLogLevel(1)`
- **d.** `db.currentOp()`

---

### Question 15

A new team member must be able to download `mongod` logs from an Atlas M20 cluster, and nothing more.

Which is the minimum Atlas role that grants this?

- **a.** Organization Read Only
- **b.** Project Read Only
- **c.** Project Data Access Read Only
- **d.** Organization Member

---

### Question 16

A database administrator is explaining replica set benefits to a new colleague and defines one term as: *the guarantee that data which has been committed will not be lost in the event of a system failure.*

Which term is being defined?

- **a.** High availability
- **b.** Data durability
- **c.** Data partitioning
- **d.** Reduced latency

---

### Question 17

The secondaries in a replica set lose network connectivity to the primary. After a period, they call an election and one of them becomes the new primary.

What determines how long the secondaries wait before calling that election?

- **a.** The configured election timeout, which is 10 seconds by default
- **b.** The oplog window reported by `rs.printReplicationInfo()`
- **c.** The `wtimeout` value on the most recent write operation
- **d.** The `maxStalenessSeconds` value in the clients' connection strings

---

### Question 18

Several application teams connect to the same replica set. Rather than requiring every team to specify write and read guarantees in their own code, the database administrator wants to establish `"majority"` as the deployment-wide default for both.

Which command should the administrator run?

- **a.** `db.adminCommand({ setDefaultRWConcern: 1, defaultReadConcern: { level: "majority" }, defaultWriteConcern: { w: "majority" } })`
- **b.** `rs.reconfig({ defaultReadConcern: "majority", defaultWriteConcern: "majority" })`
- **c.** `db.adminCommand({ setParameter: 1, writeConcern: "majority", readConcern: "majority" })`
- **d.** Write and read concerns can only be set per operation; there is no deployment-wide default.

---

### Question 19

To speed up a large one-time data migration, a developer configures the loader to write with `{ w: 0 }`.

What risk does this introduce?

- **a.** The writes are applied only to secondaries, and never reach the primary.
- **b.** The client receives no acknowledgement that the writes succeeded, so failures can pass unnoticed, and the unthrottled write rate can push secondaries into replication lag.
- **c.** The writes are held in the oplog but not applied until a write concern of at least 1 is used.
- **d.** The writes are rejected, because `w: 0` is not a valid write concern value.

---

### Question 20

A database administrator needs to see the rate at which inserts, queries, updates, deletes, and commands are being performed against a deployment, in order to correlate a performance change with a shift in workload.

Which metric provides this?

- **a.** Connections
- **b.** Opcounters
- **c.** Logical Size
- **d.** Tickets Available

---

### Question 21

An engineer holds the `clusterAdmin` database role and attempts to configure a new alert condition for a specific host in an Atlas project. The option is unavailable.

What does the engineer need?

- **a.** The Project Owner role, which is required to configure alerts.
- **b.** The `dbAdminAnyDatabase` role in addition to `clusterAdmin`.
- **c.** An M10 or higher cluster, since alerts cannot be configured on smaller tiers.
- **d.** The Atlas CLI, since host-level alerts cannot be configured in the UI.

---

### Question 22

An administrator has locked the database, created an LVM snapshot volume named `mdb-snapshot`, and unlocked the database. The next step is to produce a compressed `tar` archive of the snapshot's contents for off-host storage.

What must happen before the `tar` archive can be created?

- **a.** The snapshot volume must be mounted, typically read-only, so its filesystem contents are accessible.
- **b.** The snapshot volume must be converted to a BSON archive with `bsondump`.
- **c.** The `mongod` service must be stopped for the duration of the archiving.
- **d.** The database must be locked again with `db.fsyncLock()` until the archive completes.

---

### Question 23

A 2 TB dataset must be restored into a replica set inside a tight maintenance window. The team plans to build the indexes afterwards, during off-peak hours, to reduce load during the restore itself.

Which `mongorestore` option supports this plan?

- **a.** `--drop`
- **b.** `--noIndexRestore`
- **c.** `--oplogReplay`
- **d.** `--nsExclude`

---

### Question 24

Following a security incident, an organisation needs a record showing which users were connected at a given time and what configuration changes were made, so investigators can reconstruct events after the fact.

Which security practice provides this?

- **a.** Authentication
- **b.** Authorization
- **c.** Auditing
- **d.** Role-Based Access Control

---

### Question 25 *(Select all that apply)*

A database administrator is planning an auditing configuration for a MongoDB Enterprise deployment.

Which statements about auditing are accurate?

- **a.** Auditing can support the analysis of security incidents after they occur.
- **b.** Auditing is often required to satisfy regulatory obligations.
- **c.** Auditing improves database performance by reducing the work the server must repeat.
- **d.** It is generally recommended to audit every read and write operation performed by every user.

---

### Question 26

An administrator is enabling TLS on a self-managed deployment and proposes generating a self-signed certificate for each server rather than obtaining certificates from a certificate authority.

What should the administrator understand about this approach?

- **a.** MongoDB rejects self-signed certificates, so TLS cannot be enabled at all.
- **b.** MongoDB accepts self-signed certificates, but clients cannot validate the servers' identity, leaving the deployment exposed to man-in-the-middle attacks.
- **c.** Self-signed certificates disable encryption while still requiring TLS handshakes, so traffic remains readable.
- **d.** Self-signed certificates are equivalent to CA-issued certificates, because both use the same encryption algorithms.

---

### Question 27

A development team wants to protect specific fields with Client-Side Field Level Encryption, but does not want to write application code that specifies which fields to encrypt and how on every read and write.

Which MongoDB capability addresses this?

- **a.** Automatic encryption, available in MongoDB Enterprise, which performs encrypted reads and writes without per-field application logic
- **b.** The Encrypted Storage Engine, which encrypts the named fields as they are written to disk
- **c.** TLS with `requireTLS`, which encrypts the fields in transit without application changes
- **d.** A `partialFilterExpression` on the index covering the sensitive fields

---

### Question 28

An administrator needs a real-time view of a MongoDB deployment's insert, query, and command rates, refreshed every 5 seconds.

Which command is correct?

- **a.** `mongostat -o='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' "<uri>" 5`
- **b.** `mongostat -o='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate,opcounters.command.rate()=Command Rate' "<uri>" 5000`
- **c.** `mongostat -stats='host,opcounters.insert.rate()=Insert Rate,opcounters.query.rate()=Query Rate' "<uri>" 5`
- **d.** `mongostatus -o='host,opcounters.insert.rate()=Insert Rate' "<uri>" 5`

---

### Question 29

A `mongorestore` operation is failing on one `.bson` file from a dump directory. The administrator needs to inspect the file directly to see the data types and sizes of the documents it contains, in order to diagnose the failure.

Which command should the administrator use?

- **a.** `bsondump --pretty accounts.bson`
- **b.** `bsondump --type=debug accounts.bson`
- **c.** `mongoexport --type=bson --collection=accounts`
- **d.** `mongofiles list accounts.bson`

---

### Question 30

A `grades` database has a GridFS store containing many files. An administrator needs to list only the files whose names **begin with** `"final"`.

Which command should the administrator use?

- **a.** `mongofiles -d=grades search final`
- **b.** `mongofiles -d=grades list final`
- **c.** `mongofiles -d=grades list --files="final"`
- **d.** `mongofiles -d=grades get final`

---

## Answer Key

### 1 — **a**

`$nin` selects documents whose field value matches **none** of the values in the array. Worth knowing for the exam: `$nin` also matches documents where the field is **absent** entirely, so if some orders lack a `status` field they will be returned too.

- **b** `$in` returns the opposite set — exactly the cancelled and refunded orders.
- **c** `$ne` compares against a single value; supplying an array asks for documents whose `status` is not literally that array, so every document matches.
- **d** `$not` inverts an operator expression, not a bare array, so this is invalid.

*Unit: CRUD — Finding Documents in a Collection*

### 2 — **b**

`$push` appends a value to an existing array field, leaving the current elements in place. (If the field does not exist, `$push` creates it as an array.)

- **a** `$set` replaces the value of `hosts` with the string `"Priya Nair"`, destroying the array and both existing entries — the trap in this question.
- **c** `upsert` is an option, not an update operator, and controls document insertion rather than field modification.
- **d** `$insert` is not a MongoDB operator.

*Unit: CRUD — Updating Documents with updateOne()*

### 3 — **a** and **b** *(2 correct)*

Because `age` is stated to be an integer, `$gte: 65` and `$gt: 64` select the same documents. Both are correct. (This equivalence holds only for integers — with fractional ages, a value of 64.5 would satisfy `$gt: 64` but not `$gte: 65`, which is why the stem specifies the type.)

- **c** is a syntax error: a dotted field path must be quoted, since `customer.age` is not a valid unquoted key.
- **d** `$lte: 65` returns customers aged 65 **and under** — the opposite population.

*Unit: CRUD — Finding Documents by Using Comparison Operators*

### 4 — **b**

`decimal128` is BSON's 128-bit decimal type. It represents decimal values exactly, so repeated addition of monetary amounts does not accumulate the binary floating-point rounding error that `double` introduces.

- **a** `double` is a binary floating-point type; values such as 0.1 have no exact binary representation, and the error compounds across millions of sums.
- **c** integers cannot represent fractional currency units at all, unless the application stores minor units and handles scaling itself — a workable pattern, but not what this option claims.
- **d** strings cannot be aggregated or compared numerically by the database.

*Unit: Document Model — Data Types in MongoDB*

### 5 — **b**

Embedding satisfies "data that is accessed together should be stored together." Because the application always reads the address with the customer, embedding returns both in one document read and eliminates the application-side join that referencing would require. For a one-to-one relationship, embedding is the default recommendation.

- **a** describes an advantage of **referencing**, not embedding.
- **c** is backwards: embedding increases document size, so the 16 MB limit is a risk of embedding rather than a guarantee. It is not a concern here only because a single address is bounded.
- **d** is false; fields in referenced documents are indexed normally in their own collection.

*Units: Document Model — Embedding and Referencing; Data Modeling — Embedding Data in Documents*

### 6 — **b**

The Schema Anti-Patterns tab in Data Explorer flags design problems in a collection and provides guidance for resolving each one.

- **a** the Indexes tab lists existing indexes and their usage.
- **c** the Find tab runs queries against documents.
- **d** the Real-Time Performance Panel shows live operations and hardware metrics, and requires M10 or above.

*Unit: Data Modeling — Using Atlas Tools for Schema Help*

### 7 — **b**

`config.set(<option>, <value>)` sets a `mongosh` configuration option. Setting `editor` is a prerequisite for the `edit` command — by default no editor is configured, and `config.get("editor")` returns null.

- **a** `config.get()` retrieves a value and takes only the option name.
- **c** `db.config.set()` is not a valid method.
- **d** `config.apply()` does not exist. (`config.reset(<option>)` restores a default.)

*Unit: The MongoDB Shell — Configuring the MongoDB Shell*

### 8 — **a**

Persistent `mongosh` settings live in a configuration file in **YAML** format, located at `/etc/mongosh.conf` on Linux, with options nested under a top-level `mongosh:` key. `displayBatchSize` controls how many documents each batch of the `it` iterator returns.

- **b** `config.set()` changes the setting for the current user's shell, not for all users on the host, and writes nothing to the server.
- **c** `.mongoshrc.js` holds JavaScript that runs at shell startup, is per-user, and would need `config.set()` syntax rather than an assignment.
- **d** invents a command-line flag; the persistent setting does exist.

*Unit: The MongoDB Shell — Configuring the MongoDB Shell*

### 9 — **b**

`mongos` can target a query when it includes the shard key or a **prefix** of a compound shard key. `storeId` is the prefix of `{ storeId: 1, orderDate: 1 }`, so `mongos` can determine which shards hold the matching chunks and contact only those.

- **a** `orderDate` is the second field and cannot be used without the prefix, so the query is broadcast.
- **c** `customerEmail` is not part of the shard key at all — a scatter-gather query.
- **d** has no filter, so every shard must be consulted.

*Units: Getting Started with Atlas — Architecture Overview; sharding fundamentals (see docs on Sharded Cluster Query Routing)*

### 10 — **b**

Indexes improve read performance **at the cost of write performance**: every insert, update that touches an indexed field, and delete must also update each affected index. Twelve indexes on a high-volume insert collection means twelve additional structures maintained per write. The remedy is to identify unused indexes with `$indexStats` and drop them.

- **a** invents a threshold; indexes are updated incrementally, not rebuilt.
- **c** is false; index structures are separate from the collection's document storage.
- **d** is false; MongoDB does not serialise inserts because indexes exist.

*Unit: Indexes — Using MongoDB Indexes in Collections*

### 11 — **b**

`dropIndexes()` removes all indexes on the collection **except** the default index on `_id`, which MongoDB creates automatically and does not allow you to drop. `dropIndexes()` can also accept an array of index names to remove a specific subset.

- **a** is wrong on both points — the `_id` index is never dropped.
- **c** is wrong; the array argument is optional.
- **d** describes `hideIndex()`, which hides an index from the planner while continuing to maintain it.

*Unit: Indexes — Deleting MongoDB Indexes*

### 12 — **b**

A sparse index contains no entries for documents missing the indexed field. Since this query must return **all** products, using the sparse index would silently drop the ~90% that have no `discount` field. The query planner will not select an index that would produce incomplete results, so it falls back to a collection scan plus an in-memory sort.

- **a** is false; sparse indexes support sorts on the documents they do cover.
- **c** confuses sparse with partial indexes; a partial index has the same completeness constraint.
- **d** invents a shard key requirement.

*Unit: Indexes II — Sparse Indexes*

### 13 — **a**

A clustered index accepts `expireAfterSeconds` at creation time, so the collection gets automatic document expiry without a separate TTL index — one of the operational advantages of clustered collections. (Expiry is driven by the clustered key, so this is useful when `_id` holds a date value.)

- **b** and **c** invent cache and rebuild semantics.
- **d** is false; clustered collections and capped collections are different features.

*Unit: Indexes II — Clustered Indexes*

### 14 — **a**

`show log global` returns recent log messages from the server's **RAM cache**, which is exactly the fallback when the log file is not reachable. `show logs` lists the available filters — `global` and `startupWarnings` among them — and the helper is a wrapper around `db.adminCommand({ getLog: 'global' })`.

- **b** rotates the log file rather than displaying it.
- **c** changes verbosity for future messages.
- **d** lists in-progress operations, not log messages.

*Unit: Logging Basics — MongoDB Logs on Self-Managed Instances*

### 15 — **c**

**Project Data Access Read Only** is the minimum role that permits viewing and downloading process and audit logs. Its API name is `GROUP_DATA_ACCESS_READ_ONLY`. The role also allows viewing databases, collections, indexes, the Performance Advisor, the profiler, and real-time statistics.

- **a** and **d** are organization-level roles and do not grant project data access.
- **b** Project Read Only grants view-only access to the project control panel, but not to data or log files.

*Unit: Logging Basics — MongoDB Logs in Atlas*

### 16 — **b**

**Data durability** is the guarantee that committed data survives a failure. It is what write concern governs: `w: "majority"` means a write is acknowledged only once a majority of members have committed it durably.

- **a** high availability is the guarantee that data remains continuously *accessible* despite an interruption — related, but a different property.
- **c** data partitioning is sharding.
- **d** reduced latency describes response time.

*Unit: Replication — Introduction to Replication*

### 17 — **a**

If secondaries cannot reach the primary for longer than the configured election timeout — **10 seconds** by default — they initiate an election. The primary becoming unavailable is the trigger; how long a member has been primary is irrelevant.

- **b** the oplog window governs how far behind a member can fall before needing an initial sync.
- **c** `wtimeout` bounds how long a write waits for acknowledgement.
- **d** `maxStalenessSeconds` is a client-side read routing constraint.

*Unit: Replication — Automatic Failover and Elections*

### 18 — **a**

`setDefaultRWConcern` is an admin command that sets cluster-wide default read and write concerns, so applications inherit them without specifying anything per operation. An operation that does specify its own concern still overrides the default.

- **b** `rs.reconfig()` changes replica set membership and member settings, not read/write concern defaults.
- **c** is not a valid `setParameter` usage for these settings.
- **d** is false — deployment-wide defaults are exactly what this command provides.

*Unit: Replication — Read and Write Concerns*

### 19 — **b**

`w: 0` requests **no acknowledgement**. The client fires writes and moves on without learning whether any of them succeeded, so errors — including duplicate keys and validation failures — can pass silently. It also removes the natural backpressure that acknowledgement provides, and unacknowledged bulk loads are a documented cause of secondaries falling behind.

- **a** is false; all writes go to the primary regardless of write concern.
- **c** is false; writes are applied immediately, not deferred.
- **d** is false; `w: 0` is valid, just risky.

*Unit: Replication — Read and Write Concerns; The MongoDB Operation Log*

### 20 — **b**

**Opcounters** measures the rate at which each category of operation is performed — insert, query, update, delete, and command — which is precisely what you compare against a latency or resource change to see whether the workload shifted.

- **a** Connections counts open network connections.
- **c** Logical Size reports the uncompressed data size.
- **d** Tickets Available reports remaining storage-engine concurrency slots.

*Unit: Database Metrics & Monitoring — More Metrics*

### 21 — **a**

Configuring alerts is an **Atlas project permission**, not a database privilege. The **Project Owner** role is required to create alert conditions for a specific host. `clusterAdmin` is a MongoDB database role that grants cluster administration inside the deployment and confers nothing in the Atlas control plane — the distinction the question is testing.

- **b** adds another database role, which does not help.
- **c** is false; alert conditions can be configured on any tier, though shared tiers only ever trigger alerts for the metrics they collect.
- **d** is false; alerts are configurable in both the UI and the CLI.

*Unit: Database Metrics & Monitoring — Configure Alerts*

### 22 — **a**

A snapshot volume is a block device. To read its files — which is what `tar` does — the volume must first be **mounted**, conventionally read-only and with `nouuid` on XFS so the duplicate filesystem UUID does not conflict with the live volume. Only then can `tar -czvf` archive the directory contents.

- **b** `bsondump` operates on `.bson` dump files, not block devices.
- **c** is unnecessary; the point of snapshotting is that `mongod` keeps running. The brief `fsyncLock()`/`fsyncUnlock()` window already guaranteed a consistent snapshot.
- **d** would leave the deployment unable to accept writes for the entire archive operation.

*Unit: Self-Managed Backup & Recovery — Filesystem Archives on a MongoDB Server*

### 23 — **b**

`--noIndexRestore` skips index creation during the restore, reducing CPU, memory, and I/O load and shortening the restore itself. The indexes are then built separately when there is capacity for it.

- **a** `--drop` removes existing collections before restoring — useful, but unrelated to index timing.
- **c** `--oplogReplay` applies oplog entries captured by `mongodump --oplog`.
- **d** `--nsExclude` filters which namespaces are restored.

*Units: Self-Managed Backup & Recovery — Restoring a Deployment; DBA Tools — Restore Tools*

### 24 — **c**

**Auditing** is the practice of monitoring and recording activity — authentication events, authorization changes, and configuration changes — so it can be reviewed after the fact. That retrospective record is what an investigation needs.

- **a** authentication verifies identity at connection time but keeps no investigative trail by itself.
- **b** and **d** determine what a user is permitted to do; they do not record what was done.

*Unit: Self-Managed Security — Introduction to Security*

### 25 — **a** and **b** *(2 correct)*

Auditing supports post-incident analysis — for example, establishing which users were authenticated at the time — and is frequently mandated by regulation, which is why many organisations enable it despite its cost.

- **c** is backwards: recording audit events **degrades** performance, which is why the recommended practice is to audit selectively.
- **d** contradicts that guidance. Auditing every read and write from every user is possible but expensive; audit the operations that matter.

*Unit: Self-Managed Security — Introduction to Security; Security Auditing in MongoDB*

### 26 — **b**

MongoDB will use a self-signed certificate, so encryption still happens — but a self-signed certificate cannot be validated against a trusted certificate authority, so clients and drivers have no way to confirm they are talking to the intended server. That is precisely the gap a man-in-the-middle exploits. For production, obtain a CA-issued certificate for each server.

- **a** overstates the restriction.
- **c** is wrong; the traffic is encrypted, the *identity* is unverified.
- **d** ignores identity validation, which is the entire point of a CA.

*Unit: Self-Managed Security — Enabling Network Encryption*

### 27 — **a**

**Automatic encryption** is the MongoDB Enterprise mechanism for setting up CSFLE. An encryption schema declares which fields are protected and how, and the driver then performs encrypted reads and writes transparently — no per-field logic in application code. The manual alternative, explicit encryption, requires that code.

- **b** the Encrypted Storage Engine encrypts entire data files on disk and cannot target named fields; the server would also hold plaintext in memory.
- **c** TLS protects data in transit only.
- **d** `partialFilterExpression` is an index option with no relationship to encryption.

*Unit: Self-Managed Security — Encryption in Self-Managed MongoDB Deployments*

### 28 — **a**

`mongostat` takes the polling interval as a positional argument **in seconds**, and `-o` specifies which fields or expressions to display, each optionally renamed with `=`.

- **b** passes 5000, which `mongostat` reads as 5,000 seconds — roughly 83 minutes between samples.
- **c** uses `-stats`, which is not a valid option.
- **d** `mongostatus` is not a command.

*Unit: DBA Tools — Diagnostic Tools: mongostat*

### 29 — **b**

`bsondump --type=debug` reports the internal structure of each document, including the BSON data types and sizes — the information needed to work out why a restore is rejecting a file.

- **a** `--pretty` produces readable indented JSON, which shows values but not types and sizes.
- **c** `mongoexport` reads from a live deployment and emits JSON or CSV; it cannot read a `.bson` file, and `bson` is not a valid `--type`.
- **d** `mongofiles` operates on GridFS, not on dump files.

*Unit: DBA Tools — Diagnostic Tools: bsondump*

### 30 — **b**

`mongofiles list <prefix>` lists GridFS files whose names **begin with** the given string, passed directly as an argument with no flag.

- **a** `search` lists files whose names **contain** the string — broader than required, and the distinction the question tests.
- **c** invents a `--files` flag.
- **d** `get` retrieves a file rather than listing.

*Unit: DBA Tools — MongoDB as a Filesystem (mongofiles)*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| CRUD operations & operators | 1, 2, 3 | 3 |
| Document model & BSON types | 4 | 1 |
| Data modeling & Atlas schema tooling | 5, 6 | 2 |
| The MongoDB Shell (mongosh) | 7, 8 | 2 |
| Sharded cluster query routing | 9 | 1 |
| Indexing | 10, 11, 12, 13 | 4 |
| Logging | 14, 15 | 2 |
| Replication & read/write concerns | 16, 17, 18, 19 | 4 |
| Metrics, monitoring & alerts | 20, 21 | 2 |
| Backup & recovery | 22, 23 | 2 |
| Security: auditing & encryption | 24, 25, 26, 27 | 4 |
| DBA tools | 28, 29, 30 | 3 |

**Scoring guide:** 27+ = strong; 22–26 = solid, review the missed areas; below 22 = revisit those units.

## Traps Worth Remembering From This Set

- **`$set` on an array field destroys the array.** Appending requires `$push` (Q2). The same shape of mistake as replacing a subdocument instead of using dot notation.
- **`$nin` matches missing fields too.** A query excluding two values will also return documents where the field is absent (Q1).
- **Atlas control-plane roles are not MongoDB database roles.** `clusterAdmin` grants cluster administration inside the deployment; configuring alerts needs Project Owner in Atlas (Q21). Set 3 tested the same boundary from the logs side.
- **The planner refuses any index that would return incomplete results.** This applies to sparse indexes over documents missing the field, and to partial indexes whose filter the query does not guarantee (Q12).
- **`mongostat` and `mongotop` take their interval in seconds**, as a positional argument — not milliseconds, not a flag (Q28).
- **Two different "encryption at rest" answers exist.** The Encrypted Storage Engine covers whole data files and is Enterprise-only; CSFLE with automatic encryption covers named fields and keeps the server from ever seeing plaintext (Q27).

## Cumulative Coverage — All Four Sets

You now have 120 questions.

| Topic area | Set 1 | Set 2 | Set 3 | Set 4 | Total |
|---|---|---|---|---|---|
| Indexing & explain | 9 | 7 | 4 | 4 | 24 |
| Security | 5 | 5 | — | 4 | 14 |
| CRUD & query results | 3 | 4 | 4 | 3 | 14 |
| Replication | 4 | 3 | 3 | 4 | 14 |
| Data modeling & document model | 2 | 1 | 4 | 3 | 10 |
| Metrics & monitoring | 1 | 2 | 4 | 2 | 9 |
| Backup & recovery | 2 | 2 | 2 | 2 | 8 |
| DBA tools | 1 | 1 | 2 | 3 | 7 |
| Logging | 1 | 2 | 2 | 2 | 7 |
| Shell & connecting | — | — | 4 | 2 | 6 |
| Upgrades & maintenance | 1 | 2 | 1 | — | 4 |
| Sharded clusters | 1 | 1 | — | 1 | 3 |

The syllabus in your uploaded material is now close to exhausted at this difficulty. What remains largely untested is narrow: `mongoexport --query` filters, `atlas metrics processes` and the alert CLI commands, `db.version()` and the feature-compatibility-version query, `rs.printSecondaryReplicationInfo()`, Atlas cluster tiers and project organisation, and the Real-Time Performance Panel's contents. A fifth themed set would start repeating concepts with fresh scenarios rather than covering new ground.

Two things likely to be worth more than Set 5:

1. **A timed 60-question mixed mock** drawn from all four sets plus new items, topics shuffled and unlabelled, answers withheld to the end. Themed sets prime you for the topic; the real exam does not.
2. **A targeted drill on your misses.** Collect every question you answered wrong across the four sets into a single pass, taken at least a day later. Anything missed twice is a real gap rather than a misread.