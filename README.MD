## Exercise 1

This task uses sample data available on the Atlas Cloud console.

1. Please follow the steps in [This Tutorial](https://www.mongodb.com/resources/products/platform/mongodb-atlas-tutorial)
   
2. Using your `mongosh` shell, list your databases, select the `sample_mflix` set, then show collections within that:
```
show dbs;
use sample_mflix;
show collections;
```

3. This should show you there is a `movies` collection within that database. Find all documents in the collection, then count them:
```
db.movies.find();
db.movies.countDocuments();
```

4. Search for all documents containing a `year` of `1921` and count the results. Then display the results:
```
db.movies.find({"year":1921})
db.movies.countDocuments({"year":1921})
```

5. Retrieve a single document based on `ObjectId`:
```
db.movies.find(ObjectId('573a1391f29313caabcd72f0'));
```

6. Finally, using the code below insert a new document. After insertion, can you retrieve this document?
```
db.movies.insertOne({
    genres: [ 'Drama' ],
    runtime: 14,
    cast: [
        'Acty Actor',
    ],
    title: "Example Movie",
    countries: [ 'USA' ],
    released: ISODate('1930-01-01T00:00:00.000Z'),
    directors: [ 'Director Name' ],
    lastupdated: '2024-04-09 01:12:08.943000000',
    year: 1930,
    imdb: { rating: 8.1, votes: 1455, id: 12999 },
    type: 'movie',
    num_mflix_comments: 0
});
```

## Exercise 2

This exercise walks you through creating your own database and populating a collection with your own data.

1. List DBs
```
show dbs
```
2. Show the DB you are currently in
```
db
```
3. Use a specific database based on your UVA computing ID. If it does not exist, it will be created.
```
use mst3k
show dbs
```
Notice that your new database does not yet show up. This is because it needs to contain some documents first.

4. Insert a simple document. You will specify a collection within the `things` DB, and if it does not yet exist it will be created.
```
db.hobbies.insertOne({name:"horseback riding"})
```
5. List all documents in this collection.
```
db.hobbies.find()
```
You should get back a simple document:
```
{
    "_id" : ObjectId("606b5e9d37c1606354c39e3d"),
    "name" : "horseback riding",
}
```

6. Insert several more documents, varied in their data complexity.
```
db.hobbies.insertOne({"name":"cycling","equipment":["bicycle","helmet","air pump"]})
db.hobbies.insertOne({"name":"basketball","equipment":["ball","shoes","court","rim","game"]})
db.hobbies.insertOne({"name":"archery","equipment":["bow","arrows"]})
```
7. View all documents again:
```
db.hobbies.find()
```
8. Notice your first document lacks any equipment values. To update it
```
db.hobbies.updateOne({name:"horseback riding"},{$set : {equipment:["horse","saddle","helmet"]}})
```

9. Search for that record to see if it has been updated properly:

```
db.hobbies.find({name:"horseback riding"})
```
```
{
    "_id" : ObjectId("606b5e9d37c1606354c39e3d"),
    "name" : "horseback riding",
    "equipment" : [ "horse", "saddle", "helmet" ]
}
```

10. Search for all hobbies that require a helmet:
```
db.hobbies.find({equipment:"helmet"})
```
1.  Upsert - adds a document when it does not exist from an `UPDATE` command
```
db.hobbies.update({name:"ultimate frisbee"},{name:"ultimate frisbee",equipment:["friends","frisbee"]},{upsert: true})
```
1.  Remove a document
You can remove document based on any `find` parameters, such as a particular value. However, the most unique key for
single-row deletions is the `_id` of a particular row. Try deleting some documents (replace the `ObjectId` value with one
from your collection:

```
# Remove by the _id:ObjectId
db.hobbies.remove({_id:ObjectId('606b5e9d37c1606354c39e3d')})

# Or use deleteOne if you know another unique value
db.hobbies.deleteOne({name:"archery"})

# Or deleteMany based on a common attribute
db.hobbies.deleteMany({equipment:"helmet"})
```

## Reference: CRUD Operations

Let's create another collection to work with called `inventory`:
```
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );
```

**Create**
```
db.<db-name>.insertOne({...})
db.inventory.insertOne({ item: "calendar", qty: 80, size: { h: 20, w: 40, uom: "cm" }, status: "D" })

db.<db-name>.insertMany({...})   # See above
```

**Read**
```
db.<db-name>.find({...})
db.inventory.find({"status": "D"})

db.<db-name>.findOne({...})
db.inventory.findOne(ObjectId('66157d66f27a5c78964fdd80'))
```

**Update / Upsert**

An `upsert` is applied to all documents matching the criteria, or inserts a new document if there are no matches.

```
db.<db-name>.update({{"<search-key>" : "<search-value>"},{$set : {"<key>": "<updated-value>"}}})
db.<db-name>.updateOne({SingleKeyToUpdate},{Set Command})

db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

**Delete**
```
db.<db-name>.deleteOne(<search-condition>)
db.<db-name>.delete(<search-condition>)

db.inventory.deleteMany({ status : "A" })
```

