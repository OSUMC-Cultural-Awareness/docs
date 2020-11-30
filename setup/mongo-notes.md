## ssh into mongo container

```sh
docker exec -it mongodb bash
# verify mongodb instance is running
ps -edaf | grep mongo | grep -v grep

# log into mongo shell with credentials
mongo --username user --password password --authenticationDatabase admin
```

## mongo shell commands

[mongo shell docs](https://docs.mongodb.com/manual/reference/mongo-shell/)

```sh

show dbs

use <database>
db.dropDatabase()
db.createCollection('<collection>')
show collections
db.<collection>.insertOne({name: 'bill', email: 'example@gamil.com'})

# show all documents in the collection
db.<collection>.find()

# remove a collection
db.<collection>.drop()
```

## python mongo commands

[pymongo docs](https://pymongo.readthedocs.io/en/stable/index.html)

```python
from pymongo import MongoClient

client = MongoClient(MONGO_URL)
database = client["<database_name>"]
collection = database["<collection_name>"]
database.collection_names()

# insert BSON document into collection, returns id
document = collection.insert_one({"name": "<document_name>"})
x = mycol.insert_many(<list_of_documents>)

# print first document in the collection
x = collection.find_one()

# print all documents in the collection
for x in collection.find():
...     print(x)

# filter using a query
query = {"name": "Example"}
for x in col.find(query):
...     print(x)

# delete document
myquery = { "name": "Example" }
mycol.delete_one(myquery)


# delete all documents in a collection
x = collection.delete_many({})

# update a document in a collection
query = { "address": "Valley 345" }
newvalue = { "$set": { "address": "Canyon 123" } }
mycol.update_one(query, newvalue)
```
