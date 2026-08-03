# MongoDB DBA Practice Questions

---

### Question 1
**A monitoring application stores temperature readings from IoT sensors in a `readings` collection. Each document includes a `temperature` field. A MongoDB engineer needs to retrieve all readings where `temperature` is between 20 and 30 degrees, inclusive.**

Which query expression should the engineer use to retrieve the correct documents?

- **a. `{ temperature: { $gte: 20, $lte: 30 } }`** — **Correct**: Uses `$gte` (≥ 20) and `$lte` (≤ 30) to capture both endpoints inclusively.
- **b. `{ temperature: { $gt: 20, $lt: 30 } }`** — **Incorrect**: Uses strict comparisons, excluding 20 and 30.
- **c. `{ temperature: { $gte: 20 } }`** — **Incorrect**: Only applies a lower bound; no upper limit.
- **d. `{ temperature: { $lte: 30 } }`** — **Incorrect**: Only applies an upper bound; no lower limit.

---

### Question 2
**An HR application stores employee records in an `employees` collection. Each document includes a `department` field. A MongoDB developer needs to return the total number of employees in the "sales" department.**

Which method call should the developer use to return the correct count?

- **a. `db.employees.countDocuments({ department: "sales" })`** — **Correct**: `countDocuments()` with a filter returns the exact count of matching documents.
- **b. `db.employees.find({ department: "sales" }).limit(1)`** — **Incorrect**: Returns at most one document, not a count.
- **c. `db.employees.find({ department: "sales" })`** — **Incorrect**: Returns matching documents, not a count.
- **d. `db.employees.countDocuments()`** — **Incorrect**: Counts all documents without filtering by department.

---

### Question 3
**An e-commerce application stores orders in an `orders` collection. Each document includes a `total` field. A MongoDB developer needs to count all orders where `total` is greater than 100.**

Which method call should the developer use to return the correct count?

- **a. `db.orders.countDocuments({ total: { $gt: 100 } })`** — **Correct**: Uses `$gt` (strictly greater than 100) with `countDocuments()`.
- **b. `db.orders.countDocuments({ total: 100 })`** — **Incorrect**: Only matches orders where `total` exactly equals 100.
- **c. `db.orders.find({ total: { $gt: 100 } })`** — **Incorrect**: Returns matching documents, not a count.
- **d. `db.orders.countDocuments({ total: { $gte: 100 } })`** — **Incorrect**: Includes orders where `total` equals 100, which is not "greater than 100".

---

### Question 4
**A task management application stores tasks in a `tasks` collection. Each document includes a `status` field. A MongoDB developer needs to delete all tasks where `status` is "archived".**

Which operation should the developer use to remove the matching documents?

- **a. `db.tasks.deleteMany({ status: "archived" })`** — **Correct**: Deletes all documents matching the filter.
- **b. `db.tasks.deleteOne({ status: "archived" })`** — **Incorrect**: Only deletes the first matching document.
- **c. `db.tasks.updateMany({ status: "archived" }, { $set: { deleted: true } })`** — **Incorrect**: Updates documents rather than deleting them.
- **d. `db.tasks.deleteMany({ status: { $ne: "archived" } })`** — **Incorrect**: Deletes all documents that are *not* "archived".

---

### Question 5
**A developer needs to find all orders in an `orders` collection where the `finalPrice` field equals the `originalPrice` field, indicating no discount was applied.**

Which query should the developer use?

- **a. `db.orders.find({ $expr: { $eq: ["$finalPrice", "$originalPrice"] } })`** — **Correct**: `$expr` allows comparing two document fields against each other.
- **b. `db.orders.find({ finalPrice: originalPrice })`** — **Incorrect**: `originalPrice` is treated as a literal variable, not a field reference.
- **c. `db.orders.find({ finalPrice: { $eq: "originalPrice" } })`** — **Incorrect**: Compares `finalPrice` to the string `"originalPrice"`, not the field value.
- **d. `db.orders.find({ finalPrice: "$originalPrice" })`** — **Incorrect**: `"$originalPrice"` is a plain string, not a field reference outside of aggregation expressions.

---

### Question 6
**A product analytics system runs the following aggregation pipeline on a `products` collection. Each document includes an `inStock` boolean field and a `salesCount` field.**

```javascript
db.products.aggregate([
  { $match: { inStock: true } },
  { $sort: { salesCount: -1 } },
  { $limit: 10 }
])
```

What does the `$limit` stage produce in this pipeline?

- **a. The 10 documents with the highest `salesCount`** — **Correct**: After filtering in-stock items and sorting by `salesCount` descending, `$limit: 10` returns the top 10.
- **b. All in-stock documents sorted by `salesCount`** — **Incorrect**: `$limit` restricts the number of output documents.
- **c. The total count of in-stock products** — **Incorrect**: `$limit` does not produce a count.
- **d. Documents where `salesCount` equals 10** — **Incorrect**: `$limit` limits quantity of output, not filters by value.

---

### Question 7
**An analytics team builds aggregation pipelines against a large `transactions` collection. A MongoDB developer places `$match` as the first stage in the pipeline.**

What is the benefit the developer gains by placing `$match` at the start of a pipeline?

- **a. Reduces the number of documents passed to subsequent stages** — **Correct**: Filtering early reduces the working dataset for all downstream stages, improving performance.
- **b. Ensures `$group` operates on pre-sorted input** — **Incorrect**: `$match` filters documents; it does not sort them.
- **c. Prevents `$project` from including unnecessary fields** — **Incorrect**: `$project` controls field inclusion independently of `$match`.
- **d. Allows `$sort` to use an index regardless of pipeline position** — **Incorrect**: Index use by `$sort` depends on placement and index availability, not `$match` position.

---

### Question 8
**In what format does MongoDB store documents internally?**

- **a. BSON (Binary JSON)** — **Correct**: MongoDB stores documents in BSON format, a binary-encoded serialization of JSON-like documents.
- **b. JSON (JavaScript Object Notation)** — **Incorrect**: JSON is the representation used in queries and shell output, not the internal storage format.
- **c. XML (Extensible Markup Language)** — **Incorrect**: MongoDB does not use XML for document storage.
- **d. CSV (Comma-Separated Values)** — **Incorrect**: MongoDB does not use CSV for document storage.

---

### Question 9
**A healthcare application stores patient records. Each patient has a medical history with entries added continuously over time. The history entries are also queried independently by a billing system.**

Which data modeling approach is more appropriate for storing medical history entries?

- **a. Store medical history entries in a separate collection and reference them from the patient document.** — **Correct**: Unbounded growth and independent access by the billing system make a separate referenced collection the appropriate choice.
- **b. Store medical history entries as an unbounded array embedded within the patient document.** — **Incorrect**: Unbounded embedded arrays cause documents to grow indefinitely, exceeding the 16 MB document limit and degrading performance.
- **c. Store both patient and history data as flat key-value pairs in the same document.** — **Incorrect**: Flat key-value pairs cannot efficiently represent structured, growing history entries.
- **d. Store the patient document embedded within each individual medical history entry.** — **Incorrect**: This inverts the natural relationship and causes significant data duplication.

---

### Question 10
**A team configures their analytics application to use `secondaryPreferred` read preference to avoid impacting production traffic. A database administrator warns this does not fully isolate the analytics workload.**

What is the reason for the administrator's concern?

- **a. `secondaryPreferred` falls back to the primary when no secondary is available.** — **Correct**: If all secondaries are unavailable, `secondaryPreferred` routes reads to the primary, potentially impacting production traffic.
- **b. `secondaryPreferred` routes queries to the secondary with the highest replication lag.** — **Incorrect**: MongoDB does not route based on highest lag; it selects the closest or least-lagged secondary.
- **c. `secondaryPreferred` is not supported for aggregation pipelines.** — **Incorrect**: `secondaryPreferred` is supported for aggregation pipelines.
- **d. `secondaryPreferred` requires a dedicated analytics node to be configured before it takes effect.** — **Incorrect**: No dedicated node configuration is required.

---

### Question 11
**An organization's database stores financial transactions updated continuously throughout the day. The recovery point objective (RPO) requires no more than one hour of data loss.**

Which backup strategy satisfies this requirement?

- **a. A continuous backup strategy with point-in-time recovery (PITR)** — **Correct**: PITR enables recovery to any point within the backup window, satisfying a sub-hour RPO.
- **b. A scheduled logical backup strategy utilizing `mongodump` every 24 hours** — **Incorrect**: A 24-hour cycle means up to 24 hours of data loss, violating the 1-hour RPO.
- **c. A cloud-provider snapshot backup strategy** — **Incorrect**: Snapshots are typically scheduled and may not meet a 1-hour RPO without PITR capability.
- **d. A replication-based backup strategy utilizing a delayed replica set** — **Incorrect**: A delayed replica introduces intentional lag, which can exceed the RPO window.

---

### Question 12
**A MongoDB database administrator configures a self-managed MongoDB deployment and needs to enable encryption at rest using MongoDB's native capability.**

Which storage engine should the administrator use to get built-in encryption at rest support?

- **a. WiredTiger with the encrypted storage engine option** — **Correct**: MongoDB Enterprise's WiredTiger Encrypted Storage Engine natively encrypts data files on disk.
- **b. WiredTiger with client-side field level encryption enabled** — **Incorrect**: CSFLE encrypts data in the application layer — it is not a storage engine option for at-rest encryption.
- **c. WiredTiger with TLS encryption configured on the network interface** — **Incorrect**: TLS encrypts data in transit, not at rest.
- **d. WiredTiger with encrypted audit logs enabled** — **Incorrect**: Encrypted audit logs protect audit records, not all data files at rest.

---

### Question 13
**What is the purpose of placing sort fields between equality and range fields in a compound index?**

- **a. To allow the index to satisfy the query's sort order without performing an in-memory sort** — **Correct**: The ESR (Equality, Sort, Range) rule positions sort fields so the index naturally provides the required order.
- **b. To ensure the sort field is always included in the index prefix for equality queries** — **Incorrect**: Index prefixes are based on equality fields, not sort fields.
- **c. To prevent the query planner from selecting the index for range-only queries** — **Incorrect**: The placement of sort fields does not prevent range-only query index usage.
- **d. To compress the sort field entries so the index consumes less disk space** — **Incorrect**: Field ordering in a compound index does not affect compression.

---

### Question 14
**A `logs` collection has a compound index `{ timestamp: 1, status: 1 }`. A query filters documents by a `timestamp` range and sorts results by `status`. A performance review shows an in-memory `SORT` stage.**

What change should the administrator make to eliminate it?

- **a. Recreate the index as `{ status: 1, timestamp: 1 }`** — **Correct**: Following the ESR rule, placing the sort field (`status`) before the range field (`timestamp`) allows the index to serve the sort order directly.
- **b. Recreate the index as `{ timestamp: -1, status: 1 }`** — **Incorrect**: Reversing the sort direction of `timestamp` does not fix the sort order mismatch for `status`.
- **c. Add a partial filter expression on `timestamp`** — **Incorrect**: Partial indexes limit which documents are indexed — they do not resolve in-memory sort issues.
- **d. Convert the index to a TTL index on `timestamp`** — **Incorrect**: TTL indexes expire documents — they do not improve sort performance.

---

### Question 15
**A team creates the compound index `{ category: 1, brand: 1, price: 1 }` on a `products` collection. A MongoDB database administrator needs to determine which query can use this index.**

Which query should the administrator identify as able to use the index?

- **a. A query filtering on `category` and `brand`** — **Correct**: This query uses the first two fields of the index prefix, which is a valid prefix match.
- **b. A query filtering on `brand` and `price` only** — **Incorrect**: Skipping `category` (the leading field) means the index prefix is not satisfied.
- **c. A query filtering on `price` only** — **Incorrect**: `price` is the third field; the index cannot be used without the leading fields.
- **d. A query filtering on `brand` only** — **Incorrect**: `brand` is not the leading field; the prefix is not satisfied.

---

### Question 16
**A development team maintains the compound index `{ tenantId: 1, userId: 1, eventType: 1 }` on an `audit` collection. A new reporting query filters on `tenantId` and `eventType` but not `userId`. A MongoDB database administrator reviews the query plan and finds the index is used but with a higher-than-expected number of keys examined.**

What should the administrator understand as the cause of this behavior?

- **a. The query includes `tenantId` but skips `userId`, so MongoDB scans all tenant entries before applying the `eventType` filter.** — **Correct**: Because `userId` is absent, MongoDB cannot narrow the scan within the tenant's index range and must examine all entries under that `tenantId` before filtering on `eventType`.
- **b. The query skips `tenantId`, forcing MongoDB to scan the entire index before applying any filter.** — **Incorrect**: The query does include `tenantId`, satisfying the index prefix.
- **c. Compound indexes cannot filter on non-adjacent fields, so `eventType` is evaluated as a post-scan filter.** — **Incorrect**: MongoDB can still use part of the index; it just performs extra key examination when a middle field is skipped.
- **d. The index does not support equality filters on `eventType` when `userId` is absent from the query.** — **Incorrect**: The index can still be used; the issue is efficiency, not capability.

---

### Question 17
**A MongoDB database administrator analyzes a slow query on a `customers` collection. The query planner output shows `COLLSCAN` rather than `IXSCAN`.**

Which architectural component should the administrator investigate as responsible for choosing between a collection scan and an index scan when executing a query?

- **a. The query planner, which evaluates available indexes and selects the execution plan** — **Correct**: The query planner selects the winning plan by evaluating candidate plans, including available indexes.
- **b. The WiredTiger storage engine, which decides whether to use a B-tree or heap scan** — **Incorrect**: WiredTiger executes the chosen plan — it does not make the planning decision.
- **c. The replication layer, which routes reads to the node with the lowest scan cost** — **Incorrect**: The replication layer handles data synchronization, not query planning.
- **d. The aggregation framework, which rewrites queries to avoid collection scans** — **Incorrect**: The aggregation framework processes pipelines — it does not rewrite `find()` queries.

---

### Question 18
**A MongoDB developer runs a query on a `products` collection and wants to verify whether the query uses an index. The developer calls `db.products.find({ sku: "ABC123" }).explain()`. The output shows `"stage": "COLLSCAN"` in the winning plan.**

What does this indicate to the developer?

- **a. The query scanned the entire collection because no suitable index was used.** — **Correct**: `COLLSCAN` means MongoDB examined every document in the collection — no index was used for this query.
- **b. The query used an index but fell back to a collection scan for tie-breaking.** — **Incorrect**: `COLLSCAN` in the winning plan means no index was used at all.
- **c. The query planner rejected all available indexes because the collection is too small.** — **Incorrect**: Collection size can influence plan selection, but `COLLSCAN` does not specifically indicate this reason.
- **d. The query executed successfully and the collection scan completed faster than the index scan.** — **Incorrect**: While a collection scan can sometimes win a plan race, the presence of `COLLSCAN` alone does not confirm this.

---

### Question 19
**A MongoDB database administrator configures a sharded cluster for an application that stores user activity events with a `timestamp` field. The team is deciding between hashed sharding and ranged sharding on `timestamp`.**

What is the reason the administrator should give for why hashed sharding produces more uniform data distribution?

- **a. Hashed sharding distributes documents using a hash of the shard key value, spreading inserts evenly across shards.** — **Correct**: Hashing the `timestamp` value randomizes its distribution, avoiding the hotspot problem that ranged sharding creates for monotonically increasing fields.
- **b. Hashed sharding reorders documents within each shard to balance chunk sizes automatically.** — **Incorrect**: Hashed sharding affects how documents are assigned to shards — it does not reorder documents within shards.
- **c. Ranged sharding on `timestamp` creates more chunks than hashed sharding, increasing balancer overhead.** — **Incorrect**: The number of chunks is not the primary reason for distribution differences.
- **d. Hashed sharding prevents the balancer from migrating chunks, reducing latency during write-heavy workloads.** — **Incorrect**: Hashed sharding does not prevent chunk migration.

---

### Question 20
**A MongoDB developer runs a query on a sharded `orders` collection that filters on a field not included in the shard key. A database administrator explains that this query will perform a scatter-gather operation.**

What happens to the developer's query during a scatter-gather operation in a sharded cluster?

- **a. The `mongos` router sends the query to all shards and merges the results before returning them to the client.** — **Correct**: Without a shard key filter, `mongos` cannot target a specific shard and must broadcast the query to all shards, then merge results.
- **b. The `mongos` router sends the query to the config servers, which identify the matching documents across shards.** — **Incorrect**: Config servers store metadata (chunk routing info) — they do not execute queries.
- **c. The `mongos` router forwards the query to the primary shard, which relays it to the remaining shards.** — **Incorrect**: `mongos` sends the query directly to all shards — it does not relay through the primary shard.
- **d. The `mongos` router buffers the query until the balancer completes any in-progress chunk migrations.** — **Incorrect**: `mongos` does not buffer queries waiting for the balancer.

---

### Question 21
**A MongoDB database administrator sets up monitoring for a MongoDB replica set. The team needs visibility into whether secondary nodes are keeping pace with the primary.**

Which metric should the administrator include in the monitoring strategy to track this?

- **a. Replication lag between the primary and each secondary** — **Correct**: Replication lag measures how far behind each secondary is from the primary, directly indicating whether secondaries are keeping pace.
- **b. The number of active client connections on each secondary node** — **Incorrect**: Connection counts do not indicate replication health.
- **c. The WiredTiger cache eviction rate on the primary node** — **Incorrect**: Cache eviction relates to memory pressure on the primary, not secondary replication state.
- **d. Replication oplog window length on the primary node** — **Incorrect**: The oplog window tells you how much history is available for recovery, not whether secondaries are caught up.

---

### Question 22
**A MongoDB database administrator monitors a replica set secondary using `db.serverStatus()` and needs to determine whether the node is caught up with the primary.**

Which section of the output should the administrator check for replication state information?

- **a. The `repl` section** — **Correct**: The `repl` section of `db.serverStatus()` output contains replication state information, including optime and lag details.
- **b. The `locks` section** — **Incorrect**: The `locks` section reports lock acquisition and wait statistics.
- **c. The `opcounters` section** — **Incorrect**: The `opcounters` section reports the rate of CRUD and command operations.
- **d. The `mem` section** — **Incorrect**: The `mem` section reports memory usage statistics.

---

### Question 23
**A MongoDB deployment runs complex aggregation pipelines that involve multiple `$group` and `$sort` stages on large collections. The MongoDB database administrator observes that the host system experiences sustained 100% utilization load during these jobs, causing all other database queries to slow down simultaneously.**

Which resource bottleneck should the administrator identify as affecting this deployment?

- **a. CPU saturation** — **Correct**: Complex `$group` and `$sort` stages are compute-intensive. Sustained 100% utilization during these operations indicates CPU saturation.
- **b. Disk I/O saturation** — **Incorrect**: Disk I/O saturation typically manifests as high read/write wait times, not CPU utilization at 100%.
- **c. Lock saturation** — **Incorrect**: Lock contention causes specific operations to wait, not a sustained 100% overall load.
- **d. Network saturation** — **Incorrect**: Network saturation affects data transfer rates and latency, not CPU utilization.

---

### Question 24
**A MongoDB database administrator designs the access control model for a new MongoDB deployment. A security policy requires applying the principle of least privilege to all database users.**

What does the principle of least privilege mean in the context of the administrator's MongoDB deployment?

- **a. Each user should be granted only the permissions required to perform the user's specific tasks.** — **Correct**: The principle of least privilege limits access rights for accounts to only what is strictly required.
- **b. Each user should be granted the lowest available built-in role, regardless of the user's actual responsibilities.** — **Incorrect**: Least privilege is about matching permissions to responsibilities — not blindly assigning the lowest role.
- **c. Each user should authenticate using the authentication mechanism with the fewest configuration steps.** — **Incorrect**: The principle of least privilege applies to authorization (permissions), not authentication mechanisms.
- **d. Each user should have access only to the collections created after the user's account was provisioned.** — **Incorrect**: Least privilege is about the scope of access, not the timing of collection creation.

---

### Question 25
**A development team creates a MongoDB service account for an application that only generates read-only reports. A developer assigns the built-in `dbOwner` role to this account on the target database.**

Why does assigning the `dbOwner` role violate the principle of least privilege for this application?

- **a. It grants write, collection modification, and administrative privileges that exceed the application's required read-only access.** — **Correct**: `dbOwner` combines `readWrite`, `dbAdmin`, and `userAdmin` — far more than the read-only access required.
- **b. It grants global read and write access to all databases across the entire MongoDB deployment.** — **Incorrect**: `dbOwner` is scoped to the target database, not all databases (that would be `root` or `dbOwnerAnyDatabase`).
- **c. It grants cluster-wide administrative privileges, allowing the application to modify replication and sharding configurations.** — **Incorrect**: Cluster-wide privileges are granted by roles like `clusterAdmin`, not `dbOwner`.
- **d. It bypasses Network Isolation rules, allowing the service account to connect from unauthorized IP addresses.** — **Incorrect**: `dbOwner` does not affect network access controls.

---

### Question 26
**A MongoDB administrator is asked to add the privilege `find` to a role named `myrole`.**

What is the correct syntax for adding this privilege to the role in MongoDB?

- **a.**
  ```javascript
  db.runCommand({
    grantPrivilegesToRole: "myRole",
    privileges: {
      resource: { db: "testDB", collection: "" },
      actions: ["find"]
    },
    writeConcern: { w: "majority" },
    comment: "Granting find action"
  })
  ```
  — **Incorrect**: The `privileges` field must be an **array**, not a single object.

- **b.**
  ```javascript
  db.runCommand({
    grantPrivilegesToRole: "myRole",
    privileges: [
      {
        resource: { db: "testDB", collection: "" },
        actions: ["find"]
      }
    ],
    writeConcern: { w: "majority" },
    comment: "Granting find action"
  })
  ```
  — **Correct**: `privileges` is correctly provided as an array with a valid `resource` object and an `actions` array.

- **c.**
  ```javascript
  db.runCommand({
    grantPrivilegesToRole: "myRole",
    privileges: [
      {
        db: "testDB",
        collection: "",
        actions: ["find"]
      }
    ],
    writeConcern: { w: "majority" },
    comment: "Granting find action"
  })
  ```
  — **Incorrect**: The privilege object must have a nested `resource` field — `db` and `collection` cannot be placed at the top level.

- **d.**
  ```javascript
  db.runCommand({
    grantPrivilegesToRole: "myRole",
    privileges: [
      {
        resource: { db: "testDB", collection: "" },
        actions: "find"
      }
    ],
    writeConcern: { w: "majority" },
    comment: "Granting find action"
  })
  ```
  — **Incorrect**: The `actions` field must be an **array**, not a plain string.

---

### Question 27
**A MongoDB administrator manages an e-commerce platform. The platform has a `sales` database that includes the role `DailyReport` and three collections: `orders`, `customers`, `products`.**

**The `DailyReport` role is currently used to generate reports on the `orders` collection. This role also needs access to the `products` collection in order to query products.**

What option should the administrator use to grant the correct access to the `DailyReport` role?

- **a.**
  ```javascript
  use sales
  db.runCommand({
    grantPrivilegesToRole: "DailyReport",
    privileges: [
      {
        resource: { db: "sales", collection: "products" },
        actions: ["find"]
      }
    ]
  })
  ```
  — **Correct**: Uses the correct `sales` database context and grants only `find` on `products` — the minimum required access.

- **b.**
  ```javascript
  use system
  db.runCommand({
    grantPrivilegesToRole: "DailyReport",
    privileges: [
      {
        resource: { db: "sales", collection: "products" },
        actions: ["find"]
      }
    ]
  })
  ```
  — **Incorrect**: The `system` database is not the correct context. The command must be run against the `sales` database.

- **c.**
  ```javascript
  use sales
  db.runCommand({
    grantPrivilegesToRole: "DailyReport",
    privileges: [
      {
        resource: { db: "sales", collection: "products" },
        actions: ["create"]
      },
      {
        resource: { db: "sales", collection: "orders" },
        actions: ["find"]
      }
    ]
  })
  ```
  — **Incorrect**: Grants `create` on `products` (unnecessary) and redundantly adds `find` on `orders` (already granted).

- **d.**
  ```javascript
  use sales
  db.runCommand({
    grantPrivilegesToRole: "DailyReportv2",
    privileges: [
      {
        resource: { db: "sales", collection: "products" },
        actions: ["find"]
      },
      {
        resource: { db: "sales", collection: "orders" },
        actions: ["find"]
      }
    ]
  })
  ```
  — **Incorrect**: Targets `DailyReportv2` — a different role — instead of the existing `DailyReport` role.

---

### Question 28
**The `students` collection includes the following sample documents:**

```javascript
{ "_id": 1, "name": "Alice", "grade": "A", "age": 14, "class": "Math" }
{ "_id": 2, "name": "Bob",   "grade": "B", "age": 15, "class": "Math" }
```

**A MongoDB administrator performs the following query:**

```javascript
db.students.find({ class: "Math", age: 14 }).explain("executionStats")
```

**The output includes:**

```json
{
  "winningPlan": {
    "stage": "FETCH",
    "inputStage": {
      "stage": "IXSCAN",
      "keyPattern": { "class": 1 },
      "indexName": "class_1"
    }
  },
  "executionStats": {
    "nReturned": 1,
    "totalKeysExamined": 3,
    "totalDocsExamined": 3
  }
}
```

What action should the administrator take to further optimize the query?

- **a. Compare the values of `nReturned`, `totalDocsExamined`, and `totalKeysExamined`.** — **Correct**: `nReturned` (1) vs `totalDocsExamined` (3) reveals inefficiency. A well-optimized query has these values close to 1:1. Adding `age` to the index (e.g., `{ class: 1, age: 1 }`) would reduce unnecessary document fetches.
- **b. Compare the `stage` fields and confirm that it is not using an index.** — **Incorrect**: The output shows `IXSCAN`, confirming an index **is** being used.
- **c. Review the value of the `direction` field.** — **Incorrect**: The `direction` field (forward/backward) indicates traversal direction — it does not identify optimization opportunities here.
- **d. Observe the `indexFilterSet` value and the use of an index.** — **Incorrect**: `indexFilterSet: false` just indicates no index filter was applied; it does not point to the optimization opportunity.

---

### Question 29
**A department store maintains a `clothing` collection containing:**

```javascript
{ "_id": 1, "item": "sweater", "sizes": ["XS", "S", "L"] }
{ "_id": 2, "item": "t-shirt", "sizes": ["S", "M", "L"] }
{ "_id": 3, "item": "vest",    "sizes": ["M", "L", "XL", "XXL"] }
```

**An employee runs the following query to know which items come in size medium:**

```javascript
db.clothing.find({ "sizes": "M" })
```

Which index should improve performance for this query the MOST?

- **a. `db.clothing.createIndex({ sizes: 1 })`** — **Correct**: A multikey index on the `sizes` array field directly supports this query with an `IXSCAN`.
- **b. `db.clothing.createIndex({ item: 1, sizes: 1 })`** — **Incorrect**: `item` is not in the query filter; the leading field wastes the index prefix.
- **c. `db.clothing.createIndex({ _id: 1, sizes: 1 })`** — **Incorrect**: `_id` is already indexed and not part of this query; `sizes` at position 2 cannot be used as a prefix.
- **d. `db.clothing.createIndex({ item: -1 })`** — **Incorrect**: This indexes `item` only, which is not referenced in the query.

---

### Question 30
**What is the purpose of the oplog?**

- **a. A rolling record of all queries ran on the databases** — **Incorrect**: The oplog does not record queries — only write operations.
- **b. A rolling record of all operations that modify the data stored in your databases** — **Correct**: The oplog is a capped collection that records all data-modifying operations, used by secondaries to replicate changes from the primary.
- **c. A rolling record of all indexes stored in your databases** — **Incorrect**: Index definitions are stored in collection metadata, not the oplog.
- **d. A rolling record of all operations and users stored in your databases** — **Incorrect**: The oplog records write operations only — not user definitions or queries.
