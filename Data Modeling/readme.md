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

// result
{ _id: 1,
  email: 'admin@gmail.com',
  userId: 'AD',
  userName: 'admin',
  user_info: 
   { _id: ObjectId("56d82612b63f1c31cf906003"),
     userId: 'AD',
     phone: '0000000000' },
  user_role: 
   [ { _id: ObjectId("56d82612b63f1c31cf906003"),
       userId: 'AD',
       role: 'admin' } ] }
```
### Model Relationships
#### One-to_One Relationshio
* Data to be used frequently --> embed
* Infrequent use of data --> reference
* **Embed Example**
```
{
   _id: "joe",
   name: "Joe Bookreader",
   address: {
              street: "123 Fake Street",
              city: "Faketon",
              state: "MA",
              zip: "12345"
            }
}
```
* If large document, embed cancause problem
```
// movie collection

{
  "_id": 1,
  "title": "The Arrival of a Train",
  "year": 1896,
  "runtime": 1,
  "released": ISODate("1896-01-25"),
  "type": "movie",
  "directors": [ "Auguste Lumière", "Louis Lumière" ],
  "countries": [ "France" ],
  "genres": [ "Documentary", "Short" ],
}


// movie_details collection

{
  "_id": 156,
  "movie_id": 1, // reference to the movie collection
  "poster": "http://ia.media-imdb.com/images/M/MV5BMjEyNDk5MDYzOV5BMl5BanBnXkFtZTgwNjIxMTEwMzE@._V1_SX300.jpg",
  "plot": "A group of people are standing in a straight line along the platform of a railway station, waiting for a train, which is seen coming at some distance. When the train stops at the platform, ...",
  "fullplot": "A group of people are standing in a straight line along the platform of a railway station, waiting for a train, which is seen coming at some distance. When the train stops at the platform, the line dissolves. The doors of the railway-cars open, and people on the platform help passengers to get off.",
  "lastupdated": ISODate("2015-08-15T10:06:53"),
  "imdb": {
    "rating": 7.3,
    "votes": 5043,
    "id": 12
  },
  "tomatoes": {
    "viewer": {
      "rating": 3.7,
      "numReviews": 59
    },
    "lastUpdated": ISODate("2020-01-29T00:02:53")
  }
}
```
