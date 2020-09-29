# Introduction to MongoDB

MongoDB is a *NOSQL* database that helps in faster application development. This is also suitable for cases where there are lots of READs
and WRITEs. Allows application to grow easily as the stringent database constraints do not apply anymore.

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

# Commands

* *show dbs* lists the databases in the instance.
* *use shop* switches to the database shop if it exists or would create one for us.
* *db.products.insertOne( {"name":"Books", "qty":17} )* inserts the JSON document onto the collection. Also the collection products will be
created if it does not exist.
* *db.products.find()* gets all the documents in a given collection.

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





