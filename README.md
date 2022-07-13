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
