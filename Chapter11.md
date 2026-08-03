# Getting Started with MongoDB Atlas

---

## Lesson 1: Foundations of MongoDB

### Question 1
**Which of the following best describes a document database?** *(Select one)*

- **a. A database where data is stored in tables with rows and columns**
  - **Incorrect**: This describes a **relational database**, not a document database.

- **b. A database where data is stored in JSON-like documents, allowing for flexible and polymorphic data structures**
  - **Correct**: A document database stores data in JSON-like documents, which can represent complex data structures.

- **c. A database that requires a fixed schema and stores data in a hierarchical structure**
  - **Incorrect**: A document database supports both structured and unstructured data through its **flexible schema**.

- **d. A database designed to index data from document files like PDFs and spreadsheets**
  - **Incorrect**: While a document database can store various types of data, it is not specifically designed to index data from document files like PDFs and spreadsheets. In the context of MongoDB, documents are the fundamental unit of data — they have a JSON-like structure consisting of field-value pairs.

---

### Question 2
**What are the primary advantages of MongoDB's flexible schema?** *(Select all that apply)*

- **a. It allows each document to have a unique structure, accommodating various data types and fields within a single collection.**
  - **Correct**: One of the main advantages of a flexible schema is that it allows each document to have a unique structure, accommodating various data types and fields within a single collection.

- **b. It enables the database to handle unstructured or semi-structured data that doesn't fit neatly into a table.**
  - **Correct**: A flexible schema is particularly beneficial for handling unstructured or semi-structured data that doesn't fit neatly into a table.

- **c. It requires all documents to follow a predefined structure, but allows changes to those structures.**
  - **Incorrect**: A flexible schema by default does **not** require documents to follow a predefined structure. It allows for variability in document structures.

- **d. It permits changes and additions to the schema without requiring predefined structures.**
  - **Correct**: An advantage of a flexible schema is that it permits changes and additions to the schema without requiring predefined structures, making it adaptable to evolving data requirements.

---

## Lesson 2: MongoDB Architecture Overview

### Question 1
**Which of the following statements correctly identifies the relationships among documents, collections, and databases in MongoDB?** *(Select one)*

- **a. A database is a group of documents, and a collection is a group of databases.**
  - **Incorrect**: A database is a group of **collections**, not documents.

- **b. A document is a group of collections, and a database is a group of documents.**
  - **Incorrect**: In MongoDB, a document is the **fundamental unit of data**, not a group of collections.

- **c. A document is a group of databases, and a collection is a group of documents.**
  - **Incorrect**: In MongoDB, a document is the fundamental unit of data, and a collection is a group of documents, not databases.

- **d. A collection is a group of documents, and a database is a group of collections.**
  - **Correct**: In MongoDB, a **collection** is a group of documents, and a **database** is a group of collections.

---

### Question 2
**Which of the following best describes how replication in MongoDB provides high availability?** *(Select all that apply)*

- **a. Replication stores multiple copies of data across multiple nodes.**
  - **Correct**: Replication stores multiple copies of data across multiple nodes, ensuring redundancy and high availability.

- **b. Replication allows for horizontal scaling by distributing data across nodes.**
  - **Incorrect**: Horizontal scaling by distributing data across nodes is achieved through **sharding**, not replication.

- **c. Replication ensures that the database remains operational even if one node fails.**
  - **Correct**: Replication ensures that the database remains operational even if one node fails by maintaining redundant copies of data.

- **d. Replication is used to partition data across different shards.**
  - **Incorrect**: Partitioning data across different shards is a function of **sharding**, not replication.

---

## Lesson 3: Atlas Overview

### Question 1
**What are key differences between MongoDB Atlas as a database-as-a-service and MongoDB's self-managed options?** *(Select all that apply)*

- **a. MongoDB Atlas requires users to manually configure and maintain their database infrastructure.**
  - **Incorrect**: MongoDB Atlas, as a database-as-a-service, **removes** the need for users to manually configure and maintain their database infrastructure, which is a requirement for self-managed options.

- **b. MongoDB Atlas provides automatic failover across regions and availability zones.**
  - **Correct**: MongoDB Atlas provides automatic failover across regions and availability zones, which enhances availability and fault tolerance — a feature not inherently available in self-managed options.

- **c. MongoDB Atlas includes built-in support for multi-factor authentication (MFA) and role-based access control (RBAC).**
  - **Correct**: MongoDB Atlas includes built-in support for MFA and RBAC, providing robust security features that are managed by the service.

- **d. MongoDB Atlas reduces operational overhead by offering a fully managed cloud-based infrastructure.**
  - **Correct**: MongoDB Atlas reduces operational overhead by offering a fully managed cloud-based infrastructure.

---

### Question 2
**Which of the following best describes the role of automatic failover in MongoDB Atlas?** *(Select one)*

- **a. Automatic failover distributes data across multiple regions to reduce latency.**
  - **Incorrect**: Distributing data across multiple regions to reduce latency is a function of **data distribution**, not specifically automatic failover.

- **b. Automatic failover ensures data is encrypted both in transit and at rest.**
  - **Incorrect**: Ensuring data is encrypted both in transit and at rest is a **security feature**, not related to automatic failover.

- **c. Automatic failover allows the database to continue operating if one availability zone fails by switching to another zone.**
  - **Correct**: Automatic failover allows the database to continue operating if one availability zone fails by switching to another zone, ensuring **high availability**.

- **d. Automatic failover manages user permissions and access control.**
  - **Incorrect**: Managing user permissions and access control is a function of **role-based access control (RBAC)**, not automatic failover.

---

## Lesson 4: Deploying an Atlas Cluster

### Question 1
**You are deploying your first MongoDB Atlas cluster and want to choose the most suitable option for loading a sample dataset and experimenting with simple CRUD operations. Which of the following cluster types should you consider?** *(Select all that apply)*

- **a. M10**
  - **Incorrect**: M10 is a dedicated cluster tier that's more suitable for handling large datasets and production deployments rather than for experimentation with simple CRUD operations.

- **b. Serverless**
  - **Correct**: The serverless tier operates on a "pay for what you use" model, making it suitable for experimenting with simple CRUD operations.

- **c. M0**
  - **Correct**: M0 is a free cluster tier that's ideal for loading a sample dataset and experimenting with simple CRUD operations. It can be upgraded to a serverless or dedicated tier with additional capabilities if needed.

- **d. Dedicated**
  - **Incorrect**: Dedicated clusters, at the M10 level and higher, are recommended for production deployments and high-traffic applications, rather than for experimentation with simple CRUD operations.

---

### Question 2
**You have created an Atlas account and want to organize your resources in a way that supports development, testing, and production environments. Which of the following actions should you take?** *(Select all that apply)*

- **a. Create separate projects for development, testing, and production to isolate the environments.**
  - **Correct**: Creating separate projects for development, testing, and production helps **isolate resources**.

- **b. Name your organization descriptively to reflect its overall purpose.**
  - **Correct**: Naming your organization descriptively helps to clarify its role, which can in turn help streamline management.

- **c. Deploy all clusters in a single project to simplify management and reduce overhead.**
  - **Incorrect**: Deploying all clusters in a single project can complicate management and reduce the effectiveness of access control and resource organization.

---

## Lesson 5: Exploring the Atlas UI

### Question 1
**You've just upgraded from an M0 to M10 cluster. Which of the following features are you now able to use that you did not have access to previously?** *(Select all that apply)*

- **a. Real-Time panel**
  - **Correct**: The Real-Time performance panel is a feature available on **M10 and above** clusters. It provides insight into metrics such as network traffic, database operations, and hardware statistics.

- **b. Find bar for querying documents**
  - **Incorrect**: The Find bar is available on **all cluster tiers** and is used for querying documents within collections.

- **c. Performance Advisor**
  - **Correct**: The Performance Advisor is available on **M10 and above** clusters. It offers guidance on improving cluster performance by identifying slow queries and suggesting new indexes.

- **d. Online Archive**
  - **Correct**: The Online Archive is available on **M10 and above** clusters. It optimizes storage while allowing queries on infrequently accessed data.

---

### Question 2
**Your data is stored in an M10 cluster, and you are working in the MongoDB Atlas UI. Which of the following information can you see in the Real-Time performance panel?** *(Select all that apply)*

- **a. Current operations running on the cluster**
  - **Correct**: The Real-Time performance panel shows the current operations running on the cluster, providing insight into what processes are actively using resources.

- **b. Summary of cluster performance over the past month**
  - **Incorrect**: The Real-Time performance panel focuses on **current and recent** performance metrics, not historical data over extended periods such as the past month.

- **c. Hardware statistics**
  - **Correct**: The Real-Time performance panel provides real-time metrics for CPU, disk IOPS, and memory usage, allowing you to monitor the current state of your cluster's hardware resources.

- **d. Network traffic**
  - **Correct**: The Real-Time performance panel reports network traffic information, such as the number of current connections and number of inbound and outbound bytes per second.

---

## Conclusion

### Getting Started with MongoDB Atlas

In this unit, you learned how to:

- Define a document database and distributed system
- Explain the purpose of a flexible schema
- Define documents, collections, and databases in MongoDB
- Describe how MongoDB, as a distributed system, maintains consistency
- Explain how MongoDB Atlas works as a database-as-a-service
- Describe how MongoDB operates on the cloud and uses automatic failover
- Deploy an Atlas cluster
- Navigate the Atlas UI and load a sample dataset
- Query for documents in a database, using the Atlas UI

---

## Resources

Use the following resources to learn more about getting started with MongoDB:

- **Lesson 1–3: Foundations & Atlas Overview**
  - [Docs: Get Started with Atlas](https://www.mongodb.com/docs/atlas/getting-started/)
  - [Docs: Introduction to MongoDB](https://www.mongodb.com/docs/manual/introduction/)
  - [MongoDB Use Cases](https://www.mongodb.com/use-cases)
  - [Docs: FAQ: MongoDB Fundamentals](https://www.mongodb.com/docs/manual/faq/fundamentals/)

- **Explore other MongoDB topics and features:**
  - [Docs: Atlas Vector Search Quickstart](https://www.mongodb.com/docs/atlas/atlas-vector-search/tutorials/)
  - [Docs: Replication](https://www.mongodb.com/docs/manual/replication/)
  - [Docs: Sharding](https://www.mongodb.com/docs/manual/sharding/)
  - [Docs: Security](https://www.mongodb.com/docs/manual/security/)
