# MongoDB Indexes II

---

## Lesson 1: How Indexes Work

### Question 1
**Which of the following statements describe a B-tree?** *(Select all that apply)*

- **a. B-trees sort the stored data in ascending sequential order from left to right.**
  - **Correct**: MongoDB stores the values of indexed fields using an optimized data structure known as a B-tree. B-trees sort their stored data in ascending sequential order, from left to right.

- **b. Nodes in a B-tree can have more than two child nodes.**
  - **Correct**: MongoDB stores the values of indexed fields using an optimized data structure known as a B-tree. Nodes in a B-tree can have more than two child nodes.

- **c. B-tree stands for Binary tree.**
  - **Incorrect**: B-trees are self-balancing tree data structures, not binary trees.

- **d. A B-tree is a self-balancing tree data structure.**
  - **Correct**: MongoDB stores the values of indexed fields using an optimized data structure known as a B-tree. B-trees are self-balancing tree data structures.

---

### Question 2
**Which index would most effectively support the following query?** *(Select one)*

```javascript
db.collection.find({
  username: "j0hnny",
  timestamp: {
    $gte: ISODate("2021-05-18T00:00:00.000Z"),
    $lt: ISODate("2021-05-18T13:00:00.000Z")
  }
})
```

- **Option A**: `db.collection.createIndex({ username: 1, timestamp: 1 })`
- **Option B**: `db.collection.createIndex({ timestamp: 1, username: 1 })`
- **Option C**: `db.collection.createIndex({ username: 1 })`
- **Option D**: `db.collection.createIndex({ timestamp: 1 })`

Options:
- **a. Option A**
  - **Correct**: `db.collection.createIndex({ username: 1, timestamp: 1 })` is the most effective index for this query. This is because our equality match (`username`) is the index prefix, so MongoDB can quickly filter out documents that don't match before applying the range query (`timestamp`).

- **b. Option B**
  - **Incorrect**: `db.collection.createIndex({ timestamp: 1, username: 1 })` is not the most effective index for the given query. This index results in more index keys being scanned than documents returned because the range query is the index prefix when an equality match exists in the query.

- **c. Option C**
  - **Incorrect**: `db.collection.createIndex({ username: 1 })` will reduce the amount of documents scanned, but creating a compound index with the `timestamp` field provides better optimization.

- **d. Option D**
  - **Incorrect**: `db.collection.createIndex({ timestamp: 1 })` will reduce the amount of documents scanned, but creating a compound index with the `username` field provides better optimization.

---

### Question 3
**Which index would most effectively support the following query?** *(Select one)*

```javascript
db.collection.find({
  timestamp: {
    $gte: ISODate("2021-05-18T00:00:00.000Z"),
    $lt: ISODate("2021-05-19T00:00:00.000Z")
  },
  username: "j0hnny"
}).sort({ rating: 1 })
```

- **Option A**: `db.collection.createIndex({ timestamp: 1, username: 1, rating: 1 })`
- **Option B**: `db.collection.createIndex({ username: 1, rating: 1, timestamp: 1 })`
- **Option C**: `db.collection.createIndex({ rating: 1, timestamp: 1, username: 1 })`
- **Option D**: `db.collection.createIndex({ rating: 1, username: 1, timestamp: 1 })`

Options:
- **a. Option A**
  - **Incorrect**: MongoDB will have to perform an in-memory sort to return the correct results because the range filter breaks the index sort ordering before reaching the sort field.

- **b. Option B**
  - **Correct**: Following the **ESR Rule** (Equality, Sort, Range), `username` is Equality, `rating` is Sort, and `timestamp` is Range (`{ username: 1, rating: 1, timestamp: 1 }`). Putting equality first filters out non-matching documents, and putting sort second satisfies the sort without an in-memory blocking sort.

- **c. Option C**
  - **Incorrect**: This index places the sort field first, which prevents avoiding an in-memory sort in subsequent query execution stages.

- **d. Option D**
  - **Incorrect**: Placing the sort field first is inefficient compared to putting equality fields first.

---

## Lesson 2: Index Usage Details via Explain

### Code Summary: Index Usage Details via Explain
To use the `explain()` method in its default mode, pass in the `"queryPlanner"` parameter, or invoke the method with no parameter:

```javascript
db.collection.explain("queryPlanner").find({ timestamp: { $gt: 2 }, isActivated: true })

// Or using default:
db.collection.explain().find({ timestamp: { $gt: 2 }, isActivated: true })
```

To extract specific details using JavaScript dot notation:
```javascript
// Return only winningPlan
db.collection.find({ timestamp: { $gt: 2 }, isActivated: true })
  .explain().queryPlanner.winningPlan

// Return only rejectedPlans
db.collection.find({ timestamp: { $gt: 2 }, isActivated: true })
  .explain().queryPlanner.rejectedPlans
```

Use `executionStats` mode to view execution statistics (e.g. `nReturned`, `totalKeysExamined`, `totalDocsExamined`, and `executionTimeMillis`):
```javascript
db.collection.explain("executionStats").find({
  timestamp: { $gt: 2 }, isActivated: true
}).sort({ rating: -1 })
```

Use `allPlansExecution` mode to inspect scores evaluated for each candidate plan:
```javascript
db.collection.explain("allPlansExecution").find({
  timestamp: { $gt: 2 }, isActivated: true
})
```

---

### Question 1
**From the following `explain` output, select the option that describes what the `explain` output is telling us:** *(Select one)*

```javascript
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'sample_airbnb.listingsAndReviews',
    indexFilterSet: false,
    parsedQuery: {},
    queryHash: 'DD1CE27D',
    planCacheKey: 'DD1CE27D',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'SORT',
      sortPattern: { host: -1 },
      memLimit: 104857600,
      type: 'simple',
      inputStage: { stage: 'COLLSCAN', direction: 'forward' }
    },
    rejectedPlans: []
  },
  executionStats: {
    executionSuccess: true,
    nReturned: 5555,
    executionTimeMillis: 256,
    totalKeysExamined: 0,
    totalDocsExamined: 5555,
    executionStages: {
      stage: 'SORT',
      nReturned: 5555,
      executionTimeMillisEstimate: 130,
      works: 11113,
      advanced: 5555,
      needTime: 5557,
      needYield: 0,
      saveState: 13,
      restoreState: 13,
      isEOF: 1,
      sortPattern: { host: -1 },
      memLimit: 104857600,
      type: 'simple',
      totalDataSizeSorted: 100493513,
      usedDisk: false,
      spills: 0,
      inputStage: {
        stage: 'COLLSCAN',
        nReturned: 5555,
        executionTimeMillisEstimate: 0,
        works: 5557,
        advanced: 5555,
        needTime: 1,
        needYield: 0,
        saveState: 13,
        restoreState: 13,
        isEOF: 1,
        direction: 'forward',
        docsExamined: 5555
      }
    }
  },
  command: {
    find: 'listingsAndReviews',
    filter: {},
    sort: { host: -1 },
    '$db': 'sample_airbnb'
  },
  ok: 1
}
```

- **a. The `explain` method was used in the `allPlansExecution` mode. An index on the `host` field was used.**
  - **Incorrect**: The output contains an `executionStats` object, not `allPlansExecution`. Also, no index was used (`COLLSCAN`).

- **b. The `explain` method was used in the default `queryPlanner` mode. The winning plan was an in-memory `SORT` stage and the query wasn't supported by an index.**
  - **Incorrect**: The output includes full `executionStats`, so it was run in `executionStats` mode.

- **c. The `explain` method was used in the `executionStats` mode. The query was not supported by an index. The winning plan was `SORT`, requiring an in-memory sort to return the results in order.**
  - **Correct**: The output displays `executionStats` metrics, shows `totalKeysExamined: 0` and `stage: 'COLLSCAN'`, indicating that no index was used and an in-memory `SORT` stage was required.

- **d. The `explain` method was used in the `executionStats` mode. The query was supported by an index on the `host` field.**
  - **Incorrect**: `totalKeysExamined: 0` and `COLLSCAN` confirm that no index was used.

---

### Question 2
**Which of the following fields can help us determine the effectiveness of an index?** *(Select all that apply)*

- **a. `nReturned`**
  - **Correct**: `nReturned` shows the number of documents matching the query condition. Comparing `nReturned` against `totalDocsExamined` and `totalKeysExamined` helps gauge index efficiency.

- **b. `executionSuccess`**
  - **Incorrect**: `executionSuccess` only indicates whether the operation completed without error.

- **c. `totalDocsExamined`**
  - **Correct**: `totalDocsExamined` shows the number of documents read from collection storage during execution.

- **d. `executionStages`**
  - **Correct**: `executionStages` details whether stages like in-memory blocking `SORT` occurred.

- **e. `totalKeysExamined`**
  - **Correct**: `totalKeysExamined` shows how many index entries were scanned.

---

## Lesson 3: Optimized Compound Indexes

### Code Summary: Optimized Compound Indexes
Insert sample documents into `mongosh`:

```javascript
db.getSiblingDB("sample_game").users.insertMany([
  {
    _id: new ObjectId("6488bcfe84b99e26917f78b1"),
    dob: new Date("1987"),
    username: "testAccount",
    inactive: false,
    score: 800,
  },
  {
    _id: new ObjectId("6488bcfe84b99e26917f78b2"),
    dob: new Date("1988"),
    username: "exampleUser",
    inactive: false,
    score: 700,
  },
  {
    _id: new ObjectId("6488bcfe84b99e26917f78b3"),
    dob: new Date("1989"),
    username: "coolperson",
    inactive: true,
    score: 998,
  },
  {
    _id: new ObjectId("6488bcfe84b99e26917f78b4"),
    dob: new Date("1990"),
    username: "randomGuy",
    inactive: false,
    score: 500,
  },
])
```

Optimizing query with Equality, Sort, and Range (**ESR Rule**):
- **Query**:
  ```javascript
  db.users.find({
    dob: { $gte: new Date("1988"), $lte: new Date("1990") },
    inactive: false
  }).sort({ current_score: -1 })
  ```
- **Optimal Index Order**: Equality (`inactive`), Sort (`current_score`), Range (`dob`):
  ```javascript
  db.users.createIndex({ inactive: 1, current_score: 1, dob: 1 })
  ```

---

### Question 1
**The `SORT` stage will be present in the `executionStages` object of the `explain('executionStats')` output if a blocking (in-memory) sort took place.** *(Select one)*

- **a. True**
  - **Correct**: The presence of the `SORT` stage indicates that MongoDB sorted the documents in memory, which is computationally expensive.

- **b. False**
  - **Incorrect**: The `SORT` stage specifically denotes an in-memory blocking sort.

---

### Question 2
**You check the `executionStats` for a query using an index and see the following output:**

```json
{
  "executionSuccess": true,
  "nReturned": 2,
  "executionTimeMillis": 0,
  "totalKeysExamined": 3,
  "totalDocsExamined": 3
}
```

**Which of the following is true?** *(Select all that apply)*

- **a. MongoDB had to scan an extra document**
  - **Correct**: `totalDocsExamined` is 3 while `nReturned` is 2, meaning 1 extra document was scanned.

- **b. Two documents were returned**
  - **Correct**: `nReturned: 2` indicates two documents matched and were returned.

- **c. MongoDB had to scan an extra index key**
  - **Correct**: `totalKeysExamined` is 3 while `nReturned` is 2, meaning 1 extra index key was scanned.

- **d. This query is not using an index**
  - **Incorrect**: `totalKeysExamined: 3` shows that an index was used.

---

## Lesson 4: Wildcard Indexes

### Code Summary: Wildcard Indexes
Insert sample documents into `mongosh`:

```javascript
db.getSiblingDB("sample_products").products.insertMany([
  {
    _id: new ObjectId("64a36318574fd20cd8fb9798"),
    sku: 111,
    product_name: "Stereo Speakers",
    price: 100,
    stock: 5,
    product_attributes: { color: "black", size: "5x5x5", weight: "5lbs" },
  },
  {
    _id: new ObjectId("64a36318574fd20cd8fb9799"),
    sku: 121,
    product_name: "Bread",
    price: 2,
    stock: 50,
    product_attributes: {
      type: "white",
      calories: 100,
      weight: "24g",
      crust: "soft",
    },
  },
  {
    _id: new ObjectId("64a36318574fd20cd8fb979a"),
    sku: 131,
    product_name: "Milk",
    price: 3,
    stock: 20,
    product_attributes: {
      type: "2%",
      calories: 120,
      weight: "1L",
      brand: "Dairy Farmers",
    },
  },
])
```

#### Create a Wildcard Index
```javascript
// Index all sub-fields of product_attributes
db.products.createIndex({ "product_attributes.$**": 1 })

// Create wildcard index with inclusions/exclusions
db.products.createIndex(
  { "$**": 1 },
  { wildcardProjection: { _id: 1, stock: 0, price: 0 } }
)

// Compound Wildcard Index (MongoDB 7.0+)
db.products.createIndex({
  stock: 1, "product_attributes.$**": 1 
})
```

---

### Question 1
**Why should you use a wildcard index to support queries in a MongoDB collection instead of a regular index?** *(Select one)*

- **a. Wildcard indexes have a smaller storage footprint than regular indexes.**
  - **Incorrect**: Wildcard indexes index every matching sub-field, so they do not inherently have a smaller footprint.

- **b. Wildcard indexes make queries that use regular expressions more efficient.**
  - **Incorrect**: Wildcard indexes do not specifically optimize regex pattern queries.

- **c. Wildcard indexes can support queries against any field, even if that field is unknown at the time of querying.**
  - **Correct**: Wildcard indexes allow indexing arbitrary or dynamic sub-document fields.

- **d. Wildcard indexes allow for efficient querying against time-series data.**
  - **Incorrect**: Wildcard indexes are not designed for time-series data optimizations.

---

### Question 2
**Given the following query:**

```javascript
db.people.find({ "metadata.likes": "golfing", "metadata.age": 30 })
```

**Which of the following indexes would support all the fields in the query?** *(Select one)*

- **Option A**: `db.people.createIndex({ name: 1 })`
- **Option B**: `db.people.createIndex({ metadata: 1 })`
- **Option C**: `db.people.createIndex({ "metadata.likes": 1, "metadata.status": 1 })`
- **Option D**: `db.people.createIndex({ "metadata.$**": 1 })`

Options:
- **a. Option A**
  - **Incorrect**: `name` is not used in the query.

- **b. Option B**
  - **Incorrect**: A single-field index on `metadata` matches the entire object, not sub-field queries.

- **c. Option C**
  - **Incorrect**: Does not index `metadata.age`.

- **d. Option D**
  - **Correct**: `db.people.createIndex({ "metadata.$**": 1 })` indexes all sub-fields under `metadata`, supporting queries on `metadata.likes` and `metadata.age`.

---

## Lesson 5: Partial Indexes

### Code Summary: Partial Indexes
Create a partial index using `partialFilterExpression`:

```javascript
db.zips.createIndex(
  { state: 1 },
  { partialFilterExpression: { pop: { $gte: 10000 } } }
)
```

Verify index eligibility with `explain()`:
```javascript
// Uses partial index (matches filter pop >= 10000)
db.zips.find({ state: "CA", pop: { $gte: 10000 } })
  .explain().queryPlanner.winningPlan

// Does NOT use partial index (result set includes documents with pop < 10000)
db.zips.find({ state: "CA" })
  .explain().queryPlanner.winningPlan
```

---

### Question 1
**When should you use a partial index?** *(Select one)*

- **a. To index documents that match a specified filter document.**
  - **Correct**: Partial indexes index only documents in a collection that satisfy a specified filter expression (`partialFilterExpression`).

- **b. To index based on ranges of documents rather than individual documents.**
  - **Incorrect**: Partial indexes index individual documents matching a filter.

- **c. To support queries against time-series data.**
  - **Incorrect**: Partial indexes are not specifically targeted at time-series optimization.

- **d. To index a field that has a value of an array.**
  - **Incorrect**: Multikey indexes handle array fields.

---

### Question 2
**Given the following query:**

```javascript
db.zips.find({ state: "AZ", pop: { $gte: 20000 } })
```

**Which Partial index will support this query?** *(Select one)*

- **Option A**: `db.zips.createIndex({ state: 1 }, { partialFilterExpression: { pop: { $gte: 10000 } } })`
- **Option B**: `db.zips.createIndex({ state: 1 }, { partialFilterExpression: { pop: { $lte: 10000 } } })`
- **Option C**: `db.zips.createIndex({ state: 1 }, { partialFilterExpression: { pop: { $gte: 25000 } } })`

Options:
- **a. Option A**
  - **Correct**: The index filters documents where `pop >= 10000`. Since the query requests `pop >= 20000`, all query results are guaranteed to be in the index.

- **b. Option B**
  - **Incorrect**: Indexes documents where `pop <= 10000`, which excludes `pop >= 20000`.

- **c. Option C**
  - **Incorrect**: Indexes documents where `pop >= 25000`, which misses documents with `20000 <= pop < 25000`.

---

## Lesson 6: Sparse Indexes

### Code Summary: Sparse Indexes
Insert sample documents:

```javascript
db.getSiblingDB("sample_db").sparseExample.insertMany([
  {
    _id: new ObjectId("64920144bf3922c17f7181ca"),
    username: "coolUser",
    avatar_url: "https://api.multiavatar.com/coolUser.svg",
  },
  {
    _id: new ObjectId("64920144bf3922c17f7181cb"),
    username: "testUser",
    avatar_url: "https://api.multiavatar.com/testUser.svg",
  },
  {
    _id: new ObjectId("64920144bf3922c17f7181cc"),
    username: "anotherUser",
    avatar_url: "https://api.multiavatar.com/anotherUser.svg",
  },
  { _id: new ObjectId("64920173bf3922c17f7181cd"), username: "test" },
])
```

Create a sparse index:
```javascript
db.sparseExample.createIndex({ avatar_url: 1 }, { sparse: true })
```

---

### Question 1
**Which of the following statements about sparse indexes are true?** *(Select all that apply)*

- **a. Sparse indexes only create index entries for documents that have null or non-null values for the indexed field.**
  - **Correct**: Sparse indexes only index documents that contain the indexed field (including `null` values), ignoring documents where the field is completely absent.

- **b. Sparse indexes are used to support queries against documents that meet a specified filter expression.**
  - **Incorrect**: Indexes supporting arbitrary filter expressions are partial indexes.

- **c. Sparse indexes will not be chosen by the query planner if it means the query results will be incomplete.**
  - **Correct**: The query planner will reject a sparse index if using it would omit documents missing the indexed field.

- **d. Sparse indexes only create index entries for documents that have non-null values for the indexed field.**
  - **Incorrect**: Sparse indexes include `null` values as long as the field exists in the document.

---

### Question 2
**Given the following index:**

```javascript
db.collection.createIndex({ stock: 1 }, { sparse: true })
```

**Which document will be indexed?** *(Select one)*

- **Option A**: `{ sku: 131, product_name: "Milk", price: 3 }`
- **Option B**: `{ sku: 121, product_name: "Bread", price: 2, stock: 50 }`

Options:
- **a. Option A**
  - **Incorrect**: Missing the `stock` field, so it is omitted from the sparse index.

- **b. Option B**
  - **Correct**: Contains the `stock` field, so it will be included in the sparse index.

---

## Lesson 7: Clustered Indexes

### Question 1
**How does a clustered index in MongoDB differ from a regular index?** *(Select all that apply)*

- **a. Clustered indexes arrange documents in order based on their index key.**
  - **Correct**: Clustered collections store documents directly ordered by their index key, improving I/O performance for range queries.

- **b. Clustered indexes optimize query performance for a given field over regular indexes.**
  - **Incorrect**: Performance improvements stem from physical document ordering, not field-specific indexing algorithms.

- **c. Clustered indexes store the index key alongside the documents themselves.**
  - **Correct**: Document data is stored directly in the index structure, avoiding separate disk lookups.

- **d. Clustered index keys eliminate the need for an additional TTL (time to live) index.**
  - **Correct**: Clustered indexes can accept `expireAfterSeconds` directly during creation.

---

### Question 2
**When can we create a clustered index?** *(Select one)*

- **a. When creating the clustered collection**
  - **Correct**: Clustered indexes can only be created at collection creation time via `db.createCollection()`.

- **b. Anytime**
  - **Incorrect**: Cannot be added to an existing unclustered collection.

- **c. When dropping the clustered collection**
  - **Incorrect**: Dropping deletes the collection.

- **d. When creating secondary indexes**
  - **Incorrect**: Secondary indexes are separate.

---

### Question 3
**You run a query against a clustered collection, as shown below:**

```javascript
db.weather.find({ "metadata.sensorId": 5578 })
```

**The clustered collection has an internal clustered index and a secondary index that is eligible for the query. Which of the following two indexes will be automatically selected by the query planner to support the query?** *(Select one)*

- **a. Clustered index**
  - **Incorrect**: MongoDB's query planner will select the eligible secondary index unless hinted otherwise.

- **b. Secondary index**
  - **Correct**: The query optimizer selects the eligible secondary index by default. To force the internal clustered index, use `.hint()`.

---

## Lesson 8: Time Series Collections

### Code Summary: Time Series Collections
Create a time-series collection:

```javascript
db.createCollection("weather", {
  timeseries: {
    timeField: "timestamp",
    metaField: "metadata",
    granularity: "hours",
  },
})
```

Create secondary compound index on time-series collection:
```javascript
db.weather.createIndex({ "metadata.sensorId": 1, "timestamp": 1 })
```

---

### Question 1
**What is the correct definition of a time series collection?** *(Select one)*

- **a. Time series collections efficiently store time series data. In time series collections, writes are organized so that data from the same source is stored alongside other data points from a similar point in time.**
  - **Correct**: Time series collections automatically bucket and order data by source (`metaField`) and time (`timeField`).

- **b. Time series collections is a specialized collection that stores time-related data in multiple time zones for easy lookup.**
  - **Incorrect**: Time-series collections do not handle automatic timezone conversions.

- **c. Time series collections are collections of documents that are grouped together into a single bucket based on the total size of the documents.**
  - **Incorrect**: Bucketing is based on time range and metadata source, not document size limits.

- **d. Time series collections are fixed-size collections that support high-throughput operations that insert and retrieve documents based on insertion order.**
  - **Incorrect**: Fixed-size insertion-ordered collections are Capped Collections.

---

### Question 2
**What are the advantages of providing a `metaField` field when creating a time series collection?** *(Select one)*

```javascript
db.createCollection("stockprice", {
  timeseries: {
    timeField: "timestamp",
    metaField: "metadata",
    granularity: "seconds",
  },
})
```

- **a. Improves the efficiency of querying data that changes over time**
  - **Incorrect**: Bucketing optimization comes from `timeField` and collection structure.

- **b. Allows you to visualize the data using third-party tools**
  - **Incorrect**: `metaField` is not required for visualization tools.

- **c. Allows for better organization by attaching additional information directly to the data**
  - **Correct**: `metaField` acts as a unique metadata key (e.g. sensor ID, ticker symbol) to group related time series measurements into common buckets.

---

## Lesson 9: How to Monitor Indexes

### Code Summary: How to Monitor Indexes
Monitor index usage with `$indexStats`:

```javascript
db.customers.aggregate([{ $indexStats: {} }])
```

Configure Database Profiler:
```javascript
// Level 1: Log operations taking longer than 30 ms
db.setProfilingLevel(1, { slowms: 30 })

// Query system.profile collection for slow operations
db.system.profile.find({ op: "query", ns: "sample_airbnb.listingsAndReviews" })
  .sort({ ts: -1 })
  .limit(1)
```

---

### Question 1
**What will the following command return?** *(Select one)*

```javascript
db.customers.aggregate([{ $indexStats: {} }])
```

- **a. An array of integers, each one representing the score assigned to each index in the collection.**
  - **Incorrect**: Does not return scores.

- **b. An array of documents, each representing an index specification document.**
  - **Correct**: Returns detailed access statistics (`accesses.ops`, `accesses.since`), key pattern, index name, and host info for every index on the collection.

- **c. An object estimating how much has been saved by leveraging the existing indexes to support queries against the collection.**
  - **Incorrect**: Does not estimate financial or computational savings.

- **d. Suggested actions that can be taken on current mongod instances to improve performance.**
  - **Incorrect**: Suggested index actions are provided by MongoDB Atlas Performance Advisor, not `$indexStats`.

---

### Question 2
**What happens when the database profiler is enabled on a database?** *(Select one)*

- **a. Operations are captured and recorded inside the database under a capped collection named `system.profile`.**
  - **Correct**: Profiler logs query operations exceeding thresholds into `system.profile`.

- **b. A web server is enabled to support queries on the database.**
  - **Incorrect**: Profiler operates internally within `mongod`.

- **c. You'll receive suggestions for actions you can take on your database to improve performance.**
  - **Incorrect**: Profiler records execution metrics, not automated AI advice.

- **d. The MongoDB instance is profiled in order to find the source of out-of-memory errors.**
  - **Incorrect**: Profiler targets slow queries, not system memory crashes.

---

## Conclusion

### MongoDB Indexes II
In this unit, you learned how to:
- Identify which fields in `explain()` output indicate performance issues (e.g. `nReturned`, `totalDocsExamined`, `totalKeysExamined`).
- Set the verbosity level for `explain()` (e.g. `executionStats`, `allPlansExecution`).
- Run `explain()` on a query to determine if the query is using an index.
- Read `explain()` output and identify which index a query is using.
- Create an efficient compound index using the **ESR Rule** (*Equality, Sort, Range*).
- Create a wildcard index (`$**`).
- Create a partial index (`partialFilterExpression`).
- Create a sparse index (`sparse: true`).
- Create a clustered time-series index.
- Use `$indexStats` to view index operations and identify unused indexes in a collection.
- Enable profiling and set profiling levels using `db.setProfilingLevel()`.
- Use the Database Profiler (`system.profile` collection) to identify slow queries.

### Resources
Use the following resources to learn more about indexing in MongoDB:

- **Lesson 01: How Indexes Work**
  - Indexes in MongoDB
  - What is Indexing in a Database?
  - Compound Indexes
  - Index Best Practices

- **Lesson 02: Index Usage Details via Explain**
  - Explain Method
  - Explain Verbosity Levels
  - Explain Results
  - Query Plans

- **Lesson 03: Optimized Compound Indexes**
  - Compound Indexes
  - The ESR (Equality Sort Range) Rule
  - Tips and Tricks for Effective Indexing
  - Create Indexes to Support Your Queries

- **Lesson 04: Wildcard Indexes**
  - Wildcard Indexes in MongoDB
  - Create a Wildcard Index on All Fields
  - Include or Exclude Fields in a Wildcard Index
  - Wildcard Index Restrictions
  - Compound Wildcard Indexes (new in 7.0)

- **Lesson 05: Partial Indexes**
  - Partial Indexes in MongoDB
  - Partial Index Restrictions

- **Lesson 06: Sparse Indexes**
  - Sparse Indexes in MongoDB
  - Indexes that are Sparse by Default

- **Lesson 07: Clustered Indexes**
  - Clustered Collections in MongoDB
  - Clustered Index Reference
  - `expireAfterSeconds`
  - Clustered Collection Examples

- **Lesson 08: Time Series Collections**
  - Time Series
  - Create a Time-Series Collection
  - Add Secondary Indexes to Time-Series Collections
  - List Time-Series Collections in a Database
  - Set up Automatic Removal
  - Time Series Product Overview

- **Lesson 09: How to Monitor Indexes**
  - `$indexStats` Operator
  - MongoDB Database Profiler
  - Reading Profiler Output
  - `db.setProfilingLevel()`
  - Database Profiler Verbosity Levels
