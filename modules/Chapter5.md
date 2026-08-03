# MongoDB CRUD Operations: Insert and Find Documents

---

## Lesson 1: Inserting Documents in a MongoDB Collection

### Code Summary: Inserting Documents in a MongoDB Collection
Review the following code, which demonstrates how to insert a single document and multiple documents into a collection.

#### Insert a Single Document
Use `insertOne()` to insert a document into a collection. Within the parentheses of `insertOne()`, include an object that contains the document data. Here's an example:

```javascript
db.grades.insertOne({
  student_id: 654321,
  products: [
    {
      type: "exam",
      score: 90,
    },
    {
      type: "homework",
      score: 59,
    },
    {
      type: "quiz",
      score: 75,
    },
    {
      type: "homework",
      score: 88,
    },
  ],
  class_id: 550,
})
```

#### Insert Multiple Documents
Use `insertMany()` to insert multiple documents at once. Within `insertMany()`, include the documents within an array. Each document should be separated by a comma. Here's an example:

```javascript
db.grades.insertMany([
  {
    student_id: 546789,
    products: [
      {
        type: "quiz",
        score: 50,
      },
      {
        type: "homework",
        score: 70,
      },
      {
        type: "quiz",
        score: 66,
      },
      {
        type: "exam",
        score: 70,
      },
    ],
    class_id: 551,
  },
  {
    student_id: 777777,
    products: [
      {
        type: "exam",
        score: 83,
      },
      {
        type: "quiz",
        score: 59,
      },
      {
        type: "quiz",
        score: 72,
      },
      {
        type: "quiz",
        score: 67,
      },
    ],
    class_id: 550,
  },
  {
    student_id: 223344,
    products: [
      {
        type: "exam",
        score: 45,
      },
      {
        type: "homework",
        score: 39,
      },
      {
        type: "quiz",
        score: 40,
      },
      {
        type: "homework",
        score: 88,
      },
    ],
    class_id: 551,
  },
])
```

---

### Question 1
**What methods are available in MongoDB for inserting a single document?** *(Select one)*

- **a. `.insertOne()`**
  - **Correct**: The `insertOne()` method is a valid method that's included in the MongoDB Shell to insert a single document.

- **b. `.inserting()`**
  - **Incorrect**: This is not a valid method included in the MongoDB Shell.

- **c. `.InsertDocument()`**
  - **Incorrect**: This is not a valid method included in the MongoDB Shell.

- **d. `.insertMany()`**
  - **Incorrect**: The `insertMany()` method is used to insert multiple documents.

---

### Question 2
**What methods are available in MongoDB for inserting multiple documents?** *(Select one)*

- **a. `.InsertDocument()`**
  - **Incorrect**: The `InsertDocument()` method is not a valid MongoDB method to insert documents.

- **b. `.inserting()`**
  - **Incorrect**: `inserting()` is not a valid method included in the MongoDB Shell.

- **c. `.insertOne()`**
  - **Incorrect**: The `insertOne()` method is a valid method included in the MongoDB Shell, but it's used to insert a single document.

- **d. `.insertMany()`**
  - **Correct**: The `insertMany()` method is a valid method that's included in the MongoDB Shell to insert multiple documents.

---

## Lesson 2: Finding Documents in a MongoDB Collection

### Code Summary: Finding Documents in a MongoDB Collection
Review the following code, which demonstrates how to query documents in MongoDB.

#### Find a Document with Equality
When given equality with an `_id` field, the `find()` command will return the specified document that matches the `_id`. Here's an example:

```javascript
db.zips.find({ _id: ObjectId("5c8eccc1caa187d17ca6ed16") })
```

#### Find a Document by Using the $in Operator
Use the `$in` operator to select documents where the value of a field equals any value in the specified array. Here's an example:

```javascript
db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } })
```

---

### Question 1
**What methods are available in MongoDB for finding documents?** *(Select one)*

- **a. `.find()`**
  - **Correct**: The `find()` method is a valid method that's included in the MongoDB Shell to find documents.

- **b. `.query()`**
  - **Incorrect**: This is not a valid method included in the MongoDB Shell.

- **c. `.finding()`**
  - **Incorrect**: This is not a valid method included in the MongoDB Shell.

- **d. `.search()`**
  - **Incorrect**: This is not a valid method included in the MongoDB Shell.

---

### Question 2
**You are searching for data on a small area in downtown Chicago with the following zip codes:**
- "60601"
- "60602"
- "60603"
- "60604"
- "60605"
- "60606"

**Which of the following query documents should you use to ensure that only the documents with the specified zip codes are returned?** *(Select one)*

- **a. `{ zip: { $nin : [ "60601", "60602", "60603", "60604", "60605", "60606"] } }`**
  - **Incorrect**: The `$nin` operator returns documents that do not contain the values specified in the array. Including this query would not return the specified Chicago zip codes.

- **b. `{ zip: { $in : [ "60601", "60602", "60603", "60604", "60605", "60606"] } }`**
  - **Correct**: The `$in` operator returns documents that contain the values specified in the array. This query will return the specified Chicago zip codes.

- **c. `{ zip: { $eq : "60601", "60602", "60603", "60604", "60605", "60606" } }`**
  - **Incorrect**: This syntax is inaccurate because the `$eq` operator matches documents that contain only one specified value.

- **d. `{ zip: "60601", "60602", "60603", "60604", "60605", "60606" }`**
  - **Incorrect**: This syntax is inaccurate because the implicit equality operator matches documents that contain only one specified value.

---

## Lesson 3: Finding Documents by Using Comparison Operators

### Code Summary: Finding Documents by Using Comparison Operators
Review the following comparison operators: `$gt`, `$lt`, `$lte`, and `$gte`.

- **`$gt`**: Use the `$gt` operator to match documents with a field greater than the given value. For example:
  ```javascript
  db.sales.find({ "items.price": { $gt: 50} })
  ```
- **`$lt`**: Use the `$lt` operator to match documents with a field less than the given value. For example:
  ```javascript
  db.sales.find({ "items.price": { $lt: 50} })
  ```
- **`$lte`**: Use the `$lte` operator to match documents with a field less than or equal to the given value. For example:
  ```javascript
  db.sales.find({ "customer.age": { $lte: 65} })
  ```
- **`$gte`**: Use the `$gte` operator to match documents with a field greater than or equal to the given value. For example:
  ```javascript
  db.sales.find({ "customer.age": { $gte: 65} })
  ```

---

### Question 1
**Your company is conducting research on the customer experience and is focused on identifying unsatisfied customers. You need to find all customers with a satisfaction rating of 1 or 2.**

**Which of the following query documents would return all customers with a satisfaction rating of 1 or 2?** *(Select one)*

- **a. `{ "customer.satisfaction" : { $gt : 1 } }`**
  - **Incorrect**: This query would return all documents with a customer satisfaction rating greater than 1. To solve this problem, you need to select a query that returns all documents with a customer satisfaction rating less than or equal to 2.

- **b. `{ customer.satisfaction : { $lte : 2 } }`**
  - **Incorrect**: This syntax is incorrect. Remember, to access subdocuments, you must use the syntax `"field.nestedfield"`, which includes quotation marks.

- **c. `{ "customer.satisfaction" : { $lt : 2 } }`**
  - **Incorrect**: This query would return all documents with a customer satisfaction rating less than 2. To solve this problem, you need to select a query that returns all documents with a customer satisfaction rating less than or equal to 2.

- **d. `{ "customer.satisfaction" : { $lte : 2 } }`**
  - **Correct**: This query will return all documents with a customer satisfaction rating of less than or equal to 2.

---

### Question 2
**Your company wants to offer a special discount for customers who are 65 or older. Your task is to find the records for these customers. Which of the following queries would return documents for all customers 65 or older?** *(Select all that apply)*

- **a. `{ customer.age : { $gte : 65 } }`**
  - **Incorrect**: This syntax is incorrect. Remember, to access subdocuments, you must use the syntax `"field.nestedfield"`, which includes quotation marks.

- **b. `{ "customer.age" : { $gt : 64 } }`**
  - **Correct**: This query would return all documents where the customer age is greater than 64, which includes those 65 and older.

- **c. `{ "customer.age" : { $gte : 65 } }`**
  - **Correct**: This query would return all documents where the customer age is greater than or equal to 65.

- **d. `{ "customer.age" : { $lte : 65 } }`**
  - **Incorrect**: This query would return all documents where the customer age is less than or equal to 65. To solve this problem, you need to find all documents where the customer age is 65 or older.

---

## Lesson 4: Querying on Array Elements in MongoDB

### Code Summary: Querying on Array Elements in MongoDB
Review the following code, which demonstrates how to query array elements in MongoDB.

#### Find Documents with an Array That Contains a Specified Value
In the following example, `"InvestmentFund"` is not enclosed in square brackets, so MongoDB returns all documents within the `products` array that contain the specified value.

```javascript
db.accounts.find({ products: "InvestmentFund" })
```

#### Find a Document by Using the $elemMatch Operator
Use the `$elemMatch` operator to find all documents that contain the specified subdocument. For example:

```javascript
db.sales.find({
  items: {
    $elemMatch: { name: "laptop", price: { $gt: 800 }, quantity: { $gte: 1 } },
  },
})
```

---

### Question 1
**Which of the following operators can be used to find a subdocument that matches specific criteria in an array?** *(Select one)*

- **a. `&element`**
  - **Incorrect**: This is not a valid operator included in the MongoDB Shell.

- **b. `$elemMatch`**
  - **Correct**: The `$elemMatch` operator is a valid operator that's included in the MongoDB Shell to find a subdocument that matches specific criteria in an array.

- **c. `$subMatch`**
  - **Incorrect**: This is not a valid operator included in the MongoDB Shell.

- **d. `$docMatch`**
  - **Incorrect**: This is not a valid operator included in the MongoDB Shell.

---

### Question 2
**What will the following query return?** *(Select one)*

```javascript
db.books.find({ genre: "Historical" })
```

- **a. All documents where the `genre` field is equal to either the scalar value of "Historical" or an array that contains "Historical".**
  - **Correct**: This query will return all documents that contain "Historical" as a scalar value and as an array element within the `genre` field.

- **b. All documents that contain the string "Historical" across any field.**
  - **Incorrect**: This query will not return all documents that contain the string "Historical" in any field. It will return all documents that contain "Historical" as a scalar value and as an array element within the `genre` field.

- **c. All documents where the `genre` field does not contain the value "Historical".**
  - **Incorrect**: This query will not filter out all documents that contain the string "Historical" in any field. It will return all documents that contain "Historical" as a scalar value and as an array element within the `genre` field.

---

## Lesson 5: Finding Documents by Using Logical Operators

### Code Summary: Finding Documents by Using Logical Operators
Review the following logical operators: implicit `$and`, `$or`, and `$and`.

#### Find a Document by Using Implicit $and
Use implicit `$and` to select documents that match multiple expressions. For example:

```javascript
db.routes.find({ "airline.name": "Southwest Airlines", stops: { $gte: 1 } })
```

#### Find a Document by Using the $or Operator
Use the `$or` operator to select documents that match at least one of the included expressions. For example:

```javascript
db.routes.find({
  $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }],
})
```

#### Find a Document by Using the $and Operator
Use the `$and` operator to use multiple `$or` expressions in your query.

```javascript
db.routes.find({
  $and: [
    { $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }] },
    { $or: [{ "airline.name": "American Airlines" }, { airplane: 320 }] },
  ]
})
```

---

### Question 1
**You want to know which mobile food trucks in your neighborhood, Astoria, are the best spots to eat. Using the `inspections` collection, you're making a map of all mobile food trucks nearby that have passed inspection. What should you include in your query document to ensure that you find all the mobile food vendors in Astoria that passed inspection?** *(Select one)*

- **a. `{ "sector": "Mobile Food Vendor - 881" } ,{ "address.city": "ASTORIA"} , {"result": "Pass"}`**
  - **Incorrect**: This answer option contains three separate query documents and is invalid. In order to use implicit `$and`, specify the values within one query document.

- **b. `$and: { "Mobile Food Vendor - 881", "ASTORIA", "Pass" }`**
  - **Incorrect**: This syntax is invalid. When using the `$and` operator, you must include each expression within an array.

- **c. `{ "sector": "Mobile Food Vendor - 881" , "address.city": "ASTORIA" , "result": "Pass" }`**
  - **Correct**: This query document uses implicit `$and`, so it will return all mobile food vendors in Astoria that have passed inspection.

- **d. `&and: { "Mobile Food Vendor - 881", "ASTORIA", "Pass" }`**
  - **Incorrect**: This syntax is invalid. When using the `$and` operator, you must include each expression within an array.

---

### Question 2
**What will the following query return?** *(Select one)*

```javascript
db.routes.find({
  $and: [
    { $or: [{ dst_airport: "IST" }, { src_airport: "IST" }] },
    { $or: [{ stops: 0 }, { airline.name: "Turkish Airlines" }] },
  ]
})
```

- **a. All flights departing from or landing at the Istanbul airport (IST) that are nonstop or operated by Turkish Airlines.**
  - **Correct**: This query will return all flights that either depart from or land at the Istanbul airport that are nonstop or operated by Turkish Airlines.

- **b. All flights that are either departing from the Istanbul airport (IST) or Turkish Airlines flights that are nonstop.**
  - **Incorrect**: To return all flights that are either departing from the Istanbul airport or nonstop Turkish Airlines flights, you would write the following query:
    ```javascript
    db.routes.find({
      $or: [{ src_airport: "IST" }, { airline.name: "Turkish Airlines", stops: 0 }],
    })
    ```

- **c. All flights that are either landing at the Istanbul airport (IST) or operated by Turkish Airlines.**
  - **Incorrect**: To return only flights that are landing at the Istanbul airport or those operated by Turkish Airlines, you would write the following query:
    ```javascript
    db.routes.find({
      $or: [{ dst_airport: "IST" }, { airline.name: "Turkish Airlines" }],
    })
    ```

- **d. All Turkish Airlines flights that are nonstop.**
  - **Incorrect**: To return all Turkish Airlines flights that are nonstop, you would write the following query:
    ```javascript
    db.routes.find({ "airline.name": "Turkish Airlines", stops: 0 })
    ```

---

## Conclusion

### MongoDB CRUD Operations: Insert and Find Documents
In this unit, you learned how to insert and find documents in a MongoDB collection. You built queries by using the following comparison operators:
- `$gt` (greater than)
- `$lt` (less than)
- `$lte` (less than or equal to)
- `$gte` (greater than or equal to)

You also used the following logical operators:
- `$and`
- `$or`

Finally, you learned how to query elements in an array and how to use the `$elemMatch` operator.

### Resources
Use the following resources to learn more about inserting and finding documents in MongoDB:

- **Lesson 01: Inserting Documents in a MongoDB Collection**
  - MongoDB Docs: `insertOne()`
  - MongoDB Docs: `insertMany()`
- **Lesson 02: Finding Documents in a MongoDB Collection**
  - MongoDB Docs: `find()`
  - MongoDB Docs: `$in`
- **Lesson 03: Finding Documents by Using Comparison Operators**
  - MongoDB Docs: Comparison Operators
- **Lesson 04: Querying on Array Elements in MongoDB**
  - MongoDB Docs: `$elemMatch`
  - MongoDB Docs: Querying Arrays
- **Lesson 05: Finding Documents by Using Logical Operators**
  - MongoDB Docs: Logical Operators

---

### Associate Certification Course
By completing this unit, you've finished 30% of the CRUD content necessary for the Associate Developer Certification exam.

If you're interested in continuing, your next step is to review the following units:
- Unit 01: Getting Started with MongoDB Atlas
- Unit 02: The MongoDB Document Model
- Unit 03: Connecting to a MongoDB Database
- Unit 05: MongoDB CRUD: Replace and Delete
- Unit 06: MongoDB CRUD: Reading Query Results
- Unit 07: MongoDB Aggregation
- Unit 08: MongoDB Indexes
- Unit 09: MongoDB Atlas Search
- Unit 10: MongoDB Data Modeling
- Unit 11: MongoDB Transactions
