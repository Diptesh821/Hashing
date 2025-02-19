Below is the revised, comprehensive guide on MongoDB for Windows with all references updated to use **mongosh** (the new MongoDB Shell) instead of the legacy **mongo** shell. This guide covers everything from installation to advanced topics with examples and code snippets.

---

# Table of Contents

1. [Introduction to MongoDB](#introduction-to-mongodb)
2. [Installing MongoDB on Windows](#installing-mongodb-on-windows)
3. [Getting Started: Running MongoDB with mongod and mongosh](#getting-started)
4. [MongoDB Basics: Databases, Collections, and Documents](#mongodb-basics)
5. [CRUD Operations (Create, Read, Update, Delete)](#crud-operations)
6. [Querying Data in MongoDB](#querying-data)
7. [Indexing for Performance](#indexing)
8. [Aggregation Framework](#aggregation)
9. [Advanced Concepts](#advanced-concepts)
    - Replication & Replica Sets
    - Sharding & Scaling
    - Security & Backup
10. [Tools & GUIs: MongoDB Compass](#compass)
11. [Practical Examples & Code Snippets](#practical-examples)
12. [Final Tips & Best Practices](#final-tips)

---

## 1. Introduction to MongoDB <a name="introduction-to-mongodb"></a>

- **What is MongoDB?**  
  MongoDB is a **NoSQL, document-oriented database** that stores data in JSON-like documents (BSON).  
- **Key Features:**  
  - **Schema-less:** Documents in a collection can have different structures.
  - **Scalability:** Designed for high availability and horizontal scaling.
  - **Flexibility:** Ideal for rapid development and handling unstructured data.
- **Why Use MongoDB?**  
  Use MongoDB when your application needs a flexible schema, quick iterations, or when you must handle large volumes of unstructured or semi-structured data.

---

## 2. Installing MongoDB on Windows <a name="installing-mongodb-on-windows"></a>

### **Step-by-Step Installation:**

1. **Download MongoDB:**
   - Visit the [MongoDB Download Center](https://www.mongodb.com/try/download/community).
   - Select the Windows version (the MSI installer is recommended for ease).
   - Choose the appropriate version (Community Edition is free).

2. **Run the Installer:**
   - Follow the installation wizard.
   - Select “Complete” for a full installation.
   - Optionally, check “Install MongoDB as a Service” for automatic startup.

3. **Configure the Environment:**
   - Add the MongoDB binaries (typically located at `C:\Program Files\MongoDB\Server\<version>\bin`) to your Windows PATH variable. This lets you run MongoDB commands from any Command Prompt.

4. **Create Data Directories:**
   - MongoDB stores data in a default folder. Create the following directories:
     ```cmd
     mkdir C:\data
     mkdir C:\data\db
     ```

5. **Verify Installation:**
   - Open Command Prompt and type:
     ```cmd
     mongod --version
     ```
   - You should see version details.

---

## 3. Getting Started: Running MongoDB with mongod and mongosh <a name="getting-started"></a>

### **Starting the MongoDB Server:**

- **Using Command Prompt:**
  ```cmd
  mongod
  ```
  - This starts the MongoDB server (default port is 27017).
  - **Note:** If you did not install MongoDB as a service, keep this window open.

### **Connecting via the New MongoDB Shell (mongosh):**

- **Open a New Command Prompt:**
  ```cmd
  mongosh
  ```
  - This connects to the running MongoDB server.
  - You’ll see the `mongosh` prompt (e.g., `test>`) where you can run MongoDB commands.
  
*Note:*  
- **mongosh** is the modern replacement for the legacy **mongo** shell and offers improved usability, syntax highlighting, and enhanced features. All examples below assume you’re working in **mongosh**.

---

## 4. MongoDB Basics: Databases, Collections, and Documents <a name="mongodb-basics"></a>

### **Data Model Concepts:**

- **Database:** A container for collections.
- **Collection:** A grouping of MongoDB documents (comparable to tables in relational databases).
- **Document:** A record in JSON/BSON format containing field-value pairs.

### **Example in mongosh:**

```javascript
// Switch to (or create) a new database named "mydb"
use mydb

// Insert a document into a new collection "users"
db.users.insertOne({
  name: "Alice",
  age: 28,
  email: "alice@example.com"
})

// View all documents in the "users" collection
db.users.find().pretty()
```

*Explanation:*  
- `use mydb` creates or switches to the database named "mydb".
- `db.users.insertOne()` inserts a document into the `users` collection.
- `db.users.find().pretty()` retrieves and formats the documents from the collection.

---

## 5. CRUD Operations (Create, Read, Update, Delete) <a name="crud-operations"></a>

### **Create: Inserting Documents**

```javascript
// Insert one document
db.products.insertOne({
  name: "Laptop",
  price: 1200,
  inStock: true
})

// Insert multiple documents
db.products.insertMany([
  { name: "Smartphone", price: 800, inStock: true },
  { name: "Tablet", price: 600, inStock: false }
])
```

### **Read: Querying Documents**

```javascript
// Find all documents
db.products.find().pretty()

// Find documents with a condition
db.products.find({ price: { $gt: 700 } }).pretty()

// Find one document
db.products.findOne({ name: "Laptop" })
```

### **Update: Modifying Documents**

```javascript
// Update a single document
db.products.updateOne(
  { name: "Laptop" },
  { $set: { price: 1100 } }
)

// Update multiple documents
db.products.updateMany(
  { inStock: false },
  { $set: { inStock: true } }
)
```

### **Delete: Removing Documents**

```javascript
// Delete a single document
db.products.deleteOne({ name: "Tablet" })

// Delete multiple documents
db.products.deleteMany({ price: { $lt: 1000 } })
```

---

## 6. Querying Data in MongoDB <a name="querying-data"></a>

### **Basic Query Operators:**

- **Comparison Operators:** `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`
- **Logical Operators:** `$and`, `$or`, `$not`, `$nor`
- **Element Operators:** `$exists`, `$type`
- **Array Operators:** `$in`, `$nin`, `$all`, `$elemMatch`

### **Examples:**

```javascript
// Find products with a price greater than 1000
db.products.find({ price: { $gt: 1000 } }).pretty()

// Find products that are either out of stock or cost less than 500
db.products.find({
  $or: [
    { inStock: false },
    { price: { $lt: 500 } }
  ]
}).pretty()

// Using $exists to check if a field exists
db.products.find({ discount: { $exists: false } }).pretty()
```

*Explanation:*  
These queries use various operators to filter documents based on conditions.

---

## 7. Indexing for Performance <a name="indexing"></a>

### **Why Use Indexes?**

- Indexes speed up query operations by allowing MongoDB to quickly locate data without scanning every document.
- They are similar to indexes in relational databases.

### **Creating an Index:**

```javascript
// Create an index on the "name" field in the "products" collection
db.products.createIndex({ name: 1 })

// Create a compound index on "name" and "price"
db.products.createIndex({ name: 1, price: -1 })
```

### **Viewing and Dropping Indexes:**

```javascript
// List all indexes on a collection
db.products.getIndexes()

// Drop an index by key pattern
db.products.dropIndex({ name: 1 })
```

*Tip:*  
Create indexes on fields that are frequently queried, but avoid over-indexing as it can slow down write operations.

---

## 8. Aggregation Framework <a name="aggregation"></a>

### **Overview:**

- The **Aggregation Framework** allows for advanced data processing and analytics.
- It works via a **pipeline** that passes data through multiple stages.

### **Common Pipeline Stages:**

- **`$match`:** Filters documents.
- **`$group`:** Groups documents by a specified field.
- **`$project`:** Reshapes documents.
- **`$sort`:** Sorts documents.
- **`$limit` and `$skip`:** Paginate results.

### **Example Pipeline:**

```javascript
db.sales.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$category", totalSales: { $sum: "$amount" } } },
  { $sort: { totalSales: -1 } }
])
```

*Explanation:*
- `$match` filters for sales with a "completed" status.
- `$group` aggregates sales by category, summing the `amount`.
- `$sort` orders the results by `totalSales` in descending order.

---

## 9. Advanced Concepts <a name="advanced-concepts"></a>

### **Replication & Replica Sets**

- **Replication** ensures high availability by maintaining multiple copies of data.
- **Replica Sets** are groups of MongoDB servers that store the same data.
- **Example Setup:**

  ```javascript
  rs.initiate({
    _id: "myReplicaSet",
    members: [
      { _id: 0, host: "localhost:27017" },
      { _id: 1, host: "localhost:27018" },
      { _id: 2, host: "localhost:27019" }
    ]
  })
  ```

### **Sharding & Scaling**

- **Sharding** distributes data across multiple servers.
- A **shard key** determines how data is partitioned.
- **Basic Steps:**

  ```javascript
  // Enable sharding on a database
  sh.enableSharding("mydb")
  
  // Shard a collection using a shard key
  sh.shardCollection("mydb.myCollection", { shardKeyField: 1 })
  ```

### **Security & Backup**

- **Authentication & Authorization:**
  - Enable authentication in your MongoDB configuration.
  - Create admin users and assign appropriate roles.
- **Backups:**
  - Use `mongodump` and `mongorestore` utilities for data backups.
  - Consider cloud backup solutions (such as MongoDB Atlas) if applicable.

---

## 10. Tools & GUIs: MongoDB Compass <a name="compass"></a>

- **MongoDB Compass** is the official GUI for MongoDB.
- **Features:**
  - Visualize and explore data.
  - Build and test queries with a user-friendly interface.
  - Monitor performance and manage indexes.
- **Installation:**
  - Download from the [MongoDB Compass page](https://www.mongodb.com/try/download/compass) and install on Windows.
- **Usage:**
  - Connect to your local MongoDB instance (default URL: `mongodb://localhost:27017`).
  - Use the interface to explore your databases, run aggregation pipelines, and manage collections.

---

## 11. Practical Examples & Code Snippets <a name="practical-examples"></a>

### **Example 1: Creating a Simple Blog Database**

1. **Create Database and Collection:**

   ```javascript
   use blogDB

   db.posts.insertMany([
     {
       title: "Introduction to MongoDB",
       author: "John Doe",
       tags: ["MongoDB", "NoSQL", "Database"],
       date: new Date(),
       comments: [
         { user: "Alice", comment: "Great post!" },
         { user: "Bob", comment: "Very informative." }
       ]
     },
     {
       title: "Advanced Aggregation",
       author: "Jane Smith",
       tags: ["Aggregation", "MongoDB", "Advanced"],
       date: new Date(),
       comments: []
     }
   ])
   ```

2. **Querying the Posts:**

   ```javascript
   // Find posts tagged with "MongoDB"
   db.posts.find({ tags: "MongoDB" }).pretty()

   // Count posts by author "John Doe"
   db.posts.countDocuments({ author: "John Doe" })
   ```

3. **Updating a Post:**

   ```javascript
   // Add a new comment to a specific post
   db.posts.updateOne(
     { title: "Introduction to MongoDB" },
     { $push: { comments: { user: "Charlie", comment: "Thanks for sharing!" } } }
   )
   ```

4. **Aggregation Example:**

   ```javascript
   // Aggregate posts by author and count the number of posts
   db.posts.aggregate([
     { $group: { _id: "$author", postCount: { $sum: 1 } } },
     { $sort: { postCount: -1 } }
   ])
   ```

### **Example 2: Inventory Management System**

1. **Insert Inventory Items:**

   ```javascript
   use inventoryDB

   db.items.insertMany([
     { item: "pen", qty: 50, tags: ["stationery"], price: 1.5 },
     { item: "notebook", qty: 20, tags: ["stationery"], price: 3.0 },
     { item: "eraser", qty: 100, tags: ["stationery"], price: 0.5 }
   ])
   ```

2. **Query Inventory:**

   ```javascript
   // Find items with quantity less than 30
   db.items.find({ qty: { $lt: 30 } }).pretty()
   ```

3. **Update Stock:**

   ```javascript
   // Increase quantity of all stationery items by 10
   db.items.updateMany(
     { tags: "stationery" },
     { $inc: { qty: 10 } }
   )
   ```

---

## 12. Final Tips & Best Practices <a name="final-tips"></a>

- **Keep Your Schema Flexible:**  
  Although MongoDB is schema-less, plan your data model to avoid overly nested or unstructured data.
- **Use Indexes Wisely:**  
  Create indexes on frequently queried fields but avoid over-indexing in write-heavy applications.
- **Regular Backups:**  
  Schedule regular backups using `mongodump` or other backup solutions.
- **Monitor Performance:**  
  Use MongoDB Compass and built-in commands like `db.serverStatus()` to monitor system performance.
- **Security:**  
  Enable authentication, use role-based access control, and follow security best practices to protect your database.

---

By following these revised detailed notes—now updated to use **mongosh**—you should have a complete understanding of MongoDB on Windows, from basic operations to advanced configurations. Happy coding and database management!