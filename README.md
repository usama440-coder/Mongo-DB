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
