# MongoDB CRUD Operations: Modifying Query Results

---

## Lesson 1: Sorting and Limiting Query Results in MongoDB

### Code Summary: Sorting and Limiting Query Results in MongoDB
Review the following code, which demonstrates how to sort and limit query results.

#### Sorting Results
Use `cursor.sort()` to return query results in a specified order. Within the parentheses of `sort()`, include an object that specifies the field(s) to sort by and the order of the sort. Use `1` for ascending order, and `-1` for descending order.

**Syntax:**
```javascript
db.collection.find(<query>).sort(<sort>)
```

**Example:**
```javascript
// Return data on all music companies, sorted alphabetically from A to Z.
db.companies.find({ category_code: "music" }).sort({ name: 1 });
```

To ensure documents are returned in a consistent order, include a field that contains unique values in the sort. An easy way to do this is to include the `_id` field in the sort. Here's an example:

```javascript
// Return data on all music companies, sorted alphabetically from A to Z. Ensure consistent sort order
db.companies.find({ category_code: "music" }).sort({ name: 1, _id: 1 });
```

#### Limiting Results
Use `cursor.limit()` to specify the maximum number of documents the cursor will return. Within the parentheses of `limit()`, specify the maximum number of documents to return.

**Syntax:**
```javascript
db.companies.find(<query>).limit(<number>)
```

**Example:**
```javascript
// Return the three music companies with the highest number of employees. Ensure consistent sort order.
db.companies
  .find({ category_code: "music" })
  .sort({ number_of_employees: -1, _id: 1 })
  .limit(3);
```

---

### Question 1
**Using the `inspections` collection within the `sample_training` database, you need to find all inspections that were passed. Your manager has requested that you organize this data by the certificate number in ascending order. Which query should you use?** *(Select one)*

- **Option A**: `db.inspections.find({ result: "Pass" }).sort({ certificate_number: 1 });`
- **Option B**: `db.inspections.find({ result: "Pass" }).sort({ certificate_number: -1 });`
- **Option C**: `db.inspections.find({ result: "Pass" }, { sort: { certificate_number: 1 } })`
- **Option D**: `db.inspections.find({ result: "Pass" }, { sort: { certificate_number: -1 } })`

Options:
- **a. Option A**
  - **Correct**: This query will return documents for businesses that passed inspection ordered by the certificate number in ascending order.

- **b. Option B**
  - **Incorrect**: This query will return documents for businesses that passed inspection ordered by the certificate number in descending order. You need to sort by certificate number in ascending order.

- **c. Option C**
  - **Incorrect**: Sort is a cursor method, so it must be appended to the end of the query. This query will not return documents that have passed inspection ordered by certificate number in ascending order.

- **d. Option D**
  - **Incorrect**: Sort is a cursor method, so it must be appended to the end of the query. This query will not return documents that have passed inspection ordered by certificate number in ascending order.

---

### Question 2
**You are considering creating a new membership tier for your bike sharing service for users who take long trips. Using the `trips` collection within the `sample_training` database, you need to find the trips, taken by subscribers, with the longest trip duration. Return the top 5 results in descending order. Which query should you use?** *(Select one)*

- **Option A**: `db.trips.find({ usertype: "Subscriber" }, { sort: { tripduration: -1 } }, { limit: 5 })`
- **Option B**: `db.trips.find({ usertype: "Subscriber" }, { sort: { tripduration: 1 } }, { limit: 5 })`
- **Option C**: `db.trips.find({ usertype: "Subscriber" }).sort({ tripduration: 1 }).limit(5)`
- **Option D**: `db.trips.find({ usertype: "Subscriber" }).sort({ tripduration: -1 }).limit(5)`

Options:
- **a. Option A**
  - **Incorrect**: This query will return a syntax error. Remember to append `sort()` and `limit()` to the `find()` method.

- **b. Option B**
  - **Incorrect**: This query will return a syntax error. Remember to append `sort()` and `limit()` to the `find()` method.

- **c. Option C**
  - **Incorrect**: This query will return the 5 documents with the shortest trip duration, completed by subscribers, in ascending order. You need to return documents with the longest trip durations sorted in descending order.

- **d. Option D**
  - **Correct**: This query will return the 5 documents with the longest trip durations, completed by subscribers, in descending order.

---

## Lesson 2: Returning Specific Data from a Query in MongoDB

### Code Summary: Returning Specific Data from a Query in MongoDB
Review the following code, which demonstrates how to return selected fields from a query.

#### Add a Projection Document
To specify fields to include or exclude in the result set, add a projection document as the second parameter in the call to `db.collection.find()`.

**Syntax:**
```javascript
db.collection.find(<query>, <projection>)
```

#### Include a Field
To include a field, set its value to `1` in the projection document.

**Syntax:**
```javascript
db.collection.find(<query>, { <field>: 1 })
```

**Example:**
```javascript
// Return all restaurant inspections - business name, result, and _id fields only
db.inspections.find(
  { sector: "Restaurant - 818" },
  { business_name: 1, result: 1 }
)
```

#### Exclude a Field
To exclude a field, set its value to `0` in the projection document.

**Syntax:**
```javascript
db.collection.find(<query>, { <field>: 0, <field>: 0 })
```

**Example:**
```javascript
// Return all inspections with result of "Pass" or "Warning" - exclude date and zip code
db.inspections.find(
  { result: { $in: ["Pass", "Warning"] } },
  { date: 0, "address.zip": 0 }
)
```

While the `_id` field is included by default, it can be suppressed by setting its value to `0` in any projection:

```javascript
// Return all restaurant inspections - business name and result fields only
db.inspections.find(
  { sector: "Restaurant - 818" },
  { business_name: 1, result: 1, _id: 0 }
)
```

---

### Question 1
**Which of the following statements are true about a projection document?** *(Select all that apply)*

- **a. We can include fields in our results by setting their values to 1 in the projection document.**
  - **Correct**: We can include fields in our results by setting their values to 1 in the projection document.

- **b. We can exclude fields from our results by setting their values to 0 in the projection document.**
  - **Correct**: We can exclude fields from our results by setting their values to 0 in the projection document.

- **c. We can either include or exclude fields in the results, but not both. The `_id` field is the exception to this rule.**
  - **Correct**: We can either include or exclude fields in the results, but not both. However, the `_id` field is the exception to this rule.

- **d. Inclusion and exclusion statements, not including `_id` statements, can be combined with each other in a projection document.**
  - **Incorrect**: We cannot combine inclusion and exclusion statements with each other in a projection document. However, the `_id` field is the exception to this rule.

---

### Question 2
**If we don't want to return the `_id` field, we can add it to the projection document and set it to which of the following values?** *(Select all that apply)*

- **a. `0`**
  - **Correct**: Setting the `_id` field value to 0 will exclude that field from the results.

- **b. `1`**
  - **Incorrect**: Setting a field value to 1 will include that field in the results.

- **c. `-1`**
  - **Incorrect**: -1 is not a valid value for projection.

- **d. None of the above**
  - **Incorrect**: We must set the `_id` field to a numerical value (0) in the projection document to exclude it.

---

## Lesson 3: Counting Documents in a MongoDB Collection

### Code Summary: Counting Documents in a MongoDB Collection
Review the following code, which demonstrates how to count the number of documents that match a query.

#### Count Documents
Use `db.collection.countDocuments()` to count the number of documents that match a query. `countDocuments()` takes two parameters: a query document and an options document.

**Syntax:**
```javascript
db.collection.countDocuments(<query>, <options>)
```

The query selects the documents to be counted.

**Examples:**
```javascript
// Count number of docs in trip collection
db.trips.countDocuments({})

// Count number of trips over 120 minutes by subscribers
db.trips.countDocuments({ tripduration: { $gt: 120 }, usertype: "Subscriber" })
```

---

### Question 1
**Which of the following statements are true about the `countDocuments()` collection method?** *(Select all that apply)*

- **a. The method takes a query parameter, which selects the documents to be counted.**
  - **Correct**: The correct syntax for `.countDocuments()` is `db.collection.countDocuments(<query>)`.

- **b. We can use the method to count all documents in a collection.**
  - **Correct**: We use `countDocuments()` with an empty document in the query parameter to count all documents in a collection.

- **c. The method does not support the use of operators in queries that are passed as a parameter.**
  - **Incorrect**: The `.countDocuments()` method accepts queries that use operators, like `$elemMatch` or `$lt`.

---

### Question 2
**What can we expect to be returned by running `db.inspections.countDocuments({})`?** *(Select one)*

- **a. This command doesn't return anything because it requires a query parameter.**
  - **Incorrect**: We use `countDocuments()` with an empty document in the query parameter to return the total number of documents in a collection.

- **b. This command returns the total number of documents in the `inspections` database.**
  - **Incorrect**: `countDocuments()` returns the total number of documents in the specified collection, not the entire database.

- **c. This command returns the total number of documents in the `inspections` collection.**
  - **Correct**: We use `countDocuments()` with an empty document in the query parameter to return the total number of documents in a collection.

---

## Conclusion

### MongoDB CRUD Operations: Modifying Query Results
In this unit, you learned how to modify query results with MongoDB. Specifically, you learned how to:
- Return query results in a specified order by using `cursor.sort()`.
- Constrain the number of results returned by using `cursor.limit()`.
- Specify fields to return by adding a projection document parameter in calls to `db.collection.find()`.
- Count the number of documents that match a query by using `db.collection.countDocuments()`.

### Resources
Use the following resources to learn more about modifying query results in MongoDB:

- **Lesson 01: Sorting and Limiting Query Results in MongoDB**
  - MongoDB Docs: `cursor.sort()`
  - MongoDB Docs: `cursor.limit()`
- **Lesson 02: Returning Specific Data from a Query in MongoDB**
  - MongoDB Docs: Project Fields to Return from Query
  - MongoDB Docs: Projection Restrictions
- **Lesson 03: Counting Documents in a MongoDB Collection**
  - MongoDB Docs: `db.collection.countDocuments()`
