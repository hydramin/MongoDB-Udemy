

Numbers:
    int32
    int64
    double
    NumberDecimal: high precision, 34 decimal places
ObjectId
    is unique id string, it is based on a timestamp
ISODate
    is a date datatype
Timestamp
    is automatically created and mostly used internally
Embeded Documents
    documents inside documents
Array
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

## Data Schemas and Data Modelling
