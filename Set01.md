# MongoDB Associate DBA — Practice Set 1

**30 exam-style questions** modelled on the official practice questions: short scenario, one focused question, four options, plausible distractors. Most are *select one*; two are *select all that apply* (marked).

**How to use it:** answer all 30 first without scrolling to the key. Target 75%+ before moving on. The answer key explains the correct option *and* why each distractor fails, plus the unit each question maps to so you can go back to the source material.

---

## Questions

### Question 1

A logistics platform stores shipment records in a `shipments` collection. Each document contains a `checkpoints` array of subdocuments with `city` and `delayHours` fields. A MongoDB database administrator needs to return only the shipments that have at least one single checkpoint that is both located in "Pune" and has a `delayHours` value greater than 6.

Which query should the administrator use?

- **a.** `db.shipments.find({ "checkpoints.city": "Pune", "checkpoints.delayHours": { $gt: 6 } })`
- **b.** `db.shipments.find({ checkpoints: { $elemMatch: { city: "Pune", delayHours: { $gt: 6 } } } })`
- **c.** `db.shipments.find({ checkpoints: { city: "Pune", delayHours: { $gt: 6 } } })`
- **d.** `db.shipments.find({ $elemMatch: { checkpoints: { city: "Pune", delayHours: { $gt: 6 } } } })`

---

### Question 2

A retail application stores product records in a `products` collection. A developer needs to set `clearance: true` on every document where `stock` is less than 5, and runs the following:

```js
db.products.updateMany({ stock: { $lt: 5 } }, { clearance: true })
```

The command fails. What is the reason for the failure?

- **a.** `updateMany()` accepts only one document argument; the filter and update must be combined into a single document.
- **b.** The update document must use an update operator such as `$set`; a plain document is not a valid update specification.
- **c.** `updateMany()` cannot add a field that does not already exist in the matched documents.
- **d.** The filter must use `$lte` rather than `$lt` when the compared field may be absent from some documents.

---

### Question 3

A booking service maintains a `counters` collection. Before the following operation runs, the target document is `{ _id: "invoiceSeq", value: 400 }`.

```js
db.counters.findAndModify({
  query: { _id: "invoiceSeq" },
  update: { $inc: { value: 1 } }
})
```

What does this operation return?

- **a.** The document as it existed before the increment, with `value` set to 400.
- **b.** The document after the increment, with `value` set to 401.
- **c.** An acknowledgement document containing `matchedCount` and `modifiedCount`.
- **d.** `null`, because `findAndModify()` returns a document only when `upsert` is enabled.

---

### Question 4

A MongoDB database administrator receives reports that a self-managed deployment slows down every evening. The administrator needs to determine which specific collections the `mongod` instance is spending the most time reading from and writing to, sampled at a regular interval.

Which MongoDB Database Tool should the administrator use?

- **a.** `mongostat`
- **b.** `bsondump`
- **c.** `mongotop`
- **d.** `mongofiles`

---

### Question 5

A fleet-management application stores one document per vehicle in a `vehicles` collection. Every completed trip is appended to a `trips` array inside the vehicle document. Each vehicle completes several hundred trips per month, and trips are never removed.

What problem should a database administrator anticipate with this schema?

- **a.** The array will exceed the 100-element limit that MongoDB enforces on embedded arrays.
- **b.** Vehicle documents will grow without bound, degrading write performance and eventually approaching the 16 MB BSON document limit.
- **c.** Trip subdocuments cannot be indexed once the array contains more than one shape of subdocument.
- **d.** MongoDB will automatically shard the `vehicles` collection once its documents exceed the average size threshold.

---

### Question 6

A publishing application stores articles in an `articles` collection. Each article is tagged with up to five topics drawn from a controlled list of roughly 60 topics. The application must display the topics alongside each article, and must also be able to list every article for a given topic.

Which modeling approach best meets these requirements?

- **a.** Embed the topic names in an array inside each article document.
- **b.** Embed the full article documents in an array inside each topic document.
- **c.** Store an array of article `_id` values inside each topic document, and store no topic data in the article.
- **d.** Keep topics only in a separate `topics` collection and perform an application-side join on every article read.

---

### Question 7

A `support_tickets` collection is queried as follows:

```js
db.support_tickets.find({
  queue: "billing",
  createdAt: { $gte: ISODate("2026-06-01T00:00:00Z") }
}).sort({ priority: -1 })
```

Which index should a database administrator create to support this query most efficiently?

- **a.** `db.support_tickets.createIndex({ createdAt: 1, queue: 1, priority: -1 })`
- **b.** `db.support_tickets.createIndex({ priority: -1, queue: 1, createdAt: 1 })`
- **c.** `db.support_tickets.createIndex({ queue: 1, priority: -1, createdAt: 1 })`
- **d.** `db.support_tickets.createIndex({ queue: 1, createdAt: 1, priority: -1 })`

---

### Question 8 *(Select all that apply)*

An `events` collection has the compound index `{ region: 1, occurredAt: -1 }`. A reporting query filters on a **range** of `region` values and then sorts the results.

Which sort specifications can be satisfied by this index without a blocking in-memory `SORT` stage?

- **a.** `{ region: 1, occurredAt: -1 }`
- **b.** `{ region: -1, occurredAt: 1 }`
- **c.** `{ region: 1, occurredAt: 1 }`
- **d.** `{ occurredAt: -1, region: 1 }`

---

### Question 9

A database administrator reviews the following abbreviated `explain("executionStats")` output for a query on an `orders` collection:

```js
{
  executionStats: {
    nReturned: 12000,
    executionTimeMillis: 812,
    totalKeysExamined: 12000,
    totalDocsExamined: 12000,
    executionStages: {
      stage: "SORT",
      inputStage: { stage: "FETCH", inputStage: { stage: "IXSCAN" } }
    }
  }
}
```

Why is this query slow even though it examines only as many keys and documents as it returns?

- **a.** The `FETCH` stage indicates that the index is corrupt and must be rebuilt.
- **b.** `totalKeysExamined` equals `nReturned`, which forces the planner to re-evaluate every candidate plan on each execution.
- **c.** The `SORT` stage shows that MongoDB is performing a blocking in-memory sort, because the index does not satisfy the requested sort order.
- **d.** `executionTimeMillis` includes client network round-trip time, so it is not a reliable indicator of server-side work.

---

### Question 10

An `invoices` collection has the following index:

```js
db.invoices.createIndex(
  { customerId: 1 },
  { partialFilterExpression: { void: false } }
)
```

A reporting query runs `db.invoices.find({ customerId: 4501 })`, and the winning plan shows a `COLLSCAN`.

What is the reason the partial index was not used?

- **a.** Partial indexes can be used only by aggregation pipelines, not by `find()` operations.
- **b.** The query contains no predicate guaranteeing that its result set is a subset of the documents contained in the partial index.
- **c.** Partial indexes are ignored whenever the queried field is not also the first field of the `partialFilterExpression`.
- **d.** The index must also be created with `sparse: true` before the query planner will consider it.

---

### Question 11

A `profiles` collection contains exactly these three documents:

```js
{ _id: 1, user: "asha", phone: "555-0101" }
{ _id: 2, user: "brij", phone: null }
{ _id: 3, user: "chen" }
```

An administrator then creates the index `db.profiles.createIndex({ phone: 1 }, { sparse: true })`.

Which documents have an entry in this index?

- **a.** Documents 1 and 2
- **b.** Document 1 only
- **c.** Documents 1, 2, and 3
- **d.** Documents 2 and 3

---

### Question 12

An e-commerce `catalog` collection stores vendor-supplied attributes under a `specs` subdocument. Different product categories use completely different `specs` sub-fields, and new sub-fields appear each time a vendor is onboarded. Queries filter on arbitrary `specs` sub-fields that are not known in advance.

Which index best supports these queries?

- **a.** `db.catalog.createIndex({ specs: 1 })`
- **b.** `db.catalog.createIndex({ "specs.$**": 1 })`
- **c.** `db.catalog.createIndex({ specs: 1 }, { sparse: true })`
- **d.** `db.catalog.createIndex({ "specs.color": 1, "specs.size": 1, "specs.material": 1 })`

---

### Question 13

A database administrator suspects an index on a large `sessions` collection is no longer used. Before dropping it, the administrator wants to observe the effect of its absence on production query performance, and be able to reverse the change immediately if performance degrades.

Which action should the administrator take?

- **a.** Run `db.sessions.dropIndex()`, then re-create the index if performance degrades.
- **b.** Run `db.sessions.hideIndex()` so the query planner ignores the index while MongoDB continues to maintain it.
- **c.** Run `db.sessions.dropIndexes()` and rely on Performance Advisor to recommend the index again if it is needed.
- **d.** Set the index's `expireAfterSeconds` option to `0` so the planner stops selecting it.

---

### Question 14

A team wants a `telemetry` collection in which the documents are physically stored in order of their `_id` values, so that range scans on `_id` avoid a separate lookup to fetch each document.

When can this clustered index be created?

- **a.** At any time, by running `db.telemetry.createIndex()` with the `clustered: true` option.
- **b.** Only when the collection is created, by using `db.createCollection()`.
- **c.** Only after the collection contains at least one document, so that MongoDB can determine the key ordering.
- **d.** Only on collections that already have a secondary index on the `_id` field.

---

### Question 15

A production deployment has accumulated many indexes over several years. A database administrator needs to identify the indexes that queries no longer use, so they can be dropped to reduce write amplification.

Which approach should the administrator use?

- **a.** Run `db.collection.aggregate([{ $indexStats: {} }])` and review the `accesses.ops` value for each index.
- **b.** Run `db.collection.getIndexes()` and drop every index that is not on the `_id` field.
- **c.** Run `db.collection.validate()` and drop any index reported with zero keys.
- **d.** Run `db.collection.stats()` and compare `totalIndexSize` with `storageSize`.

---

### Question 16

An administrator of a self-managed deployment wants operations slower than 20 milliseconds written to the `mongod` diagnostic log, but does **not** want the profiler writing entries into the `system.profile` collection.

Which command achieves this?

- **a.** `db.setProfilingLevel(1, { slowms: 20 })`
- **b.** `db.setProfilingLevel(2, { slowms: 20 })`
- **c.** `db.setProfilingLevel(0, { slowms: 20 })`
- **d.** `db.setLogLevel(0, { slowms: 20 })`

---

### Question 17

A five-member replica set spans two data centers. Two members reside in a disaster-recovery site. Those two members must never serve as primary during normal operations, but must still replicate data and participate in elections.

How should the administrator configure those two members?

- **a.** Set `votes: 0` on both members.
- **b.** Set `priority: 0` on both members.
- **c.** Reconfigure both members as arbiters.
- **d.** Set `hidden: true` and `buildIndexes: false` on both members.

---

### Question 18

A three-member replica set consists of one primary and two data-bearing secondaries. One secondary is taken offline for hardware maintenance. The application writes with `{ w: "majority" }`.

What happens to the application's writes while that secondary is offline?

- **a.** Writes fail immediately, because `"majority"` requires acknowledgement from all three members.
- **b.** Writes succeed, because the primary and the remaining secondary satisfy the calculated majority of two.
- **c.** Writes succeed, but are rolled back when the offline member rejoins the replica set.
- **d.** Writes are queued on the primary until the offline member returns, and are acknowledged at that point.

---

### Question 19

An analytics team must run reports against a replica set. The reports tolerate slightly stale data, but must never read from a secondary that is more than two minutes behind the primary.

Which connection string options should the team use?

- **a.** `?readConcern=majority&maxStalenessSeconds=120`
- **b.** `?readPreference=secondary&maxStalenessSeconds=120`
- **c.** `?readPreference=nearest&wtimeout=120000`
- **d.** `?readPreference=secondaryPreferred&readConcern=linearizable`

---

### Question 20

A database administrator must patch the operating system on each member of a replica set, one member at a time. Each host will be offline for a period while it is patched. The administrator needs to know how long a single member can stay offline and still catch up by replicating from the primary, rather than requiring a full initial sync.

Which command provides this information?

- **a.** `rs.status()`
- **b.** `rs.conf()`
- **c.** `rs.printReplicationInfo()`
- **d.** `db.serverStatus().opcounters`

---

### Question 21 *(Select all that apply)*

A replica set currently runs MongoDB 4.4. The administrator plans to move it to 6.0.

Which statements about this upgrade are true?

- **a.** The deployment must be upgraded to 5.0 before it can be upgraded to 6.0.
- **b.** All members can be shut down at the same time to shorten the total maintenance window.
- **c.** Before upgrading, the administrator should confirm that no member is in the `ROLLBACK` or `RECOVERING` state.
- **d.** `setFeatureCompatibilityVersion` should be set to `"6.0"` only after every member has been upgraded.

---

### Question 22

A user is created with only the `userAdminAnyDatabase` role on the `admin` database. The user authenticates, then runs `db.accounts.find()` against the `sample_analytics` database. The command fails with an authorization error.

Why does the command fail?

- **a.** `userAdminAnyDatabase` grants user and role administration privileges, but grants no privileges to read collection data.
- **b.** `userAdminAnyDatabase` grants read access only to the `local` and `config` databases.
- **c.** The user must authenticate against `sample_analytics` rather than `admin` before any assigned role takes effect.
- **d.** Read operations require the `clusterMonitor` role in addition to any database-level role.

---

### Question 23

A monitoring agent must collect server status, replica set status, and other diagnostic metrics from a self-managed deployment. Security policy requires the principle of least privilege: the agent must not be able to read application data or change the cluster configuration.

Which built-in role should be assigned to the agent's user?

- **a.** `clusterAdmin`
- **b.** `clusterMonitor`
- **c.** `dbAdminAnyDatabase`
- **d.** `readAnyDatabase`

---

### Question 24

A compliance requirement states that authentication attempts and privilege changes on a self-managed MongoDB Enterprise deployment must be recorded to a destination that is separate from the operational server log.

Which configuration file settings should the administrator use?

- **a.** `systemLog.destination` and `systemLog.path`
- **b.** `security.authorization` and `security.keyFile`
- **c.** `auditLog.destination` and `auditLog.path`
- **d.** `operationProfiling.mode` and `operationProfiling.slowOpThresholdMs`

---

### Question 25

A healthcare application must ensure that patient diagnosis values are unreadable to anyone with access to the database server itself — including database administrators, and anyone who obtains a copy of the data files or a backup.

Which MongoDB encryption capability meets this requirement?

- **a.** TLS/SSL transport encryption
- **b.** The Encrypted Storage Engine (encryption at rest)
- **c.** Client-Side Field Level Encryption (CSFLE)
- **d.** A keyfile configured for local key management

---

### Question 26

An administrator is enabling TLS on an existing three-member replica set that currently accepts unencrypted connections. Replication traffic between members must be encrypted immediately, but application servers will be reconfigured to use TLS gradually over the following week and must keep connecting in the meantime.

Which `net.tls.mode` value should be set during the transition?

- **a.** `requireTLS`
- **b.** `preferTLS`
- **c.** `allowTLS`
- **d.** `disabled`

---

### Question 27

A database administrator must produce a nightly backup of a self-managed replica set that can be restored to a consistent point in time, even though writes continue for the duration of the backup.

Which combination should the administrator use?

- **a.** `mongodump --oplog` for the backup, and `mongorestore --oplogReplay` for the restore.
- **b.** `mongodump --gzip --archive` for the backup, and `mongorestore --drop --noIndexRestore` for the restore.
- **c.** `mongoexport --jsonFormat canonical` for the backup, and `mongoimport --mode=upsert` for the restore.
- **d.** `mongodump --readPreference=secondary` for the backup, and `mongorestore --writeConcern majority` for the restore.

---

### Question 28

A database administrator is about to take an LVM filesystem snapshot of the data volume on a replica set secondary.

Which sequence ensures the snapshot is a valid, complete copy of the data files?

- **a.** Run `db.fsyncLock()`, take the snapshot, then run `db.fsyncUnlock()`.
- **b.** Run `rs.stepDown()`, take the snapshot, then restart `mongod`.
- **c.** Take the snapshot, then run `db.adminCommand({ logRotate: 1 })` to flush pending writes.
- **d.** Run `db.fsyncUnlock()`, take the snapshot, then run `db.fsyncLock()`.

---

### Question 29

During a bulk data load on an M30 cluster, a database administrator observes that the Tickets Available metric for reads and writes is falling steadily toward zero while operation latency climbs.

What does this metric indicate?

- **a.** The number of concurrent read and write operations the storage engine can still accept; as it approaches zero, operations must queue.
- **b.** The number of client connections remaining before the cluster's connection limit is reached.
- **c.** The number of oplog entries that the secondaries have not yet applied.
- **d.** The number of documents scanned per document returned by the current workload.

---

### Question 30

An application queries a sharded `orders` collection using a filter that does not include the shard key. A database administrator explains that this will run as a scatter-gather (broadcast) operation.

What happens when the query executes?

- **a.** `mongos` routes the query to the config servers, which locate the matching documents across the shards.
- **b.** `mongos` routes the query to the primary shard, which then forwards it to the remaining shards.
- **c.** `mongos` sends the query to every shard in the cluster, then merges the results before returning them to the client.
- **d.** `mongos` rejects the query, because queries against a sharded collection must include the shard key.

---

## Answer Key

### 1 — **b**

`$elemMatch` requires that a **single** array element satisfy all of the enclosed criteria, which is exactly the stated requirement.

- **a** applies each condition to the array independently, so a shipment matches if *one* element is in Pune and a *different* element has `delayHours > 6`.
- **c** attempts an exact subdocument match; it matches only elements containing precisely those fields in that order, and `{ $gt: 6 }` cannot be used that way.
- **d** is invalid syntax — `$elemMatch` must be applied to an array field, not used at the top level of the query.

*Unit: CRUD — Querying on Array Elements*

### 2 — **b**

`updateMany()` takes a filter, an **update document built from update operators**, and an optional options document. Passing a bare document raises an error stating that the update must contain atomic operators. The correct form is `{ $set: { clearance: true } }`.

- **a** misstates the signature; filter and update are separate arguments.
- **c** is false — `$set` creates the field if it is absent.
- **d** is irrelevant; `$lt` is correct here, and documents missing `stock` simply do not match.

*Unit: CRUD — Updating Documents with updateMany()*

### 3 — **a**

`findAndModify()` returns the **pre-modification** document by default, because `new` defaults to `false`. To get the document as it exists after the update, `new: true` must be passed explicitly.

- **b** would be correct only with `new: true`.
- **c** describes what `updateOne()`/`updateMany()` return, not `findAndModify()`.
- **d** is wrong — `upsert` controls insertion of a missing document, not whether a document is returned.

*Unit: CRUD — findAndModify()*

### 4 — **c**

`mongotop` tracks how much time a `mongod` instance spends reading and writing, reported **per collection**, and polls at a specified interval.

- **a** `mongostat` gives a real-time, instance-wide view (opcounters, queues, network) — not per-collection read/write time.
- **b** `bsondump` converts BSON files to human-readable JSON.
- **d** `mongofiles` manipulates GridFS files.

*Unit: DBA Tools — Diagnostic Tools: mongotop*

### 5 — **b**

This is the classic **unbounded document** anti-pattern. Every append rewrites the document in storage, so write cost grows with document size; reads must pull the entire document into memory; and the document eventually risks the 16 MB BSON limit. The fix is to move trips into their own collection and reference the vehicle.

- **a** invents a limit — MongoDB imposes no 100-element array cap.
- **c** is false; multikey indexes handle arrays of varying subdocument shapes.
- **d** is false; sharding is never triggered automatically by document size.

*Unit: Data Modeling — Scaling a Data Model*

### 6 — **a**

The array is bounded and small (at most five topics), so embedding satisfies "data accessed together is stored together." Querying all articles for a topic is then a single query against an indexable array field, with no second round trip.

- **b** would duplicate whole articles and produce enormous, unbounded topic documents.
- **c** creates unbounded arrays inside topic documents and fails the requirement to display topics with the article.
- **d** forces an application-side join on every read with no offsetting benefit at this scale.

*Unit: Document Model — Embedding and Referencing*

### 7 — **c**

Apply the **ESR rule**: Equality (`queue`), Sort (`priority`), Range (`createdAt`). Placing the equality field first narrows the scan; placing the sort field next lets the index return documents already ordered, avoiding a blocking sort; the range field goes last.

- **a** and **d** place the range field before the sort field, so the sort ordering is broken and an in-memory sort is required.
- **b** leads with the sort field, which prevents the index from filtering efficiently first.

*Unit: Indexes II — Optimized Compound Indexes*

### 8 — **a** and **b** *(2 correct)*

An index can serve a sort when the sort pattern matches the index key pattern **or is its exact inverse** — the index is simply scanned backwards. `{ region: 1, occurredAt: -1 }` matches; `{ region: -1, occurredAt: 1 }` inverts every key.

- **c** inverts only one of the two keys, which no single scan direction can produce, so a blocking `SORT` is required.
- **d** reverses the key order; the index prefix is `region`, so it cannot deliver `occurredAt`-first ordering.

*Unit: Indexes — Working with Compound Indexes; Indexes II — Index Usage via Explain*

### 9 — **c**

The `SORT` stage in `executionStages` means a **blocking in-memory sort** occurred: MongoDB fetched all 12,000 matching documents, then sorted them in memory before returning any. The key-to-document-to-result ratio is ideal (1:1:1), so the filter is well indexed — the sort is not. Rebuild the index following ESR so the sort field is positioned to be served by the index.

- **a** is wrong; `FETCH` simply means documents were read from the collection, which is normal for a non-covered query.
- **b** describes nothing MongoDB does; plan caching works the other way.
- **d** is wrong; `executionTimeMillis` measures server-side execution.

*Unit: Indexes II — Index Usage Details via Explain*

### 10 — **b**

The query planner uses a partial index only when the query is guaranteed to return a subset of the indexed documents. The query filters solely on `customerId`, so its results could include documents where `void` is `true` — those have no index entries, and using the index would return incomplete results. Adding `void: false` to the query makes the index eligible.

- **a** is false; partial indexes serve `find()` operations.
- **c** invents a rule; the indexed field and the filter field are routinely different.
- **d** is false; `sparse` and `partialFilterExpression` are separate options and cannot be combined.

*Unit: Indexes II — Partial Indexes*

### 11 — **a**

A sparse index creates entries only for documents in which the indexed field **exists**. Document 1 has a value and document 2 has an explicit `null` — in both cases the field is present, so both are indexed. Document 3 omits `phone` entirely and is skipped.

- **b** wrongly assumes `null` is excluded — the common trap on this topic.
- **c** would be true of an ordinary (non-sparse) index, which stores `null` for missing fields.
- **d** inverts the rule.

*Unit: Indexes II — Sparse Indexes*

### 12 — **b**

A **wildcard index** on `specs.$**` indexes every sub-field beneath `specs`, including sub-fields that do not exist yet, which is precisely the "unknown or arbitrary field" case.

- **a** indexes `specs` as a whole object; it supports matching on the entire subdocument, not on individual sub-fields.
- **c** adds nothing — sparseness is unrelated to unknown field names.
- **d** covers only three named sub-fields and would need rebuilding for each new vendor.

*Unit: Indexes II — Wildcard Indexes*

### 13 — **b**

`hideIndex()` hides an index from the query planner while MongoDB keeps updating it. Query performance reflects the index being gone, but unhiding restores it instantly — no rebuild needed. This is the intended way to test an index removal.

- **a** and **c** destroy the index; re-creating it on a large collection is expensive and slow.
- **d** is a TTL setting and does not control planner visibility.

*Unit: Indexes — Deleting MongoDB Indexes*

### 14 — **b**

A clustered index can only be defined **at collection creation time**, via `db.createCollection()` with the `clusteredIndex` option. An existing non-clustered collection cannot be converted.

- **a** is wrong; `createIndex()` creates secondary indexes only.
- **c** and **d** invent prerequisites that do not exist.

*Unit: Indexes II — Clustered Indexes*

### 15 — **a**

`$indexStats` returns one document per index with usage statistics, including `accesses.ops` (how many operations have used the index) and `accesses.since`. Indexes with an operation count of zero over a representative period are candidates for removal.

- **b** lists index definitions with no usage data — dropping on that basis is guesswork.
- **c** `validate()` checks structural integrity, not usage.
- **d** reports sizes, not usage.

*Unit: Indexes II — How to Monitor Indexes*

### 16 — **c**

Profiling level `0` disables the profiler, but `db.setProfilingLevel()` still sets the `slowms` threshold that governs which operations are written to the **diagnostic log**. So level `0` with `slowms: 20` logs slow operations without populating `system.profile`.

- **a** (level 1) writes slow operations into `system.profile`.
- **b** (level 2) profiles *every* operation — the heaviest option.
- **d** `setLogLevel()` adjusts log verbosity and does not accept `slowms`.

*Unit: Logging Basics — Server Log Customizations; Indexes II — How to Monitor Indexes*

### 17 — **b**

A member with `priority: 0` can never be elected primary, yet it still replicates data and — because its votes are untouched — still participates in elections. That is exactly the disaster-recovery member pattern.

- **a** removing votes reduces election participation, which the requirement forbids.
- **c** arbiters hold no data at all.
- **d** hidden members are invisible to client read routing and `buildIndexes: false` cripples them for failover use; neither addresses electability.

*Unit: Replication — Automatic Failover and Elections; Configuring a Replica Set*

### 18 — **b**

`"majority"` is a **calculated** value: the majority of data-bearing voting members. For three such members, the majority is two. The primary plus one healthy secondary satisfies it, so writes continue to succeed. (A second failure would stall majority writes until `wtimeout` elapses.)

- **a** confuses `"majority"` with `w: 3`.
- **c** misdescribes rollback, which applies to writes that never reached a majority before a failover.
- **d** is not how write concern behaves.

*Unit: Replication — Read and Write Concerns*

### 19 — **b**

`readPreference=secondary` directs reads to secondaries only; `maxStalenessSeconds=120` instructs the driver to exclude any secondary estimated to be more than 120 seconds behind. Note the floor: `maxStalenessSeconds` must be at least **90** — smaller values are rejected.

- **a** omits a read preference, so reads still go to the primary; read concern governs durability, not routing.
- **c** `nearest` may select the primary, and `wtimeout` is a write concern option.
- **d** `secondaryPreferred` falls back to the primary and specifies no staleness bound.

*Unit: Replication — Read and Write Concerns*

### 20 — **c**

`rs.printReplicationInfo()` reports the oplog's configured and actual size plus the **oplog window** (`log length start to end`) — the span of time the oplog covers. A member offline longer than that window falls off the end of the oplog and requires a full initial sync, so the window is the effective budget for rolling maintenance.

- **a** shows member states, health, and optimes, but not the oplog window.
- **b** returns replica set configuration.
- **d** returns operation counters.

*Units: Replication — The MongoDB Operation Log; Upgrades & Maintenance — Zero Downtime Maintenance*

### 21 — **a**, **c**, and **d** *(3 correct)*

Major versions must be installed in **consecutive order** (4.4 → 5.0 → 6.0). Before starting, verify member states — upgrading a member in `ROLLBACK` or `RECOVERING` risks data loss. And `setFeatureCompatibilityVersion` is set last, once every member runs the new binary, because it enables persisted features incompatible with earlier versions.

- **b** is wrong and is the dangerous distractor: exactly **one** member is taken down at a time, gracefully shut down, upgraded, and returned to the set — that is what keeps downtime near zero.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 22 — **a**

`userAdminAnyDatabase` is a user-administration role: it can create and modify users and roles, but it confers **no data read privileges**. Reading `sample_analytics` requires a role such as `read` on that database.

- **b** inverts the actual exclusion — the role's user-admin privileges apply to all databases *except* `local` and `config`.
- **c** is wrong; `admin` is the correct `authSource` for a user created there.
- **d** is wrong; `clusterMonitor` covers diagnostics, not collection reads.

*Unit: Self-Managed Security — Enabling Authentication; Establishing Authorization*

### 23 — **b**

`clusterMonitor` grants read-only access to monitoring commands and cluster metadata — the minimum needed by a monitoring agent, and the role used for the Percona MongoDB Exporter.

- **a** `clusterAdmin` adds replication and sharding administration — far beyond the need.
- **c** `dbAdminAnyDatabase` permits schema and index changes on every database.
- **d** `readAnyDatabase` grants exactly what the policy forbids: read access to application data.

*Units: Self-Managed Security — Authorization; Metrics & Monitoring — Self-Managed Monitoring*

### 24 — **c**

Auditing is configured under `auditLog`: `destination` (`file`, `syslog`, or `console`), `format` (`JSON` or `BSON`), and `path` for the output file. This captures events such as authentication and authorization changes, separately from the operational log.

- **a** configures the ordinary server log (`mongod.log`), which is not an audit trail.
- **b** enables access control — a prerequisite, but it records nothing.
- **d** configures the profiler for slow operations.

*Unit: Self-Managed Security — Security Auditing in MongoDB*

### 25 — **c**

**CSFLE** encrypts the field inside the client application before it crosses the network, so the server stores and returns ciphertext. Neither a DBA on the server, nor a stolen data file, nor a backup yields readable diagnosis values.

- **a** protects data in transit only; the server still receives plaintext.
- **b** protects the files on disk, but the server decrypts data for use — so an authorized insider still reads plaintext. This is the documented insider-threat limitation of encryption at rest.
- **d** is a key-management mechanism for the encrypted storage engine, not a separate protection.

*Unit: Self-Managed Security — Introduction to Encryption Concepts; Encryption in Self-Managed Deployments*

### 26 — **b**

`preferTLS` encrypts connections **between members** while still accepting both TLS and non-TLS incoming client connections — precisely the rolling-migration mode. Once every application is converted, tighten it to `requireTLS`.

- **a** immediately rejects the not-yet-converted application servers.
- **c** `allowTLS` also accepts both kinds of client connections, but does **not** use TLS for intra-cluster traffic, so it fails the replication-encryption requirement.
- **d** disables TLS entirely.

*Unit: Self-Managed Security — Enabling Network Encryption*

### 27 — **a**

`mongodump --oplog` captures oplog entries for writes that occur *during* the dump, producing `oplog.bson`; `mongorestore --oplogReplay` then applies them, yielding a consistent point-in-time restore. `--oplog` requires a replica set member.

- **b** compresses into an archive but captures no oplog, so the dump is not point-in-time consistent.
- **c** `mongoexport`/`mongoimport` are for data interchange (JSON/CSV) and are not backup tools — they lose type fidelity and offer no consistency guarantee.
- **d** are valid options, but neither addresses point-in-time consistency.

*Units: Self-Managed Backup & Recovery — Backing Up / Restoring a Deployment; DBA Tools — Backup and Restore Tools*

### 28 — **a**

`db.fsyncLock()` blocks writes and flushes pending writes to disk, so the snapshot captures a complete, consistent set of data files. `db.fsyncUnlock()` must follow — forgetting it leaves the deployment unable to accept writes.

- **b** stepping down changes the primary; it neither quiesces writes nor flushes them.
- **c** rotates the log file and has nothing to do with flushing data.
- **d** reverses the order, leaving writes in flight during the snapshot and the node locked afterwards.

*Unit: Self-Managed Backup & Recovery — Filesystem Snapshots / Snapshot Volumes*

### 29 — **a**

Tickets Available reports how many concurrent read and write operations the WiredTiger storage engine can still admit. As the value approaches zero, incoming operations wait for a ticket, which shows up as rising latency — a concurrency bottleneck, typically downstream of slow disk or CPU saturation.

- **b** describes the Connections metric.
- **c** describes replication lag / the oplog window.
- **d** describes Query Targeting, whose ideal scanned-to-returned ratio is about 1:1.

*Unit: Database Metrics & Monitoring — Core Metrics / More Metrics*

### 30 — **c**

Without the shard key, `mongos` cannot determine which shards hold matching documents, so it broadcasts the query to **all** shards, waits for their responses, and merges them. This is why shard key selection should reflect the application's most common query filters.

- **a** is wrong; config servers store cluster metadata and never execute queries against collection data.
- **b** describes no MongoDB routing behavior; the primary shard is only where unsharded collections in a database live.
- **d** is wrong; such queries are permitted, just inefficient.

*Units: Getting Started with Atlas — MongoDB Architecture Overview; sharding fundamentals (see docs on Sharded Cluster Query Routing)*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| CRUD operations & query operators | 1, 2, 3 | 3 |
| Data modeling | 5, 6 | 2 |
| Indexing, query plans & explain | 7, 8, 9, 10, 11, 12, 13, 14, 15 | 9 |
| Logging & profiling | 16 | 1 |
| Replication, elections, read/write concerns | 17, 18, 19, 20 | 4 |
| Upgrades & maintenance | 21 | 1 |
| Security: authn, authz, auditing, encryption | 22, 23, 24, 25, 26 | 5 |
| Backup & recovery | 27, 28 | 2 |
| Metrics & monitoring | 29 | 1 |
| DBA tools | 4 | 1 |
| Sharded cluster architecture | 30 | 1 |

**Scoring guide:** 27+ = strong; 22–26 = solid, review the missed areas; below 22 = revisit those units before attempting a timed mock.

## Where to Focus Next

Two areas the uploaded course material covers thinly relative to the official practice questions, and worth reading around:

- **Sharding** — shard keys, hashed vs. ranged distribution, `mongos` routing, targeted vs. scatter-gather queries, the balancer, and why `mongodump`/`mongorestore` are not recommended for sharded clusters.
- **Aggregation pipeline fundamentals** — `$match` placement and why it belongs early, `$group`, `$sort`, `$limit`, `$project`, and the resource profile of `$group`/`$sort` on large collections.