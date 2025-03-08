# MongoDB Setup Guide

## Prerequisites
Ensure you have the following installed on your system:

- [MongoDB](https://www.mongodb.com/try/download/community) (Community Edition)
- [MongoDB Compass](https://www.mongodb.com/try/download/compass) (Optional GUI tool)
- [Git](https://git-scm.com/downloads)

## 1. Setting Up the Database

### 1.1 Start MongoDB
If MongoDB is installed, start the MongoDB service:
```sh
mongod --dbpath <path_to_your_database_directory>
```
If using Windows, you may start the service using:
```sh
net start MongoDB
```

### 1.2 Access the MongoDB Shell
Open a new terminal and run:
```sh
mongo
```

## 2. Creating a Database and Collection

### 2.1 Create and Switch to the `library` Database
```js
use library;
```

### 2.2 Create the `books` Collection
```js
db.createCollection("books");
```

## 3. Inserting Data
Insert sample book records:
```js
db.books.insertMany([
  { title: "The Alchemist", author: "Paulo Coelho", publishedYear: 1988, genre: "Fiction", ISBN: "9780062315007" },
  { title: "Sapiens: A Brief History of Humankind", author: "Yuval Noah Harari", publishedYear: 2011, genre: "History", ISBN: "9780062316097" },
  { title: "Atomic Habits", author: "James Clear", publishedYear: 2018, genre: "Self-help", ISBN: "9780735211292" },
  { title: "The Hobbit", author: "J.R.R. Tolkien", publishedYear: 1937, genre: "Fantasy", ISBN: "9780547928227" },
  { title: "The Catcher in the Rye", author: "J.D. Salinger", publishedYear: 1951, genre: "Fiction", ISBN: "9780316769488" }
]);
```

## 4. Retrieving Data

### 4.1 Retrieve All Books
```js
db.books.find().pretty();
```

### 4.2 Query Books by Author
```js
db.books.find({ author: "Yuval Noah Harari" }).pretty();
```

### 4.3 Find Books Published After 2000
```js
db.books.find({ publishedYear: { $gt: 2000 } }).pretty();
```

## 5. Updating Data

### 5.1 Update `publishedYear` of a Specific Book
```js
db.books.updateOne({ ISBN: "9780062315007" }, { $set: { publishedYear: 1993 } });
```

### 5.2 Add a `rating` Field to All Books
```js
db.books.updateMany({}, { $set: { rating: 4.5 } });
```

## 6. Deleting Data

### 6.1 Delete a Book by ISBN
```js
db.books.deleteOne({ ISBN: "9780316769488" });
```

### 6.2 Remove All Books of a Particular Genre
```js
db.books.deleteMany({ genre: "Fiction" });
```

## 7. Data Modeling Exercise:

### 7.1 Create a data model for an e-commerce platform including collections for users ,orders, and products.
```js
use shoeStore;

// Create users collection
db.createCollection("users");

// Create products collection
db.createCollection("products");

// Create orders collection
db.createCollection("orders");

// Create categories collection
db.createCollection("categories");

// Create tags collection
db.createCollection("tags");

db.users.insertMany([
  { 
    _id: ObjectId("67cbe149b017acead9fa4219"),
    name: "Alice",
    email: "alice@yahoo.com", 
    address: "123 Kimathi Street"
  },
  {
    _id: ObjectId("67cbe149b017acead9fa421a"),
    name: "Bob",
    email: "bob@gmail.com",
    address: "456 Taveta Street"
  }
]);

db.products.insertMany([
  {
    _id: ObjectId("67cbe3e9b017acead9fa421b"),
    name: "Laptop",
    price: NumberInt(1000),
    category: ObjectId("categories.electronics_category"),
    subcategory: ObjectId("categories.laptops_subcategory"),
    description: "High-performance laptop for gaming and productivity",
    stock: NumberInt(10)
  },
  {
    _id: ObjectId("67cbe3e9b017acead9fa421c"),
    name: "Phone",
    price: NumberInt(500),
    category: ObjectId("categories.electronics_category"),
    subcategory: ObjectId("categories.smartphones_subcategory"),
    description: "Latest smartphone with advanced camera and AI features",
    stock: NumberInt(20)
  }
]);

db.categories.insertMany([
  { _id: ObjectId("categories.electronics_category"), name: "Electronics" },
  { _id: ObjectId("categories.laptops_subcategory"), name: "Laptops" },
  { _id: ObjectId("categories.smartphones_subcategory"), name: "Smartphones" }
]);

db.tags.insertMany([
  { _id: ObjectId("tags.gaming_tag"), name: "Gaming" },
  { _id: ObjectId("tags.portable_tag"), name: "Portable" },
  { _id: ObjectId("tags.camera_tag"), name: "Camera" }
]);

db.orders.insertMany([
  {
    _id: ObjectId(),
    userId: ObjectId("67cbe149b017acead9fa4219"),
    items: [
      {
        productId: ObjectId("67cbe3e9b017acead9fa421b"),
        quantity: NumberInt(1)
      }
    ],
    totalPrice: NumberInt(1000),
    status: "pending",
    dateCreated: ISODate(),
    shippingAddress: {
      street: "123 Main St",
      city: "Nairobi",
      country: "Kenya"
    },
    paymentMethod: "Credit Card",
    paymentStatus: "processing"
  },
  {
    _id: ObjectId(),
    userId: ObjectId("67cbe149b017acead9fa421a"),
    items: [
      {
        productId: ObjectId("67cbe3e9b017acead9fa421c"),
        quantity: NumberInt(1)
      }
    ],
    totalPrice: NumberInt(500),
    status: "pending",
    dateCreated: ISODate(),
    shippingAddress: {
      street: "456 Market St",
      city: "Nairobi",
      country: "Kenya"
    },
    paymentMethod: "PayPal",
    paymentStatus: "processing"
  }
]);

// Users
db.users.find().pretty();

// Products
db.products.find().pretty();

// Categories
db.categories.find().pretty();

// Tags
db.tags.find().pretty();

// Orders
db.orders.find().pretty();
```

## 8. Aggregation Pipeline

### 8.1 Find the total number of books per genre:
```js
db.books.aggregate([
  { $group: { _id: "$genre", count: { $sum: 1 } } }
]);

```

### 8.2 Calculate the average publishedYear of all books:

```js
db.books.aggregate([
  { $group: { _id: null, avgPublishedYear: { $avg: "$publishedYear" } } }
]);
```

### 8.3 Identify the top-rated book:
```js
db.books.aggregate([
  { $sort: { rating: -1 } },
  { $limit: 1 }
]);
```

### 9 Indexing
### 9.1 Create an index on the author field:
```js
db.books.createIndex({ author: 1 });
```
### 9.2 Explain the benefits of indexing in MongoDB.
## 1. Faster Query Performance

- Indexes speed up read operations by allowing the database to quickly locate data without scanning entire collections.
- This is particularly beneficial for frequently accessed fields.

## 2. Improved Write Performance

- Indexes can also accelerate write operations like insertions and updates.
- However, they require additional storage space and maintenance overhead.

## 3. Efficient Sorting and Aggregation

- Indexes allow for faster sorting and aggregation operations.
- This is especially useful when you need to sort results or perform complex queries.

## 4. Reduced I/O Operations

- By allowing the database to quickly locate data, indexing reduces the number of disk I/O operations needed.
- This leads to better overall system performance.



