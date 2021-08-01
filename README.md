# MongoDB-Mongoose-Notes
These are the notes for MongoDB and Mongoose, which I prepared while learning.

-----------------------------------------------------------------------------------------------------------------

MongoDB:

/* CREATE DATA IN DATABASE*************************************************************************/

cmd:
mongod
// use mongod command to start mongo db server to the default port.


then open a new tab in the terminal and use, 
cmd:
mongo
// mongo cmd opens up the mongo shell by which we can interact with the mongoDB


cmd:
help
// help command prints the helpful commands one can use with mongo db


cmd:
show dbs
// the show dbs command prints the names of the dbs created that we currently have in our local systems.
// by default mongoDB comes with 3 databases: 
// admin, config & local.


cmd:
use <dbName>
// the use command is used to create a new db and also switches to that db in order to use it.
// if the db is empty, then it is not shown in the db list via show dbs command.


cmd:
db
// the db command tells, on which db the user is working currently.


cmd:
db.<collection>.insertOne(
    {
        _id: 1,
        name: "Pen",
        price: 1.20
    }
)

// this command inserts an entry to a particular collection in the db. If the collection doesn't exists then it creates the collection first and then inserts that one particular item into it. Inside the insertOne function, a javascript object is passed as key value pairs resembles the field name and value.
// a collection in mongoDB is like tables in sqlDB, and it consists of documents and the documents resembles a single record in the sqlDB.


cmd:
show collections
//this command shows all of the collections under the current db.



cmd:
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)

//Inserts multiple documents into a collection.
/*
Parameter	    Type	    Description
document	    document	An array of documents to insert into the collection.

writeConcern	document	Optional. A document expressing the write concern. Omit to use the 
                            default write concern.

                            Do not explicitly set the write concern for the operation if run in a transaction. To use write concern with transactions, see Transactions and Write Concern.

ordered	        boolean	    Optional. A boolean specifying whether the mongod instance should 
                            perform an ordered or unordered insert. Defaults to true.


Returns:	
A document containing:
A boolean acknowledged as true if the operation ran with write concern or false if write concern was disabled
An array of _id for each successfully inserted documents
*/




/* READ DATA FROM DATABASE*************************************************************************/


cmd:
db.<collection>.find({field: "value"})

//this command returns the particular documents/records within the collection.
//eg: db.products.find({name: "Pencil"})


cmd:
db.<collection>.find({price: {$gt: 1}})

//this command finds all of the documents which has the price greater than 1. $gt is a query selector used for comparison. It Matches values that are greater than a specified value.


cmd: 
db.<collection>.find(<query>, <projection>)

// here query refers to the condition on the basis of which we want to fetch data.
// and, projection refers to the corresponding fields we want to fetch out of the document.

eg: db.products.find({_id: 1}, {name: 1})
//here, it will find the record with _id 1 and print the following:
// { "_id" : 1, "name" : "Pen" }
// by default it will print the id but we can control what we want to print and what we want to avoid, just by assigning 1 and 0 in the projection to print and avoid respectively.

eg: db.products.find({_id: 1}, {name: 1, _id: 0})
//here, in the projection: name has a corresponding value of 1 which means we want to print the value of name, but _id has a corresponding value of 0 which means we don't want to print the value of _id.

//Please note if we don't specify any projection then we get all of the fields in the colleciton.




/* ******UPDATE DATA INSIDE THE DATABASE ********************************************************/


cmd:
db.collection.updateOne(<filter>, <update>, <options>)
db.collection.updateMany(<filter>, <update>, <options>)
db.collection.replaceOne(<filter>, <update>, <options>)

eg: db.products.updateOne({_id: 1}, {$set: {stock: 32}})
//here updateOne method is used to update one record/document, the 1st parameter here is filter on the basis of which we find the record/document to be updated, the update paramenter has values to be updated. Here, in this case $set allows us to set a new field and value into the document.




/* ******DELETE DATA FROM THE DATABASE ********************************************************/

cmd:
db.<collection>.deleteOne(<condition>)
db.<collection>.deleteMany(<condition>)

eg: db.products.deleteOne({_id: 2})
//here, using deleteOne or deleteMany method we can delete the document/documents which satisfy the condition we provide inside the parenthesis.




/* ****** RELATIONSHIPS IN MONGODB ********************************************************/

cmd:
eg:
db.products.insert(
    {
        _id: 3,
        name: "Rubber",
        price: 1.30,
        stock: 43,
        reviews: [
            {
                authorName: "Sally",
                rating: 5,
                review: "Best rubber ever!"
            },

            {
                authorName: "Johnny"
                rating: 5,
                review: "Awesome Rubber, Loved it!!"
            }
        ]
    }
)

// here, the insert method is inserting a new document, and this document consists of a new field namely 'reviews'.
// reviews is of type array, as one product can have multiple reviews/reviewers. This explains One to Many RELATIONSHIP in MongoDB. As one document consists of multiple documents.





  
---------------------------------------------------------------------------------------------------------------------------------------------------
  
Mongoose:

    
Mongoose is a package to work with MongoDB, and it's called an ODM (Object Document Mapper).

const mongoose = require('mongoose');
// to start working with mongoose in your app.js

// Connection URL
mongoose.connect("mongodb://localhost:27017/fruitsDB", { useNewUrlParser: true, useUnifiedTopology: true });

// first we define a schema for our collection in the db:
const fruitSchema = new mongoose.Schema({
    name: String,
    rating: Number,
    review: String
});



// *********** INSERT INTO DATABASE USING MONGOOSE**************




// then we use this schema to create a mongoose model 
// The model method takes 2 parameters, the first parameter is a string and it specifies the name of the collection that is going to comply with the defined schema, the mongoose way of defining the name of the collection is by defining the singular name of the collection and mongoose will very cleverly convert this string into a pluralise form to create this collection, by doing this mongoose will create a collection called Fruits.
// the second parameter is the schema which we have defined and want to use for this particular collection.

const Fruit = mongoose.model("Fruit", fruitSchema);



// lowercase 'f'ruit is used to define a new document namely fruit which follows the Fruit model defined above:

const fruit = new Fruit({
    name: "Apple",
    rating: 7,
    review: "Pretty solid as a fruit."
});


// calls the save method in mongoose to save this fruits document into the fruits collection in the fruitsDb.

fruit.save();



// here are multiple fruit documents:

const kiwi = new Fruit({
    name: "Kiwi",
    score: 10,
    review: "The best fruit!"
});

const orange = new Fruit({
    name: "Orange",
    score: 4,
    review: "Too sour for me"
});

const banana = new Fruit({
    name: "Banana",
    score: 3,
    review: "Weird Texture"
});


// to insert multiple documents to the collection at once use the following method with the model created:
// here Fruit is a model 
/* model.insterMany([array of documents to be saved], function(err){
    if (err){
        console.log(err);
    }
    else 
        console.log("success");
});
*/

Fruit.insertMany([kiwi, orange, banana], function(err) {
    if (err) {
        console.log(err);
    }
    else {
        console.log("Successfully saved all the fruits!");
    }
});



// *********** READ FROM THE DATABASE ************************************************

/* SYNTAX:
<model>.find(function(err, <collections>){
    if (err)
        console.log(err);
    else
        console.log(<collections>)
})

*/

//eg:
Fruit.find(function(err, fruits){
    if (err)
        console.log(err);
    else
        mongoose.connection.close(); // close the connection once all of the tasks are completed.
        console.log(fruits);
});


// to print just the name of the fruits:
Fruit.find(function(err, fruits){
    if (err)
        console.log(err);
    else
    {
        mongoose.connection.close(); // close the connection once all of the tasks are completed.
        // console.log(fruits);
        fruits.forEach(function(fruit){  // here the forEach loop accepts a callback function with 
                                            a single document out of all the fruits collection & then prints out fruit.name:
            console.log(fruit.name);
        });
    }
});


// DATA VALIDATIONS **************
const fruitSchema = new mongoose.Schema({
    name: {
        type: String,
        required: [true, "Please check your data entry, no name specified!"]
    },  // either use true or 1.

    rating: {
        type: Number,
        min: 1,
        max: 10
    },

    review: String
});



//********* UPDATE DATA USING MONGOOSE *****************

// SYNTAX:
<model>.updateOne({conditionForSearch}, {fieldToBeUpdated}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("success");
});


//eg:
Fruit.updateOne({_id: "5fda801a747ef9247442b8ca"}, {name: "Peach"}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("Successfully updated the document.");
});




// ************ DELETE DATA USING MONGOOSE *******************

// SYNTAX:
<model>.deleteOne({conditionForSearch}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("success");
});


//eg:
Fruit.deleteOne({name: "Peach"}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("Successfully deleted the document.");
});


// DELETE MANY************

//SYNTAX:
<model>.deleteMany({conditionForSearch}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("success");
});


//eg:
Person.deleteMany({name: "John"}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("Successfully deleted all the documents.");
});



// ***** RELATIONSHIPS AND EMBEDDING DOCS. USING MONGOOSE *******************

const personSchema = new mongoose.Schema({
    name: String,
    age: Number,
    favouriteFruit: fruitSchema             // The personSchema has fruitSchema embedded into it 
                                            //  thus formig a relationship.
});

const pineapple = new Fruit({
    name: "Pineapple",
    rating: 9,
    review: "Great fruit."
});
pineapple.save();

const person = new Person({
    name: "Amy",
    age: 12,
    favouriteFruit: pineapple
});
person.save();


const mango = new Fruit({
    name: "Mango",
    rating: 10,
    review: "Really a great one."
});

mango.save();

Person.updateOne({name: "John"}, {favouriteFruit: mango}, function(err) {
    if (err)
        console.log(err);
    else
        console.log("Successfully updated the document.");
});
