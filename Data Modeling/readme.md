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
