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