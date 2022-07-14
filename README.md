# Mongo-DB
### Basics
#### find()
* After connecting using connection URI
* show all the dbs you have
```
show dbs
```
* Switching to a specific DB
```
use <db_name>
```
* Show all the collections inside the DB
```
show collections
```
* Find a document
```
// show all the documents having state 'NY'
db.<collection_name>.find({"state": "NY"})
```
* How many operations:
```
db.zips.find({"state": "NY"}).count()
```
* State is NY but also have city of 'ALBANY'
```
db.zips.find({"state": "NY", "city": "ALBANY"}).count()
```
* View data in more readable way:
```
db.<collection_name>.find({...}).pretty()
```
* Find first 20 documents (any 20)
```
db.<collection_name>.find({})
```
* iterate for more results
```
it
```
#### insert()
* Connect
* Navigate to collection you need
* Find one document (random)
```
db.inspections.findOne()
```
* Insert a document
* Key should be unique
* Don't write id as it will automatically be generated
```
db.inspections.insert({"name": "usama"})
```
* Insert more than one document
```
db.inspections.insert([{"name": "usama"}, {"name": "ali"}])
```
* While inserting many documents, if more than one document having same id, it will generate error because of the order. But in case you want to add documents having unique id regardless of some having same id, use following:
```
db.inspections.insert([{"_id": 1, "test": "1"}, {"_id": 1, "test": "2"}, {"_id": 2, "test": "3"}], {"ordered": false})
```
* In another case: first 2 documents will be inserted successfuly
```
db.inspections.insert([{"_id": 1, "test": "1"}, {"_id": 2, "test": "2"}, {"_id": 2, "test": "3"}], {"ordered": false})
```
#### updateMany()
* Update the documents of city 'HUDSON' and increment the 'population' field by 10
```
db.zips.updateMany({"city": "HUDSON"}, {"$inc": {"population": 10}})
```
#### updateOne()
* Using of set operator
```
db.zips.updateOne({"city": "HUDSON"}, {"$set": {"population": 50000}})
```
* Update the document by adding field in a sub array
```
db.grades.updateOne({"_id": 250}, {"$push": {"scores": {"type": "extra", "score": 100}}})
```
#### deleteMany()
* Delete all the documents matching the 'test1' crieteria
```
db.inspections.deleteMany({"test": 1})
```
* To delete the collection
```
db.inspections.drop()
```
* Delete one document which has the test field of 3
* Use this if you are having a unique criteria like id
```
db.inspections.deleteOne({"_id": 1})
```
### Advance CRUD Operations
#### Operators
* Update Operators
  * Modify the data
  * Like $inc, $set, $unset
* Query Operators
  * Locate data in DB
  * Aggregation pipeline
* Comparison Operators (starts with $)
  * eq(default), ne, gt, lt, gte, lte
  * {<field>: {<operator>: <value>}}
* Find documents where **tripduaration** was less than 70 seconds and **usertype** is not subscriber
```
db.trips.find({"tripduration": {"$lt": 70}, "usertype": {"$ne": "Subscriber"}})
```
  
