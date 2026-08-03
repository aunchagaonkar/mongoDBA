# MongoDB Associate DBA — Practice Set 6 (Applied & Diagnostic)

**30 questions in a different format.** The syllabus facts have been covered across Sets 1–5; this set tests the same knowledge by asking you to *apply* it rather than recall it. Every question presents an artefact — a configuration file, a command, a plan, a log line, a status output — or a set of symptoms, and asks what is wrong, what happens next, or what to do about it.

That shift matters because recognising a fact in a definition question and spotting its consequence in a broken configuration are different skills, and the exam tests both. Expect several questions where more than one option is true in isolation and only one answers the question asked.

**How to use it:** answer all 30 before opening the key. There are no *select all that apply* questions.

---

## Questions

### Question 1

An administrator deploys a three-member replica set with the following in each `mongod.conf`:

```yaml
storage:
  dbPath: /var/lib/mongodb
net:
  bindIp: 0.0.0.0
  port: 27017
replication:
  replSetName: replset
security:
  authorization: enabled
```

Client authentication works, but replication never establishes and elections fail.

What is missing from this configuration?

- **a.** `security.keyFile`, which provides the internal authentication members need to verify each other
- **b.** `net.tls.mode`, which is mandatory whenever `authorization` is enabled
- **c.** `replication.oplogSizeMB`, without which members cannot exchange oplog entries
- **d.** `security.clusterAuthMode: scram`, which must be set explicitly for members to authenticate

---

### Question 2

An administrator adds the following to `mongod.conf` and restarts the service. `mongod` fails to start.

```yaml
net:
  port: 27017
  tls:
    mode: requireTLS
```

What is the cause?

- **a.** `requireTLS` is not a valid mode value; the correct value is `required`.
- **b.** No `certificateKeyFile` is specified, so the server has no certificate to present.
- **c.** TLS cannot be enabled on port 27017; a dedicated TLS port must be configured.
- **d.** `mode` must be set to `preferTLS` first and can never be set to `requireTLS` directly in the configuration file.

---

### Question 3

A user was created on the `admin` database with `readWrite` on `salesdb`. The application connects with:

```text
mongodb+srv://salesApp:<password>@cluster0.abcde.mongodb.net/salesdb?retryWrites=true&w=majority
```

The credentials are confirmed correct, but the application receives `MongoServerError: bad auth : Authentication failed.`

What should be changed?

- **a.** Add `authSource=admin` to the connection string, because the driver is otherwise authenticating against `salesdb`.
- **b.** Add the client's IP address to the Atlas Network Access allowlist.
- **c.** Change `w=majority` to `w=1`, because majority write concern requires an elevated role.
- **d.** Recreate the user on `salesdb` instead, because `readWrite` cannot be granted across databases.

---

### Question 4

An administrator creates `/etc/mongosh.conf` with the following content, then opens a new shell. The batch size is still the default.

```yaml
displayBatchSize: 50
```

What is wrong?

- **a.** The option belongs in `~/.mongoshrc.js`; `mongosh.conf` accepts only telemetry settings.
- **b.** The options must be nested under a top-level `mongosh:` key.
- **c.** `displayBatchSize` must be written as `display_batch_size` in the configuration file.
- **d.** The file must be JSON, not YAML.

---

### Question 5

A query on an `invoices` collection filters on `region` with an equality match, filters on `amount` with a range, and sorts by `issuedAt` descending.

Which of the following indexes will still leave a blocking in-memory `SORT` stage in the plan?

- **a.** `{ region: 1, issuedAt: -1, amount: 1 }`
- **b.** `{ region: 1, amount: 1, issuedAt: -1 }`
- **c.** `{ region: 1, issuedAt: -1 }`
- **d.** `{ region: 1, issuedAt: -1, amount: -1 }`

---

### Question 6

A database administrator has two viable indexes on a collection and has confirmed with `explain("allPlansExecution")` that the query planner is choosing the less selective one for a particular reporting query, producing a much higher key count than the alternative.

What is the appropriate way to make that query use the better index?

- **a.** Drop the index the planner is choosing, so only the better one remains.
- **b.** Append `hint()` to the query, specifying the index to use.
- **c.** Hide the less selective index with `hideIndex()` permanently.
- **d.** Recreate the better index so it is the most recently built and therefore preferred.

---

### Question 7

`$indexStats` on an `accounts` collection reports the following for one index:

```js
{
  name: "email_1",
  key: { email: 1 },
  accesses: { ops: Long("0"), since: ISODate("2026-05-02T00:00:00Z") },
  spec: { unique: true }
}
```

The administrator is compiling a list of indexes to drop in order to reduce write overhead.

How should this index be treated?

- **a.** Drop it — three months with zero operations is conclusive evidence it is unnecessary.
- **b.** Do not drop it on usage alone: it is a unique index, so it is enforcing a constraint on writes even though no read operation has used it.
- **c.** Drop it, then recreate it as a sparse index, which carries no write cost.
- **d.** Hide it permanently, which removes the write overhead while preserving the constraint.

---

### Question 8

The following entry appears in `mongod.log`:

```json
{
  "t": { "$date": "2026-07-28T11:02:44.310+00:00" },
  "s": "I",
  "c": "COMMAND",
  "ctx": "conn4471",
  "msg": "Slow query",
  "attr": {
    "ns": "shop.orders",
    "planSummary": "COLLSCAN",
    "keysExamined": 0,
    "docsExamined": 1840221,
    "nreturned": 6,
    "durationMillis": 3187
  }
}
```

What does this entry indicate, and what is the appropriate response?

- **a.** The query is using a multikey index inefficiently; rebuild the index with a different key order.
- **b.** No index supports this query, so MongoDB examined 1.84 million documents to return six; run `explain()` and create a supporting index.
- **c.** The `mongod` process is short of memory, since `keysExamined` is zero; increase the cache size.
- **d.** The query is already covered, since `keysExamined` is zero; the duration reflects network transfer only.

---

### Question 9

Immediately before a planned rolling maintenance window, `rs.status()` returns the following for one member:

```js
{
  name: "mongod2.replset.com:27017",
  health: 1,
  state: 3,
  stateStr: "RECOVERING"
}
```

What should the administrator do?

- **a.** Proceed — `health: 1` confirms the member is available, and `RECOVERING` members are upgraded the same way as secondaries.
- **b.** Halt the maintenance until the member reaches `SECONDARY`, because performing maintenance while a member's state is unclear risks data corruption or loss.
- **c.** Remove the member from the replica set, perform the maintenance on the remaining two, then re-add it.
- **d.** Force the member to `SECONDARY` with `rs.reconfig()` and continue.

---

### Question 10

Pre-upgrade checks on a three-member replica set return the following feature compatibility versions:

- `mongod0`: 5.0
- `mongod1`: 5.0
- `mongod2`: 4.4

What should the administrator do before upgrading the deployment to 6.0?

- **a.** Nothing — the upgrade sets all members to a common feature compatibility version automatically.
- **b.** Investigate and align all members on the same feature compatibility version first, since a mismatch indicates the previous upgrade did not complete.
- **c.** Upgrade `mongod2` to 6.0 first, which will bring its feature compatibility version forward two releases at once.
- **d.** Set every member's feature compatibility version to 6.0 now, then upgrade the binaries.

---

### Question 11

A junior administrator submits the following plan for upgrading a replica set from 5.0 to 6.0:

1. Confirm oplog window and member states.
2. Set `setFeatureCompatibilityVersion` to `"6.0"`.
3. Gracefully shut down and upgrade each secondary in turn.
4. Step down the primary and upgrade it.

Which step is out of order, and where does it belong?

- **a.** Step 1 should come after step 3, since member states change during the upgrade.
- **b.** Step 2 should come last, after every member is running the new binary.
- **c.** Step 4 should come before step 3, so the primary runs the newest binary throughout.
- **d.** The plan is correct as written.

---

### Question 12

A backup was produced with `mongodump --db=orders --out=/backups/nightly`. During a recovery drill, the administrator runs `mongorestore --oplogReplay /backups/nightly`.

What happens?

- **a.** The restore succeeds and applies the oplog entries captured during the dump.
- **b.** The restore fails or has nothing to replay, because the dump was taken without `--oplog` and therefore contains no `oplog.bson`.
- **c.** The restore succeeds but silently skips indexes, since `--oplogReplay` implies `--noIndexRestore`.
- **d.** The restore replays the target deployment's current oplog, rolling it forward to the present moment.

---

### Question 13

An administrator documents the following snapshot procedure:

1. `db.fsyncLock()`
2. `lvcreate --size 100M --snapshot --name mdb-snap /dev/vg0/mongodb`
3. Mount the snapshot read-only.
4. `tar -czvf /backups/mdb.tar.gz /mnt/mdb-snap`
5. Copy the archive to the backup host.

What is the critical omission?

- **a.** `db.fsyncUnlock()` is never run, so the deployment remains unable to accept writes after the procedure.
- **b.** The snapshot should be created before the database is locked, so writes are captured.
- **c.** `bsondump` must be run on the snapshot before it can be archived.
- **d.** The `mongod` service must be stopped between steps 1 and 2.

---

### Question 14

A team documents this plan for introducing TLS to a running replica set whose application servers currently connect without encryption:

1. Obtain CA-issued certificates for each member.
2. Set `net.tls.mode: requireTLS` and `certificateKeyFile` on each member, restarting them one at a time.
3. Reconfigure the application servers to connect with TLS.

Which step creates an outage, and how should the plan change?

- **a.** Step 1 — self-signed certificates should be used first and replaced with CA certificates later.
- **b.** Step 2 — `requireTLS` rejects the applications' non-TLS connections immediately; use `preferTLS` during the transition and tighten to `requireTLS` after step 3.
- **c.** Step 3 — the applications must be reconfigured before any member is restarted, then TLS enabled last.
- **d.** No step causes an outage, because MongoDB negotiates TLS per connection regardless of mode.

---

### Question 15

An administrator lists the ways to change the slow operation threshold on a self-managed deployment.

Which of the following is **not** a valid way to set `slowms`?

- **a.** Passing it to `db.setProfilingLevel()` as an option
- **b.** Setting `operationProfiling.slowOpThresholdMs` in the configuration file
- **c.** Using `setParameter` to set `slowOpThresholdMs`
- **d.** Passing it to `db.setLogLevel()` as an option

---

### Question 16

An Atlas alert reports that Query Targeting has exceeded its threshold on a production cluster.

What is the appropriate **first** action?

- **a.** Create indexes on the fields most commonly filtered in the application, based on the schema.
- **b.** Identify the specific queries responsible, using Performance Advisor or the profiler, before changing any indexes.
- **c.** Increase the cluster tier, since the ratio reflects insufficient hardware for the workload.
- **d.** Raise the alert threshold, since Query Targeting fluctuates with normal traffic.

---

### Question 17

The Connections metric on a production cluster has climbed steadily for a week and is now approaching the tier's limit, while the operation rate has stayed flat.

What is the appropriate first step?

- **a.** Restart the cluster to clear the accumulated connections.
- **b.** Investigate which applications hold the connections and whether their driver connection pools are misconfigured or leaking, since a flat operation rate with rising connections suggests connections are not being released.
- **c.** Upgrade the cluster tier immediately, since the connection limit is fixed per tier and cannot be influenced by the application.
- **d.** Set `w: 0` on application writes to reduce the time each connection stays open.

---

### Question 18

After an unplanned failover, `rs.status()` shows the former primary in state `ROLLBACK`.

What has occurred?

- **a.** The member is reverting writes it had accepted but which never reached a majority of members before it lost primary status.
- **b.** The member is reapplying its entire oplog from the beginning as part of an initial sync.
- **c.** The member has detected data file corruption and is restoring from the most recent checkpoint.
- **d.** The member is rolling back a configuration change made with `rs.reconfig()`.

---

### Question 19

A self-managed host running a replica set member raises a low disk space alert. The administrator considers four responses.

Which one resolves the immediate symptom but introduces a new operational risk?

- **a.** Expanding the underlying storage volume
- **b.** Archiving and removing historical data the application no longer queries
- **c.** Reducing the configured oplog size to reclaim space
- **d.** Rotating and compressing accumulated log files

---

### Question 20

A five-member replica set loses three members to a data centre failure. The application, which writes with `{ w: "majority" }`, begins failing all writes.

Why?

- **a.** A majority of the five voting members is unreachable, so no primary can be maintained and the deployment accepts no writes at all.
- **b.** The two surviving members cannot satisfy `w: "majority"`, but writes would succeed if the write concern were lowered to `w: 1`.
- **c.** The surviving members entered `ROLLBACK`, which blocks writes until they complete.
- **d.** Write concern `"majority"` requires acknowledgement from all members, so any failure blocks writes.

---

### Question 21

An application reads a document immediately after another process writes it, displays the value to a user, and then — following an unplanned failover moments later — finds the value is no longer present in the database.

Which setting would have prevented the application from reading that value in the first place?

- **a.** `readPreference=primary`
- **b.** `readConcern` level `"majority"`
- **c.** `maxStalenessSeconds=90`
- **d.** `w: "majority"` on the application's own writes

---

### Question 22

A nightly job updates customer records with the following operation. It was expected to update one existing document per customer, but the collection has grown by several thousand documents overnight.

```js
db.customers.updateOne(
  { customerID: feed.customerId },
  { $set: { lastSeen: new Date(), tier: feed.tier } },
  { upsert: true }
)
```

What is the most likely cause?

- **a.** `updateOne()` with `upsert: true` always inserts a new document in addition to updating the matched one.
- **b.** The filter field name does not match the stored field, so nothing matched and `upsert` inserted a new document for every record processed.
- **c.** `$set` cannot be combined with `upsert`, so MongoDB fell back to inserting.
- **d.** `new Date()` makes each update document unique, which forces an insert.

---

### Question 23

A nightly `mongoimport` job loads a JSON file of complete product documents, each with its own `_id`. The job succeeded the first night and now fails every night with duplicate key errors on `_id`.

What should be changed?

- **a.** Add `--mode=upsert`, so records that already exist are replaced rather than inserted.
- **b.** Add `--drop`, which is required for repeated imports.
- **c.** Remove the `_id` field from the file so MongoDB generates new values.
- **d.** Add `--jsonArray`, which permits duplicate keys.

---

### Question 24

A `mongoexport` job feeds a downstream pipeline that must reconstruct each document's exact BSON types — distinguishing 32-bit integers from doubles, and dates from strings — when the data is loaded back into MongoDB.

Which option should be used?

- **a.** `--jsonFormat=canonical`
- **b.** `--jsonFormat=relaxed`
- **c.** `--type=csv`
- **d.** `--pretty`

---

### Question 25

A file named `2026-final-report.pdf` was uploaded to the GridFS store in the `archive` database. The administrator runs the following and gets no results:

```bash
mongofiles -d=archive list final
```

What explains this?

- **a.** `list` matches filenames by prefix, and this filename begins with `2026-`; `search final` matches names containing the string.
- **b.** `mongofiles` cannot list PDF files, only text files.
- **c.** The `-d` flag must name the collection, not the database.
- **d.** GridFS filenames are stored hashed, so they cannot be matched by name at all.

---

### Question 26

A `stores` collection contains documents of this shape, and the array grows with every transaction:

```js
{
  _id: 4471,
  storeName: "Nashik Central",
  managerName: "Rohit Kale",
  managerEmail: "rohit.kale@example.com",
  transactions: [ /* one subdocument per sale, ~400 added per day */ ]
}
```

Which problem will force a change to this schema first?

- **a.** The duplication of manager details, which will eventually exceed the field limit per document.
- **b.** The unbounded `transactions` array, which grows without limit and will approach the 16 MB BSON document limit.
- **c.** The use of an integer `_id`, which prevents MongoDB from generating ObjectId values.
- **d.** The mixture of scalar fields and an array in one document, which prevents indexing.

---

### Question 27

A `films` collection embeds a `genres` array holding two or three genre names per film, drawn from a fixed list of about 40. A developer proposes moving genres into a separate collection and referencing them by `_id`, arguing that referencing is the more normalised design.

How should the database administrator assess this proposal?

- **a.** Accept it — referencing is always preferable for many-to-many relationships in MongoDB.
- **b.** Reject it — the array is small and bounded, and the application reads genres with the film, so referencing would add a second query with no offsetting benefit.
- **c.** Accept it — embedding an array of any length violates MongoDB's data modeling principles.
- **d.** Reject it — MongoDB cannot index fields in referenced collections, so genre queries would become collection scans.

---

### Question 28

A team is choosing a shard key for a `patients` collection of 40 million documents. One proposal is to shard on `bloodGroup`, which holds one of eight possible values.

What is the problem with this choice?

- **a.** Cardinality is too low: eight distinct values cannot be divided into enough chunks to distribute data evenly, so chunks grow oversized and cannot be split further.
- **b.** String fields cannot be used as shard keys; the key must be numeric or a date.
- **c.** The shard key must be unique across the collection, which `bloodGroup` is not.
- **d.** Low-cardinality keys prevent the balancer from running, so no migrations would occur.

---

### Question 29

A replica set has four data-bearing members plus one arbiter, giving five votes. Two of the data-bearing members have `priority: 0`. One of the electable members is currently primary; the other electable member and the arbiter are both reachable.

Can this deployment elect a new primary if the current primary fails?

- **a.** Yes — the remaining four votes exceed the majority of three, and one reachable member has a priority above zero and is therefore electable.
- **b.** No — with two members set to `priority: 0`, fewer than a majority of members are electable.
- **c.** No — an arbiter cannot vote in an election that follows a primary failure.
- **d.** Yes, but only after the two `priority: 0` members are reconfigured, since MongoDB requires a majority of electable members.

---

### Question 30

A security review examines a monitoring service account whose stated requirement is: collect diagnostic metrics, and never read application data. The account holds these roles:

```js
roles: [
  { role: "clusterMonitor", db: "admin" },
  { role: "read", db: "local" },
  { role: "readAnyDatabase", db: "admin" }
]
```

Which role violates the stated requirement and should be removed?

- **a.** `clusterMonitor` on `admin`
- **b.** `read` on `local`
- **c.** `readAnyDatabase` on `admin`
- **d.** None; all three are required for metric collection

---

## Answer Key

### 1 — **a**

When access control is enabled on a replica set, members must authenticate to **each other**, not just to clients. That internal authentication comes from a shared keyfile referenced by `security.keyFile` (or from x.509 certificates). Without it, members cannot verify each other's identity, so replication and elections fail while client authentication continues to work — exactly the split symptom described.

- **b** TLS is a separate concern; access control does not require it.
- **c** the oplog is created automatically with a default size.
- **d** `clusterAuthMode` defaults to keyfile-based authentication and is not the missing piece; the keyfile itself is.

*Units: Self-Managed Security — Enabling Authentication; Replication — Deploying a Replica Set*

### 2 — **b**

Any TLS mode other than `disabled` requires the server to present a certificate, supplied by `net.tls.certificateKeyFile`. Without it `mongod` has nothing to offer during the handshake and refuses to start. The startup failure is logged, which is where an administrator should look first.

- **a** `requireTLS` is the correct value.
- **c** MongoDB uses the same port for TLS and non-TLS connections.
- **d** `requireTLS` can be set directly; `preferTLS` is a migration convenience, not a prerequisite.

*Unit: Self-Managed Security — Enabling Network Encryption*

### 3 — **a**

The database named in the connection string path is also the **authentication database** unless `authSource` says otherwise. Here the driver tries to authenticate `salesApp` against `salesdb`, where the user does not exist, so authentication fails despite correct credentials. Adding `authSource=admin` points authentication at the database where the user was created.

- **b** would address a `MongoServerSelectionError`, not `bad auth`.
- **c** write concern has no bearing on authentication.
- **d** is false — a user on `admin` can hold roles scoped to any database, which is the standard pattern.

*Units: Connecting to a MongoDB Database — Troubleshooting Connection Errors; Self-Managed Security — Establishing Authorization*

### 4 — **b**

`mongosh` configuration files are YAML with all options nested under a top-level `mongosh:` key. A bare key at the document root is ignored, which is why the shell silently keeps the default.

```yaml
mongosh:
  displayBatchSize: 50
```

- **a** is wrong on both counts; `mongosh.conf` handles far more than telemetry.
- **c** the option name is correct as written.
- **d** the file is YAML.

*Unit: The MongoDB Shell — Configuring the MongoDB Shell*

### 5 — **b**

Apply ESR. The query has equality on `region`, a **sort** on `issuedAt`, and a **range** on `amount`. Option (b) places the range field before the sort field, which breaks the index's ability to return documents in `issuedAt` order — so a blocking `SORT` remains. Options (a) and (d) follow Equality → Sort → Range correctly. Option (c) omits `amount` entirely, so the range is applied as a post-scan filter, but the sort is still served by the index and no blocking sort occurs.

*Unit: Indexes II — Optimized Compound Indexes*

### 6 — **b**

`hint()` instructs MongoDB to use a specified index for a query, which is the targeted way to override a planner choice for one query without affecting anything else.

- **a** drops an index other queries may depend on, to fix one query.
- **c** hiding the index has the same collateral effect as dropping it for every other query, and permanently hiding an index you intend to keep is not the tool's purpose — it exists to test removal.
- **d** build order does not influence plan selection.

*Unit: Indexes II — Index Usage Details via Explain; Clustered Indexes (hint)*

### 7 — **b**

A unique index does two jobs: it can serve queries, and it **enforces a constraint on every write**. `$indexStats` counts only the first. Zero read operations therefore proves the index is unused for queries while saying nothing about the constraint it is enforcing. Dropping it would silently permit duplicate email values.

- **a** treats usage statistics as the whole picture, which is the trap.
- **c** is false; a sparse index still carries write cost, and sparseness would also change which documents the constraint covers.
- **d** is false; a hidden index is still maintained on every write and still enforces uniqueness, so hiding it saves nothing.

*Units: Indexes II — How to Monitor Indexes; Indexes — Creating a Single Field Index*

### 8 — **b**

`planSummary: "COLLSCAN"` with `keysExamined: 0` means no index was used at all. MongoDB read 1,840,221 documents to return 6, taking 3.2 seconds. The response is to run `explain()` on the query and build an index that supports its filter and sort.

- **a** contradicts the plan summary; there is no index in use.
- **c** misreads `keysExamined: 0`, which reflects the absence of an index scan, not memory pressure.
- **d** inverts the meaning — a covered query shows `docsExamined: 0`, not `keysExamined: 0`.

*Units: Logging Basics — MongoDB Log Events; Indexes II — Index Usage Details via Explain*

### 9 — **b**

`RECOVERING` means the member is not currently able to serve reads and is working toward a consistent state. MongoDB's pre-maintenance guidance is explicit: no member should be in `ROLLBACK` or `RECOVERING`, because acting while a member's state is unresolved sharply raises the risk of corrupting or losing data. Wait for `SECONDARY`, and investigate why it is recovering.

- **a** misreads `health: 1`, which reports reachability, not readiness.
- **c** removing members is unnecessary and would reduce the set's fault tolerance during the very window when it is most needed.
- **d** `rs.reconfig()` cannot force a member's state.

*Units: Upgrades & Maintenance — MongoDB Server Upgrades; Replication — Configuring a Replica Set*

### 10 — **b**

Every member should report the **same** feature compatibility version before an upgrade. A member sitting at 4.4 while the others are at 5.0 indicates the previous upgrade did not complete — most likely `setFeatureCompatibilityVersion` was never run, or was run while that member was unavailable. Resolve that before layering another upgrade on top.

- **a** is false; FCV is never aligned automatically.
- **c** is false; major versions must be installed consecutively, and FCV does not advance two releases at once.
- **d** ignores the anomaly rather than investigating it, and setting FCV forward on a member whose history is unclear compounds the problem.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 11 — **b**

`setFeatureCompatibilityVersion` is the **last** step, run only after every member is running the new binary. Raising it early enables features that persist data in a format the not-yet-upgraded members cannot read, and it also forecloses a straightforward downgrade if the upgrade goes badly.

- **a** the pre-checks belong at the start; that is their purpose.
- **c** the primary is upgraded last, by stepping down first so a writable primary remains available throughout.
- **d** the plan is not correct as written.

*Unit: Upgrades & Maintenance — MongoDB Server Upgrades*

### 12 — **b**

`--oplogReplay` replays the `oplog.bson` file that `mongodump --oplog` produces. This dump was taken without `--oplog`, so there is no such file and there is nothing to replay — the restore has no point-in-time capability regardless of the flag used at restore time. Point-in-time consistency is decided when the **backup** is taken, not when it is restored.

- **a** assumes an oplog that was never captured.
- **c** invents an implication between unrelated options.
- **d** is not what `--oplogReplay` does; it never reads the target's live oplog.

*Units: Self-Managed Backup & Recovery — Backing Up and Restoring a Deployment; DBA Tools — Backup and Restore Tools*

### 13 — **a**

`db.fsyncLock()` blocks writes and holds them blocked until `db.fsyncUnlock()` is called. The procedure locks the database in step 1 and never unlocks it, so the deployment stops accepting writes for the entire archive-and-copy operation — and stays that way afterwards. The unlock belongs immediately after the snapshot is created in step 2; the snapshot itself is instantaneous, so the write pause should last seconds, not the duration of the archiving.

- **b** inverts the sequence; the lock exists to make the snapshot consistent.
- **c** `bsondump` reads `.bson` dump files, not filesystem snapshots.
- **d** stopping `mongod` defeats the purpose of snapshotting a running deployment.

*Unit: Self-Managed Backup & Recovery — Snapshot Volumes; Filesystem Archives*

### 14 — **b**

`requireTLS` rejects every non-TLS connection the moment a member restarts with it, so the still-unconverted application servers are cut off — an immediate outage. `preferTLS` encrypts member-to-member traffic while continuing to accept both TLS and non-TLS client connections, which is what makes a staged migration possible. Tighten to `requireTLS` only after step 3 is complete and verified.

- **a** self-signed certificates would weaken the deployment rather than help sequencing.
- **c** applications cannot connect with TLS before the servers present certificates, so this ordering fails too.
- **d** is false; the mode is a server-wide policy, not a per-connection negotiation.

*Unit: Self-Managed Security — Enabling Network Encryption*

### 15 — **d**

`db.setLogLevel()` sets **log verbosity**, either globally or for a component. It takes no `slowms` option. The three valid mechanisms are `db.setProfilingLevel()`, the `operationProfiling.slowOpThresholdMs` configuration setting, and `setParameter`.

*Unit: Logging Basics — MongoDB Server Log Customizations*

### 16 — **b**

Query Targeting tells you a problem exists; it does not tell you which queries cause it. Identifying the offending operations first — through Performance Advisor, the profiler, or the slow query log — is what makes the subsequent index the right one. Indexing from the schema alone risks adding indexes that carry write cost without addressing the actual workload.

- **a** is the second step, not the first, and guessing produces over-indexing.
- **c** treats a query problem as a capacity problem; a larger tier scans the same excess data faster and more expensively.
- **d** suppresses the signal.

*Units: Database Metrics & Monitoring — Core Metrics; Indexes II — How to Monitor Indexes*

### 17 — **b**

Connections rising while the operation rate stays flat is the signature of connections being opened and not released — a leak, or a pool configured to grow without a sensible ceiling. The diagnosis is to find which clients hold them, which the Real-Time Performance Panel and `currentOp` can show.

- **a** clears the symptom temporarily and destroys the evidence, and the count will climb again.
- **c** may eventually be warranted, but scaling around a leak means paying for it indefinitely; the premise that the application has no influence is also wrong.
- **d** unacknowledged writes do not shorten connection lifetime and introduce a serious new risk.

*Unit: Database Metrics & Monitoring — More Metrics; Command Line Metrics*

### 18 — **a**

`ROLLBACK` occurs when a member that had been primary accepted writes that never propagated to a majority, then lost primary status. On rejoining it must discard those writes to match the new primary's history. The preventive measure is `w: "majority"` — writes acknowledged under majority write concern are never rolled back, because a majority already holds them.

- **b** describes initial sync, a different state (`STARTUP2`).
- **c** describes recovery from a checkpoint, not rollback.
- **d** replica set configuration changes are not rolled back through this state.

*Unit: Replication — Automatic Failover and Elections; Read and Write Concerns*

### 19 — **c**

Shrinking the oplog does free disk space immediately, and it is the trap precisely because it works. The cost is a shorter oplog window: less time for a secondary to be offline before requiring a full initial sync, and a narrower margin for rolling maintenance. Expanding storage, archiving unqueried data, and rotating logs all resolve the symptom without weakening recovery.

*Units: Replication — The MongoDB Operation Log; Database Metrics & Monitoring — Core Metrics*

### 20 — **a**

Elections need a majority of **voting members** — three of five here. With three members lost, the two survivors cannot form that majority, so no primary can be elected or sustained. Without a primary the deployment accepts no writes at all, whatever write concern the application requests.

- **b** is the tempting near-miss: lowering write concern cannot help, because the problem is the absence of a primary rather than an unmet acknowledgement threshold.
- **c** rollback is unrelated.
- **d** misdefines `"majority"`, which is a calculated majority rather than all members.

*Unit: Replication — Automatic Failover and Elections; Read and Write Concerns*

### 21 — **b**

The read used the default read concern, `"local"`, which returns the primary's most recent data whether or not a majority holds it. The write had not yet replicated when it was read, and the subsequent failover rolled it back — so the application displayed a value that never durably existed. Read concern `"majority"` would have withheld that value until it was majority-committed.

- **a** the read almost certainly already came from the primary; that is where the problem originated.
- **c** bounds secondary staleness and does nothing about un-replicated primary writes.
- **d** `w: "majority"` on the *application's own* writes protects those writes, but the write here came from another process, and write concern cannot constrain what this read returns.

*Unit: Replication — Read and Write Concerns*

### 22 — **b**

With `upsert: true`, a filter that matches nothing causes an **insert**. If `customerID` does not match the stored field name — the collection uses `customer_id`, say, or the value type differs — then every record in the feed matches nothing and inserts a fresh document, which is precisely the growth described. Upsert converts a silent no-match into silent data creation, which is why the filter field deserves scrutiny.

- **a** is false; upsert inserts only when nothing matched.
- **c** is false; `$set` with `upsert` is the standard pattern.
- **d** is false; fields in the update expression have no bearing on matching.

*Unit: CRUD — Updating Documents with updateOne()*

### 23 — **a**

`mongoimport` defaults to `--mode=insert`, which fails when a document's `_id` already exists — hence success on the first night and failure on every night after. `--mode=upsert` replaces the matching document instead, which is correct here because the file contains complete documents. (Were the file to contain partial records, `merge` would be the right choice.)

- **b** `--drop` is a `mongorestore` option, not a `mongoimport` one.
- **c** discards the identifiers that make the records addressable and would create duplicates on every run.
- **d** `--jsonArray` describes the input file's structure and has no effect on duplicate handling.

*Unit: DBA Tools — Data Import Tools*

### 24 — **a**

Canonical JSON preserves BSON type information using extended JSON wrappers such as `$numberInt`, `$numberDouble`, and `$date`, so the original types can be reconstructed exactly. Relaxed mode — the default — renders values in a more readable form and loses some of that fidelity.

- **c** CSV discards type information and nesting entirely.
- **d** `--pretty` affects whitespace only.

*Unit: DBA Tools — Data Export Tools*

### 25 — **a**

`mongofiles list <prefix>` matches filenames that **begin with** the argument. `2026-final-report.pdf` starts with `2026-`, so `list final` matches nothing. `mongofiles -d=archive search final` matches names *containing* the string and would find it.

- **b** GridFS is content-agnostic.
- **c** `-d` names the database, which is correct as written.
- **d** filenames are stored as-is in the files collection.

*Unit: DBA Tools — MongoDB as a Filesystem (mongofiles)*

### 26 — **b**

The unbounded array is the hard constraint. At roughly 400 subdocuments a day it grows without limit, and MongoDB enforces a 16 MB maximum document size — so this schema eventually stops accepting writes altogether. Long before that it degrades write performance, because the whole document is rewritten on every append, and read performance, because the whole document must be loaded into memory. The fix is a separate `transactions` collection referencing the store.

- **a** duplicated manager details are a real modeling concern, but no field limit exists of the kind described, and duplication does not produce a hard failure.
- **c** is false; a user-supplied `_id` of any type is permitted.
- **d** is false; documents routinely mix scalars and arrays, and arrays are indexed as multikey.

*Unit: Data Modeling — Scaling a Data Model; Embedding Data in Documents*

### 27 — **b**

The current design is already correct. Two or three genre names per film is a small, bounded array read together with the film, so embedding satisfies "data accessed together is stored together" and answers both access patterns — showing genres with a film, and finding films by genre — in a single query each. Referencing would add a second query for a handful of values and gain nothing.

- **a** overgeneralises; the choice depends on array size, growth, and access pattern.
- **c** is false; bounded arrays are the case embedding is *for*.
- **d** is false — referenced collections are indexed normally. The proposal is unnecessary, not unworkable.

*Units: Document Model — Embedding and Referencing; Data Modeling — Referencing Data in Documents*

### 28 — **a**

A shard key needs high cardinality. Eight distinct values across 40 million documents means MongoDB cannot split the data into more than eight ranges, so each chunk becomes enormous, cannot be divided further, and cannot be balanced across shards. Distribution ends up dictated by the natural frequency of the eight blood groups rather than by the balancer.

- **b** is false; strings are valid shard keys.
- **c** is false; shard keys need not be unique.
- **d** is false; the balancer still runs, but it has nothing it can usefully move.

*Unit: sharding fundamentals (see docs on Choosing a Shard Key)*

### 29 — **a**

Two things must both hold, and both do. The majority requirement is three of five votes; four members are reachable after the primary fails, so the votes are there — and `priority: 0` removes only *electability*, not the vote. Separately, at least one reachable member must be electable, and the second electable member is available. So an election succeeds.

- **b** confuses electability with voting; MongoDB requires a majority of *votes*, not of electable members.
- **c** is false; arbiters exist to vote in exactly this situation.
- **d** invents a requirement.

*Unit: Replication — Automatic Failover and Elections; Configuring a Replica Set*

### 30 — **c**

`readAnyDatabase` grants read access to every database on the deployment, which directly contradicts the requirement that the account never read application data. It should be removed. The other two are the standard monitoring grants: `clusterMonitor` supplies diagnostic commands such as `serverStatus` and `replSetGetStatus`, and `read` on `local` is needed for replication metrics.

*Units: Self-Managed Security — Establishing Authorization; Database Metrics & Monitoring — Self-Managed Monitoring*

---

## Coverage Map

| Topic area | Questions | Count |
|---|---|---|
| Security: internal auth, TLS, roles | 1, 2, 14, 30 | 4 |
| Connecting & shell configuration | 3, 4 | 2 |
| Indexing, explain & index hygiene | 5, 6, 7, 8 | 4 |
| Replication states & concerns | 9, 18, 20, 21, 29 | 5 |
| Upgrades & maintenance | 10, 11 | 2 |
| Backup & recovery | 12, 13 | 2 |
| Logging | 15 | 1 |
| Metrics & monitoring response | 16, 17, 19 | 3 |
| CRUD diagnostics | 22 | 1 |
| DBA tools | 23, 24, 25 | 3 |
| Data modeling assessment | 26, 27 | 2 |
| Sharded clusters | 28 | 1 |

**Scoring guide:** applied questions are harder than recall questions, so calibrate differently — 25+ = strong; 20–24 = solid; below 20 means the underlying facts are probably known but not yet automatic enough to apply under pressure.

## What This Set Was Testing

Each question maps to a fact covered in Sets 1–5, but asks something the earlier format did not:

- **Configuration consequences.** Knowing that a keyfile provides internal authentication is one thing; recognising *client auth works but replication fails* as the symptom of its absence is another (Q1). Same for `certificateKeyFile` (Q2) and `authSource` (Q3).
- **Sequencing.** The upgrade steps and the TLS rollout steps are individually familiar; the exam asks which one is in the wrong place (Q11, Q14).
- **Metrics as evidence rather than facts.** Q17 gives you two metrics moving in opposite directions and asks what the *combination* means. Q19 asks which of four working solutions creates a new risk.
- **When the obvious answer is wrong.** An unused index that enforces uniqueness should not be dropped (Q7). A well-modelled schema should not be "improved" (Q27). Lowering write concern cannot fix a missing primary (Q20).
- **Silent failure modes.** Upsert with a mistyped filter field inserts instead of updating, with no error (Q22). A bare key in `mongosh.conf` is ignored, with no error (Q4). `list` versus `search` in `mongofiles` returns nothing rather than complaining (Q25).

## Cumulative Coverage — All Six Sets

You now have 180 questions.

| Topic area | S1 | S2 | S3 | S4 | S5 | S6 | Total |
|---|---|---|---|---|---|---|---|
| Indexing, explain & profiling | 9 | 7 | 4 | 4 | 4 | 4 | 32 |
| Security | 5 | 5 | — | 4 | 4 | 4 | 22 |
| Replication | 4 | 3 | 3 | 4 | 3 | 5 | 22 |
| CRUD & query results | 3 | 4 | 4 | 3 | — | 1 | 15 |
| Metrics & monitoring | 1 | 2 | 4 | 2 | 3 | 3 | 15 |
| Backup & recovery | 2 | 2 | 2 | 2 | 3 | 2 | 13 |
| Data modeling & document model | 2 | 1 | 4 | 3 | 1 | 2 | 13 |
| DBA tools | 1 | 1 | 2 | 3 | 2 | 3 | 12 |
| Logging | 1 | 2 | 2 | 2 | 2 | 1 | 10 |
| Upgrades & maintenance | 1 | 2 | 1 | — | 3 | 2 | 9 |
| Shell & connecting | — | — | 4 | 2 | — | 2 | 8 |
| Sharded clusters | 1 | 1 | — | 1 | 1 | 1 | 5 |
| Atlas platform | — | — | — | — | 4 | — | 4 |
