**Schemas And Relations**

*Dropping Database*
use databaseName
db.dropDatabase()

*Dropping a Collection*
db.collection-name.drop()

- We need to find the relationship between the entities in our database. This part includes:
    - Document schemas and data types
    - Modeling Relations between documents
    - Validating a schema upon storage

*Why do we need Schema*
- Storage doesn't enforce a schema but practically we need a schema to work with. 
    - Some documents may contain extra information, or all of the documents have the same schema.

## Datatypes
**Text**
    - In quotation
    - max document size is 16 MB
**Boolean**
    - T/F
**Numbers:**
    int32
    int64
    double
    NumberDecimal: high precision, 34 decimal places
**ObjectId**
    is unique id string, it is based on a timestamp
**ISODate**
    is a date datatype
**Timestamp**
    is automatically created and mostly used internally
**Embeded Documents**
    documents inside documents
**Array**
    array of anything: Documents, arrays, ...

## Playing with the databases
```js
    db.companies.insertOne(
        {
            name: "Fresh Apples Int", // text
            isStartup: true,    // boolean
            employees: 33, // stored as a floating number, int32
            funding: 12341234123412341234, // long int 64, the max number js uses is limited to an int64 max value
            details: {
                ceo: "Mark"         // embedded document
            },
            tags: ["super", {id: "kkk"}, 23], // array
            foundingDate: new Date(), // current date
            createdTime: new Timestamp()    //
        }
    )
    /*
        The shell is based on js and it relies on the js datatypes, depending on the driver for the particular language ...
    */
```

- below numbers will be discussed

```js
    db.stats();
        // this function returns the stats of the currently used database
    // inserting a number is an 64bit floating number because of js
    /*
        we use special functions to insert int32 numbers
    */
   db.numbers.insertOne({a: NumberInt(1)})
    /*
        To see the type of datatype of some data we use the js typeof function
        
        typeof <any data>
    */
    typeof db.numbers.findOne().a
    typeof {a: 4}
    typeof "h"

    // to insert int32 and int64 or int and long use:
    NumberInt(12) 
    NumberLong(12341234123412341234)
    // simply adding a number adds a double
    NumberDecimal(12.345) // very high precision
```
- http://mongodb.github.io/node-mongodb-native/3.1/api/Long.html

## Data Schemas and Data Modelling
- check out lecture 39

## HOW TO STORE RELATED DATA   
- Embedded document or References
```js
    //references
    {
        user: "max",
        ownedBooks: [{Book1-id},{Book2-id}]
    }
```
## When to use embedded documents
Data model: 
    - Patients and Patient-Summary
    - This is a one to one data model so we can use embedded document mode
    - Patient(name, age, healthSummary)
    - Summary(diseases: [... list of all diseasases])
    - Patient collection and Summary collection is not necessary to be split. But if our interest is to analyze patients separately and we want to analyze diseases summaries separately we should separate it.
    *One To Many*
    - Question Thread and Answers
        One question but multiple answers, so how to model the database 
        - Embeded or
        - Separate Question and Answers?
        - The best approach is an embedded document.
    - Database of Cities and Residents
        - one person can ony reside in one city. One city many residents
        - City ---> Resident , one to many
        - Embedded document
    - Customer vs Product many to many relationship. It is Reference style data
        - Customer( array of product ids)
        - Product( array of customers ids )

        - If the duplicated data has to be updated at all places, then reference is the best. If Duplication is needed in the app embedded is not bad.
    - Book vs Author relationship
        - Many to many, When an authors info changes it should change every where. So Embedded is not good. Reference is better.
    - City vs Residents data cant be embedded because the 16MB limit will be reached quite easily

    - Aggregation in mongo using the $lookUp() aggreregation function. 
```js
    //lookup is used to lookup the reference data in a Reference based schema design
    // example schema
    //Customers vs favBooks relation
    db.customer.insertMany(
        [{
            name: "Amin",
            favBooks: [
                'id1',
                'id2'
            ]
        },
        {
            name: "Nadia",
            favBooks: [
                'id2'
            ]
        }]
    )
    
    //Books schema

    db.book.insertMany( 
        [{
            _id: "id1",
            title: "Harry Potter",
            author: "J.K Rowling"
        },
        {
            _id: "id2",
            title: "The Davinci Code",
            author: "Dan Brown"
        }]
    )

    // To find the details of ids for Nadia, we can use aggregation function lookup
    db.customer.aggregate([
        $lookup : {
            from: "book", // the destination collection
            localField: "favBooks", // local field of reference (foreign key)
            foreignField: "_id",// which field of the ref is used as a reference
            as: "favBookData" // the output field containing the aggregated data
        }
    ])

    //the look up aggregation would be like this
    db.customer.aggregate(
        [
            {
                $lookup : {
                    from:"book",
                    foreignField:"_id",
                    localField:"favBooks",
                    as: "aggregated"
                }
            }
        ]
    )
```

Example Project: A Blog
    - There are **Users** users can **Post** a blog and they can write **Comment** on *Posts*
    - The application should allow users to 
        Create a post
        Edit a post
        Delete a post
        Fetch all Posts
        Fetch a Post and
        Comment on a Post
    - Schema 1
        user(*uid*, name, [pids])
        post(*pid*, content, uid, [cid])
        comment(*cid*, content, pid, by-uid)

```js
db.user.insertMany([
    {
        name: "Amin",
        posts: [

        ]
    },
    {
        name: "Afia",
        posts: [

        ]
    },
    {
        name: "Nadia",
        posts: [

        ]
    }
])

db.post.insertMany([
    {
        content: "this is the posted content",
        uid: "",
        comments: [

        ]
    },
    {
        content: "this is the second content",
        uid: "",
        comments: [

        ]
    }
])
// comment(*cid*, content, pid, by-uid)

db.comment.insertMany([
    {
        content: "this is comment 1",
        pid: "",
        postedBy: "" //uid
    }
])
```
- Schema 2
        user(*uid*, name)
        post(*pid*, content, uid)
        comment(*cid*, content, in-pid, by-uid)


```js
db.user.insertMany([
    {
        name: "Amin"        
    },
    {
        name: "Afia"
    },
    {
        name: "Nadia"
    }
])

/*
ObjectId("5d733684e38eff2c7e8dbbae"),
ObjectId("5d733684e38eff2c7e8dbbaf"),
ObjectId("5d733684e38eff2c7e8dbbb0")
*/
db.post.insertMany([
    {
        content: "this is the posted content 1",
        uid: ObjectId("5d733684e38eff2c7e8dbbae")        
    },
    {
        content: "this is the second content 2",
        uid: ObjectId("5d733684e38eff2c7e8dbbaf")
    },
    {
        content: "this is the posted content 3",
        uid: ObjectId("5d733684e38eff2c7e8dbbae")        
    },
    {
        content: "this is the second content 4",
        uid: ObjectId("5d733684e38eff2c7e8dbbb0")
    }
])
// comment(*cid*, content, pid, by-uid)
/*
ObjectId("5d733759e38eff2c7e8dbbb1"),
ObjectId("5d733759e38eff2c7e8dbbb2"),
ObjectId("5d733759e38eff2c7e8dbbb3"),
ObjectId("5d733759e38eff2c7e8dbbb4")
*/

/*
ObjectId("5d733684e38eff2c7e8dbbae"),
ObjectId("5d733684e38eff2c7e8dbbaf"),
ObjectId("5d733684e38eff2c7e8dbbb0")
*/
db.comment.insertMany([
    {
        content: "this is comment 1",
        pid: ObjectId("5d733759e38eff2c7e8dbbb1"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbae")
    },
    {
        content: "this is comment 2",
        pid: ObjectId("5d733759e38eff2c7e8dbbb2"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbaf")
    },
    {
        content: "this is comment 3",
        pid: ObjectId("5d733759e38eff2c7e8dbbb2"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbae")
    },
    {
        content: "this is comment 4",
        pid: ObjectId("5d733759e38eff2c7e8dbbb1"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbae")
    },
    {
        content: "this is comment 5",
        pid: ObjectId("5d733759e38eff2c7e8dbbb4"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbaf")
    },
    {
        content: "this is comment 6",
        pid: ObjectId("5d733759e38eff2c7e8dbbb3"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbb0")
    },
    {
        content: "this is comment 7",
        pid: ObjectId("5d733759e38eff2c7e8dbbb4"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbb0")
    },
    {
        content: "this is comment 8",
        pid: ObjectId("5d733759e38eff2c7e8dbbb3"),
        postedBy: ObjectId("5d733684e38eff2c7e8dbbaf") 
    },
])
```
query on the blog database
1. Find all posts made by Amin
```js
    db.user.findOne(
        {
            name: "Amin"
        }
    )._id

    db.post.find(
        {
            uid: ObjectId("5d733684e38eff2c7e8dbbae")
        }
    ).pretty()
```
Schema validation
- use a schema to use as a validation criteria and accept or reject a Document.
- Strict: all inserts and updates
- moderate: all inserts are checked and updates are checked if the previous entry is correct
- if it fails: pass error message and reject or send a warning message.
- Collections can be created "Lazily" just as we did above or we can use the formal createion method
```js
db.createCollection(
    "name-of-collection",
    validator: { // specify the type of validator
        $jsonSchema: { // name the type of schema
            bsonType: //could be {string, objcetId, number, objet}
            required: //list array of required fields
            properties: { // describe the properties or rows or document rows
                ...
            }
        }
    }
)
// the post collection can be recreated like this
db.createCollection(
    "post",
    {
        validator: {
            $jsonSchema: {
                bsonType: 'object',
                required: ["title", "text", "creator", "comments"],
                properties: {
                    title:{ 
                        bsonType: "string",
                        description: "is a required string"
                    },
                    text: {
                        bsonType: "string",
                        description: "required string"
                    },
                    creator: {
                        bsonType: "objectId",
                        description: "array or ids"
                    }, 
                    comments: {
                        bsonttype: "array",
                        description: "required array",
                        items: {
                            bsonType: "object",
                            required: ["content", "author"]
                            properties: {
                                content: {
                                    bsonType: "object",
                                    description: "required string of blog content" 
                                },
                                author: {
                                    bsonType: "objectId":
                                    description: "required string"
                                }
                            }
                        }
                    }
                }
            }
        }
    }
    )
```
- passing in an erroneous data throws an error.
- To change the validation action from the default strict mode to a warnning mode we do this:
- to change the validator without recreating the collection we use .runCommand
```js
// db.runCommand runs an administrative command
db.runCommand(
    collMod: "name of collection",
    validator: {... just like the above},
    validationAction: "warn" // or "error"
)
```