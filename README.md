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

