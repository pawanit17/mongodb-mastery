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

`mongod` is the database service and `mongo` is the database client that we can use to connect to the database and run commands. Typically all the
backends talk to the `mongod`, which would transact with MongoDB database.

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

# Basic Commands

## Listings available databases
A MongoDB instance can hold multiple databases. These can be explore via the `show dbs` command that lists the databases.

```show dbs
admin       0.000GB
config      0.000GB
flights     0.000GB
local       0.000GB
shop        0.000GB
users_test  0.000GB
```

## Switching to a database
To switch to a specific database within the MongoDB you can use `use shop` command that switches to the database *shop* if it exists or would create one for us.

## CRUD Data
Once set into a specific database, we can do CRUD operations on Documents in a specific collection within the database.

### Creation/Insertion
If the collection specified in the command does not exist, it too would be created automatically.

#### insertOne
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

#### insertMany
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

### Read/Retrieval

#### find
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

#### find with filters

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

#### cursors
Find returns first 20 documents along with a cursor. It is so because if an application has a large nuber of documents, then it may not be sensible to return all of them at once. So, find returns a curson which we can use for requesting for the next page word of documents - pagination.
`db.passengers.find().toArray()` would return all the documents in a given collection.

### Update

#### updateOne
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

#### updateMany
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

### Delete
Delete is used for deleting documents from a collection.

#### deleteOne
***deleteOne*** deletes the first Document that matches a criterion.

	db.flightData.deleteOne( {"intercontinental": true} )
	{ "acknowledged" : true, "deletedCount" : 1 }
	db.flightData.find().pretty()
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

#### deleteMany
***deleteMany*** can be used to delete Documents that match a criterion.

	db.flightData.deleteMany({})
	{ "acknowledged" : true, "deletedCount" : 2 }
	db.flightData.find().pretty()

## Projections

Sometimes an usecase may only be interested in the details of few fields within all the documents in a collection. In such case sending all the data would be costly over the network. In this case, Projections help. Projections are constructs that help in showing only certain information.

    db.passengers.find() shows all the fields in all the documents.
	{
		"_id" : ObjectId("5f82ffdf893423df683faec6"),
		"name" : "Max Schwarzmueller"
	}
	{ "_id" : ObjectId("5f82ffdf893423df683faec7"), "name" : "Manu Lorenz" }
	{ "_id" : ObjectId("5f82ffdf893423df683faec8"), "name" : "Chris Hayton" }
	{ "_id" : ObjectId("5f82ffdf893423df683faec9"), "name" : "Sandeep Kumar" }
	{ "_id" : ObjectId("5f82ffdf893423df683faeca"), "name" : "Maria Jones" }
	{ "_id" : ObjectId("5f82ffdf893423df683faecb"), "name" : "Alexandra Maier" }
	{ "_id" : ObjectId("5f82ffdf893423df683faecc"), "name" : "Dr. Phil Evans" }
	{ "_id" : ObjectId("5f82ffdf893423df683faecd"), "name" : "Sandra Brugge" }
	{ "_id" : ObjectId("5f82ffdf893423df683faece"), "name" : "Elisabeth Mayr" }
	{ "_id" : ObjectId("5f82ffdf893423df683faecf"), "name" : "Frank Cube" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed0"), "name" : "Karandeep Alun" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed1"), "name" : "Michaela Drayer" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed2"), "name" : "Bernd Hoftstadt" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed3"), "name" : "Scott Tolib" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed4"), "name" : "Freddy Melver" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed5"), "name" : "Alexis Bohed" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed6"), "name" : "Melanie Palace" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed7"), "name" : "Armin Glutch" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed8"), "name" : "Klaus Arber" }
	{ "_id" : ObjectId("5f82ffdf893423df683faed9"), "name" : "Albert Twostone" }
	Type "it" for more

The first argument in find is the criteria in which the documents are to be choosen and the second argument lists the fields that are to be passed in the response.
The value 1 specifies that the field is to be sent in the response, the value of **0** indicates that it is not meant to be passed back.

    db.passengers.find({}, {name:1, _id:0}).pretty()
	{ "name" : "Max Schwarzmueller" }
	{ "name" : "Manu Lorenz" }
	{ "name" : "Chris Hayton" }
	{ "name" : "Sandeep Kumar" }
	{ "name" : "Maria Jones" }
	{ "name" : "Alexandra Maier" }
	{ "name" : "Dr. Phil Evans" }
	{ "name" : "Sandra Brugge" }
	{ "name" : "Elisabeth Mayr" }
	{ "name" : "Frank Cube" }
	{ "name" : "Karandeep Alun" }
	{ "name" : "Michaela Drayer" }
	{ "name" : "Bernd Hoftstadt" }
	{ "name" : "Scott Tolib" }
	{ "name" : "Freddy Melver" }
	{ "name" : "Alexis Bohed" }
	{ "name" : "Melanie Palace" }
	{ "name" : "Armin Glutch" }
	{ "name" : "Klaus Arber" }
	{ "name" : "Albert Twostone" }
	Type "it" for more

# Schema Design
Some conceptual back ground on Designing MongoDB applications.

## To schema or to not schema

MongoDB is schemaless. Consider the following two documents which fit in the same collection despite having different schema.

	db.products.insertOne({name:"A book", price: 12.99 } )
	{
		"acknowledged" : true,
		"insertedId" : ObjectId("5f84a2005536019ca108737e")
	}
	> db.products.insertOne({title:"T-Shirt", seller:{name:"Pavan", age:31}})
	{
		"acknowledged" : true,
		"insertedId" : ObjectId("5f84a2265536019ca108737f")
	}
	>
	> db.products.find().pretty()
	{
		"_id" : ObjectId("5f84a2005536019ca108737e"),
		"name" : "A book",
		"price" : 12.99
	}
	{
		"_id" : ObjectId("5f84a2265536019ca108737f"),
		"title" : "T-Shirt",
		"seller" : {
			"name" : "Pavan",
			"age" : 31
		}
	}

Although there are no restrictions on what you can insert to a collection, if unrelated data is inserted, over time the collection becomes heterogenous / not serving the purpose anymore. In real world applications, all the records in a given collection have more or less the same common fields with some additional ones for few documents.

Although this is allowed in MongoDB, most of the times, it would be beneficial to add some schema. Example, each blog post document should have value for owner. Which schema to use is entirely dependent on the problem at had.

## Datatypes

MogoDB has several data types like Double, String, Boolean, Date etc. More information here [MongoDB Documentation on BSON Types](https://docs.mongodb.com/manual/reference/bson-types/).

# Modelling relations

Basically, there are two ways in which related data could be modelled in Mongodb.

## Embedded Documents

Document containing another Document in MongoDB are referred to as Embedded Documents. Example, the information about a Product and its reviews. You will find this below. The important thing here to consider is, if you look at a Product, you are also interested in its reviews. That is why storing them together will give faster performance.

	db.inventory.insertMany([{
			"product_name": "JBL Wireless Headset",
			"cost": 2000.99,
			"best_price": 1000.99,
			"color": ["black", "blue"],
			"manufacturer": "JBL",
			"stock": 32,
			"no_of_sellers": 12,
			"reviews": [{
					"user": "Srividhyaa",
					"review": "I bought the headphones a week ago and having been using it nonstop ever since."
				},
				{
					"user": "Honest, Dil Se",
					"review": "Functional is the word i’d use for the overall design of the T460BT."
				}
			]
		},
		{
			"product_name": "Apple Watch Series 3",
			"cost": 2500.49,
			"best_price": 1800.49,
			"color": ["black", "red"],
			"manufacturer": "Apple",
			"stock": 100,
			"no_of_sellers": 10,
			"reviews": [{
					"user": "Meet",
					"review": "Nice watch. But little bit disappointment with this purchase...I didn’t get 5w power adapter with my watch which is shown in above image..."
				},
				{
					"user": "Anurag Pandey",
					"review": "Trust me I spent almost 4 months to decide weather I should go for this or not. Couple of times, I placed the order and then cancelled and again finally placed it through Amazon."
				}
			]
		},
		{
			"product_name": "PS4 Ultra Slim Bundle",
			"cost": 300,
			"best_price": 250,
			"color": ["black", "military"],
			"manufacturer": "Sony",
			"stock": 80,
			"no_of_sellers": 8,
			"reviews": [{
					"user": "Rushil G",
					"review": "First of all, Xbox is not for me as its exclusive game library is not as good, and the ones that are, can be played on PC!"
				},
				{
					"user": "Santhosh M",
					"review": "If you are choosing between a PlayStation on Xbox , I would recommend ps4 slim rather than a Xbox 1 standard , or even a Xbox 1 s."
				}
			]
		}
	])

### Points to note
1. Since the connected data is in the same Document, the number of READ operations will be reduced.
2. Sometime this approach can load the data that is not necessary. For example, to know what all Products are available in the inventory, in the example above, the reviews are also returned. This is not necessary strictly for our usecase.
3. Keep an eye on the 16MB document size limit for MongoDB.
4. To fix point 2, we can use **subset design pattern**. This is nothing but splitting the Document into two collections. `Reviews` could be a collection of its own with `Products` being the other collection. And using a JOIN, we can get the total result. But this usually increases the load time and the maintenance times.

## Referencing Documents
For example, if there is a sale going on in Amazon, then they would be something like the following:
1. 10% off on HSBC Net Banking
2. 10% cashback on HDFC Credit Cards
3. 5% cashback on Amazon ICICI Pay Credit Cards etc.

Lets create a collection called `offers` and insert the above three offers.

	db.offers.insertMany([
        {
	    "payment_type": "net-banking",
	    "offer": "10% off on HSBC Net Banking"
	},
	{
	    "payment_type": "Credit Cards",
	    "offer": "10% cashback on HDFC Credit Cards"
	},
	{
	    "payment_type": "Credit Cards",
	    "offer": "5% cashback on Amazon ICICI Pay Credit Cards etc"
	}
	])

And we will also be able to get their IDs now.

	{
		"acknowledged" : true,
		"insertedIds" : [
			ObjectId("5f906cb86ae1288bbeb11504"),
			ObjectId("5f906cb86ae1288bbeb11505"),
			ObjectId("5f906cb86ae1288bbeb11506")]
	}

Now we model the inventory collection content with offers available to them. As you can see, there are different offers available to different products. Instead of using the
offer information as an embedded document, we seperate out via a reference. This is helpful as if in future 

    db.inventory.insertMany([
    	{
		"product_name": "JBL Wireless Headset",
		"cost": 2000.99,
		"offers": [ ObjectId("5f906cb86ae1288bbeb11504"), ObjectId("5f906cb86ae1288bbeb11505") ]
	},
	{
		"product_name": "Apple Watch Series 3",
		"cost": 2500.49,
		"offers": [ ObjectId("5f906cb86ae1288bbeb11505") ]
	},
	{
		"product_name": "PS4 Ultra Slim Bundle",
		"cost": 300,
		"offers": [ ObjectId("5f906cb86ae1288bbeb11506") ]
	}
    ])

### Points to note
1. The reference to connect the islands of data can be put on either side. Best practise is to put it on the side that is going to have more increase. For example, in our case, since the reviews for a product are unbounded, it is better to have the `Review` document have a reference to the `Product`.
2. This approach helps us in preventing the maintenance of a mutable growing array - if the reference had been on the `Products` document ( array of reviews which have to be updated each time there is an addition ).
3. Extra time will be taken for relating the two pieces of data together unlike embedded Document.

And in general, relationships themselves are of 3 types.

## One to One
1. A User can only have one ShoppingCart. And a ShoppingCart could only belong to a single User.
2. A Review of a Product on an E-commernce site could belong only one User. And a User can have only a single review per a Product.

[MongoDB Documentation on 1-1 Mappings](https://docs.mongodb.com/manual/tutorial/model-embedded-one-to-one-relationships-between-documents/)

## One to Many
1. A Customer can place many orders. But a single Order could only be placed by a single Customer.
2. A User may have any number of email IDs / phone numbers but an email ID / phone number belongs to a single User.

[MongoDB (Embedded) Documentation on 1-* Mappings](https://docs.mongodb.com/manual/tutorial/model-embedded-one-to-many-relationships-between-documents/)

## Many to Many
1. Any number of Orders could be placed on a Product. And an Order could have any number of Products.
2. A User may have any number of Products in his Wishlist, and a Product could be part of multiple User's Wishlist.

[MongoDB (Referenced) Documentation on 1-* Mappings](https://docs.mongodb.com/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/)

## Aggregations

Aggregation operations group values from multiple documents together, and can perform a variety of operations on the grouped data to return a single result.

Ex: The total amount of product value obtained from selling different Products at an inventory system.

