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
  * ```{<field>: {<operator>: <value>}}```
* Find documents where **tripduaration** was less than 70 seconds and **usertype** is not subscriber
```
db.trips.find({"tripduration": {"$lt": 70}, "usertype": {"$ne": "Subscriber"}})
```
* Logical Operators
 * and(default), or, not, nor
```
// and, or, nor
{<operator>:[{statement1}, {statement2}...]}
// not
{$not: {statement}}
```
* Find all documents where airplanes CR2 or A81 left or landed in the KZN airport:
 * First 'or' airports then 'or' airplanes and finally 'and' both of the results
```
db.routes.find({"$and": [{"$or": [{"src_airport": 'KZN'}, {"dst_airport": 'KZN'}]}, {"$or": [{"airplane": 'CR2'}, {"airplane": 'A81'}]}]})
```
#### Express Query Operator
* expr
* use of aggregations statements with the query language
* ```{$expr: {<expression>}}```
* dollar sign can be used to find the value
* conditional statements inside the query
* in a same document comparison
* Find all the documents where the trip started and ended at the same station
```
db.trips.find({
  "$expr": {
      "$eq": ["$end station id", "$start station id"]
  }
}).count()
```
#### Array Operator
* push
* size
 * returns only the documents having specified number inside the array
 ```
 {<array_field>: {"$size": <number>}}
 ```
* all
 * returns all the documents having specified elements regardless of their order; otherwise order matters
 ```
 {<array_field>: {"$all": <array>}}
 ```
* An array returns only exact array matches
* If you specified a single element only, it will return all the documents having that element in array
* *Find all documents with exactly 20 amenities which include all the amenities listed in the query array:*
```
db.listingsAndReviews.find({ "amenities": {
                                  "$size": 20,
                                  "$all": [ "Internet", "Wifi",  "Kitchen",
                                           "Heating", "Family/kid friendly",
                                           "Washer", "Dryer", "Essentials",
                                           "Shampoo", "Hangers",
                                           "Hair dryer", "Iron",
                                           "Laptop friendly workspace" ]
                                         }
                            }).pretty()
```
#### Projections
* Show only specified fields only
```
db.collection.find({<query>}, {<projection>})
```
* 1 for include the field and 0 to exclude
 * don't mix both except id having 0 and others 1
* *Find all documents that have Wifi as one of the amenities only include price and address in the resulting cursor:*
```
db.listingsAndReviews.find({"amenities": "Wifi"}, {"price": 1, "address": 1})
```
* Accessing the data inside the sub document (array)
 * elemMatch
* *Find all documents where the student in class 431 received a grade higher than 85 for any type of assignment:*
   * Note that second part is the projection (will show id and scores only)
```
db.grades.find({"class_id": 431}, {"scores": {"$elemMatch": {"score": {"$gte":85}}}})
```
#### Array Operations and Sub Documents
* Using of dot operator
  * Nesting as we want
* *Select documents where start station has a type of 'Point'*
```
db.companies.find({"offices": {"$elemMatch": {"city": "Seattle"}}}).count()
```
* *Find a company name whose first person's last name was 'Zuckerberg'*
```
db.companies.find({"relationships.0.person.last_name":"Zuckerberg"}, {"name": 1})
```
* *Find **CEO** named **Mark** listed first in the **relationship** array*
```
db.companies.find({"relationships.0.person.last_name":"Zuckerberg", "relationships.0.title": {"$regex": "CEO"}}, {"name": 1})
```
* Or you can use elemMatch
```
db.companies.find({ "relationships":
                      { "$elemMatch": { "is_past": true,
                                        "person.first_name": "Mark" } } },
                  { "name": 1 }).count()
```
