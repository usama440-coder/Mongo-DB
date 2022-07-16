# Data Modeling
### Introduction
#### Methodology
* Wrokloads
  * data size and important reads/writes
  * data used frequently
* Relationships
  * Identify, link or embed
* Patterns
  * Optimizations
#### Simplicity and Performance
* Simplicity
  * small team and small project
  * Most frequest queries
  * Single collection
* Performance
  * Data size
  * Quality and Quantity
  * High resources
  * Larger teams and high loads of reads/writes
* Tradeoff
* Prioritize simplicity
* A balance approach
### Data Model Design
#### Embedded Model Design
* Embed related data in single document
* Denormalized model
* Used with one-to-one and one-to-many
* **Example**
  * contact and access are embedded 
```
{
 _id: 1111,
 username: "xyz",
 contact: {
  phone: 111,
  email: abc@mail.com
 },
  access: {
  level: 5,
  group: "dev"
 }
}
```
* Fewer Queries
#### Normalized Data Models
* References instead of Embed
* **Example**
  * contact and access has reference to user id in 'user' document
```
// user
{
 _id: <ObjectId>,
 username: "xyz"
}

// contact
{
 _id: <ObjectId>,
 user_id: <ObjectId>,
 phone: 1111
}

// access
{
 _id: <ObjectId>,
 user_id: <ObjectId>,
 level: 5
}
```
* Used with many-to-many
* large data sets
* To join collections:
  * ```$lookup```
```
// orders
db.orders.insertMany( [
   { "_id" : 1, "item" : "almonds", "price" : 12, "quantity" : 2 },
   { "_id" : 2, "item" : "pecans", "price" : 20, "quantity" : 1 },
   { "_id" : 3  }
] )

// inventory
db.inventory.insertMany( [
   { "_id" : 1, "sku" : "almonds", "description": "product 1", "instock" : 120 },
   { "_id" : 2, "sku" : "bread", "description": "product 2", "instock" : 80 },
   { "_id" : 3, "sku" : "cashews", "description": "product 3", "instock" : 60 },
   { "_id" : 4, "sku" : "pecans", "description": "product 4", "instock" : 70 },
   { "_id" : 5, "sku": null, "description": "Incomplete" },
   { "_id" : 6 }
] )

// lookup
db.orders.aggregate( [
   {
     $lookup:
       {
         from: "inventory",
         localField: "item",
         foreignField: "sku",
         as: "inventory_docs"
       }
  }
] )
```
* **$unwind** operation
  * Deconstructs an array
```
db.inventory.insertOne({ "_id" : 1, "item" : "ABC1", sizes: [ "S", "M", "L"] })
db.inventory.aggregate( [ { $unwind : "$sizes" } ] )

//result
{ "_id" : 1, "item" : "ABC1", "sizes" : "S" }
{ "_id" : 1, "item" : "ABC1", "sizes" : "M" }
{ "_id" : 1, "item" : "ABC1", "sizes" : "L" }
```
* To join more than 2 collections
```
// users
db.users.insertOne({    
    "_id" : 1,
    "email" : "admin@gmail.com",
    "userId" : "AD",
    "userName" : "admin"
})

// userInfo
db.userInfo.insertOne({
    "_id" : ObjectId("56d82612b63f1c31cf906003"),
    "userId" : "AD",
    "phone" : "0000000000"
})

// userRole
db.userRole.insertOne({
    "_id" : ObjectId("56d82612b63f1c31cf906003"),
    "userId" : "AD",
    "role" : "admin"
})

db.users.aggregate([
  {
  "$lookup": {
    from: "userInfo",
    localField: "userId",
    foreignField: "userId",
    as: "user_info"
    }
  },
  {
    "$unwind": "$user_info"
  },
  {
    "$lookup": {
      from: "userRole",
      localField: "userId",
      foreignField: "userId",
      as: "user_role"
    }
  }
])
```
