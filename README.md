# Introduction to MongoDB

MongoDB is a *NOSQL* database that helps in faster application development. This is also suitable for cases where there are lots of READs and WRITEs. Allows application to grow easily as the stringent database constraints do not apply anymore.

Data is stored in Collections in the form of Documents. A typical Document looks as follows:

    {
      "name": "pavan",
      "age":32,
      "address":{
        "place": "Hyderabad"
      },
      "hobbies":[
        { "name": "cricket" },
        {"name": "programming"}
      ]
    }

We can see that the information about hobbies is stored in the same collection as the rest of the data and so the look up would be faster.
In traditional SQL way, hobbies could be a seperate table and then there would be a need to perform a JOIN which are expensive.

*mongod* is the database service and *mongo* is the database client that we can use to connect to the database and run commands. Typically all the
backends talk to the *mongod*, which would transact with MongoDB database.

[Drivers are available](https://docs.mongodb.com/drivers/) for connecting to MongoDB from a variety of programming languages - C, Java, NodeJS, Python etc.


Another example illustrating the flexibility of MongoDB for a *Books* collection in an *Inventory* database.

    {
            "_id" : ObjectId("5f738b15f1400095226b9512"),
            "name" : "Encyclopedia",
            "prices" : 122.99
    }
    {
            "_id" : ObjectId("5f738b4ef1400095226b9513"),
            "name" : "Harry Potter",
            "prices" : 42.00,
            "Author" : "J.K.Rowling"
    }
    {
            "_id" : ObjectId("5f738caef1400095226b9514"),
            "name" : "Lord of the Rings",
            "prices" : 229.99,
            "Author" : "Tolkien",
            "details" : [
                    {
                            "category" : "Thriller"
                    },
                    {
                            "category" : "Fiction"
                    }
            ]
    }

# Exploring databases
A MongoDB instance can hold multiple databases. These can be explore via the *show dbs* command that lists the databases.

```show dbs
admin       0.000GB
config      0.000GB
flights     0.000GB
local       0.000GB
shop        0.000GB
users_test  0.000GB
```

To switch to a specific database within the MongoDB you can use *use shop* command that switches to the database shop if it exists or would create one for us.

# CRUD Data

Once set into a specific database, we can do CRUD operations on Documents in a specific collection within the database.

## Creation/Insertion
If the collection specified in the command does not exist, it too would be created automatically.

### insertOne
***insertOne()*** command would insert a Document into a specified Collection. 

    db.flightData.insertOne({
        "departureAirport": "MUC", 
        "arrivalAirport": "SFO", 
        "aircraft": "Airbus A380", 
        "distance": 12000, 
        "intercontinental": true 
    })

  Result: One doucument added to Collection.
  db.flightData.find().pretty()
    {
            "_id" : ObjectId("5f739838f1400095226b9515"),
            "departureAirport" : "MUC",
            "arrivalAirport" : "SFO",
            "aircraft" : "Airbus A380",
            "distance" : 12000,
            "intercontinental" : true
    }

MongoDB uses BSON instead of JSON for storing data in database. For example, the *ObjectId* entry is not a valid JSON object. '_id' is a unique identifier that is added by MongoDB. This includes timestamp data in it for its generation. So this determines which Document was older.

### insertMany
***insertMany()*** would insert multiple Documents to the collection.

        db.flightData.insertMany([
        {
            "departureAirport" : "LIV",
            "arrivalAirport" : "CHS",
            "aircraft" : "Airbus A100",
            "distance" : 2000,
            "intercontinental" : false
        },
        {
            "departureAirport" : "BRO",
            "arrivalAirport" : "LOS",
            "aircraft" : "Airbus A380",
            "distance" : 1000,
            "intercontinental" : false
         }])

## Read/Retrieval

### find
***find()*** command would list the Documents in a Collection.

    db.flightData.find().pretty()
    {
            "_id" : ObjectId("5f739838f1400095226b9515"),
            "departureAirport" : "MUC",
            "arrivalAirport" : "SFO",
            "aircraft" : "Airbus A380",
            "distance" : 12000,
            "intercontinental" : true
    }
    {
            "_id" : ObjectId("5f73a1e4f1400095226b9516"),
            "departureAirport" : "LIV",
            "arrivalAirport" : "CHS",
            "aircraft" : "Airbus A100",
            "distance" : 2000,
            "intercontinental" : false
    }
    {
            "_id" : ObjectId("5f73a1e4f1400095226b9517"),
            "departureAirport" : "BRO",
            "arrivalAirport" : "LOS",
            "aircraft" : "Airbus A380",
            "distance" : 1000,
            "intercontinental" : false
    }

### find with filters

***find() with filters*** help in narrowing the Documents to be extracted.

Query to get Documents whose 'distance' is 1000.
    db.flightData.find( { "distance": 1000 }).pretty()
    {
        "_id" : ObjectId("5f73a1e4f1400095226b9517"),
        "departureAirport" : "BRO",
        "arrivalAirport" : "LOS",
        "aircraft" : "Airbus A380",
        "distance" : 1000,
        "intercontinental" : false
    }

Query to get Documents whose 'distance' is greater than 1000.
	db.flightData.find({"distance":{$gt:1000}}).pretty()
	{
		"_id" : ObjectId("5f739838f1400095226b9515"),
		"departureAirport" : "MUC",
		"arrivalAirport" : "SFO",
		"aircraft" : "Airbus A380",
		"distance" : 12000,
		"intercontinental" : true
	}
	{
		"_id" : ObjectId("5f73a1e4f1400095226b9516"),
		"departureAirport" : "LIV",
		"arrivalAirport" : "CHS",
		"aircraft" : "Airbus A100",
		"distance" : 2000,
		"intercontinental" : false
	}

## Update

### updateOne
***updateOne*** method can be used to update one document. Even if multiple matches are encountered, it only updates the first one.

	db.flightData.updateOne( { "_id": ObjectId("5f739838f1400095226b9515") }, {$set:{delayed:true}})

	db.flightData.find().pretty()
	{
		"_id" : ObjectId("5f739838f1400095226b9515"),
		"departureAirport" : "MUC",
		"arrivalAirport" : "SFO",
		"aircraft" : "Airbus A380",
		"distance" : 12000,
		"intercontinental" : true,
		"delayed" : true
	}
	{
		"_id" : ObjectId("5f73a1e4f1400095226b9516"),
		"departureAirport" : "LIV",
		"arrivalAirport" : "CHS",
		"aircraft" : "Airbus A100",
		"distance" : 2000,
		"intercontinental" : false
	}
	{
		"_id" : ObjectId("5f73a1e4f1400095226b9517"),
		"departureAirport" : "BRO",
		"arrivalAirport" : "LOS",
		"aircraft" : "Airbus A380",
		"distance" : 1000,
		"intercontinental" : false
	}

### updateMany
***updateMany*** method can be used to update many documents.

	db.flightData.updateMany( { "intercontinental": false }, {$set:{intercontinental:true}})
	{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }
	db.flightData.find().pretty()
	{
		"_id" : ObjectId("5f739838f1400095226b9515"),
		"departureAirport" : "MUC",
		"arrivalAirport" : "SFO",
		"aircraft" : "Airbus A380",
		"distance" : 12000,
		"intercontinental" : true,
		"delayed" : true
	}
	{
		"_id" : ObjectId("5f73a1e4f1400095226b9516"),
		"departureAirport" : "LIV",
		"arrivalAirport" : "CHS",
		"aircraft" : "Airbus A100",
		"distance" : 2000,
		"intercontinental" : true
	}
	{
		"_id" : ObjectId("5f73a1e4f1400095226b9517"),
		"departureAirport" : "BRO",
		"arrivalAirport" : "LOS",
		"aircraft" : "Airbus A380",
		"distance" : 1000,
		"intercontinental" : true
	}

# Commands

* *db.products.insertOne( {"name":"Books", "qty":17} )* inserts the JSON document onto the collection. Also the collection products will be
created if it does not exist.
* *db.products.find()* gets all the documents in a given collection.

    db.flightData.find().pretty()
    {
            "_id" : ObjectId("5f739838f1400095226b9515"),
            "departureAirport" : "MUC",
            "arrivalAirport" : "SFO",
            "aircraft" : "Airbus A380",
            "distance" : 12000,
            "intercontinental" : true,
            "delayed" : true
    }
    




