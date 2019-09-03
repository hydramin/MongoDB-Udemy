# Document and CRUD
- Collections and Documents

Databases: one or more databses on the server
**Collections**: 
    - equivalent to a table
**Documents**: 
    - held in a collection, one or more documents in a collection. they are like a tuple.
Change port when running mongod
    mongod --port 8888
    mongo --port 8888
    - both mondod and mongo should run on the same port

Insertion
- use this data to insert the data one by one into the flights database and flightData collection
```json

[
  {
    "departureAirport": "MUC",
    "arrivalAirport": "SFO",
    "aircraft": "Airbus A380",
    "distance": 12000,
    "intercontinental": true
  },
  {
    "departureAirport": "LHR",
    "arrivalAirport": "TXL",
    "aircraft": "Airbus A320",
    "distance": 950,
    "intercontinental": false
  },
  {
    "departureAirport": "MUC",
    "arrivalAirport": "SFO"    
  },
  {
    "_id": "asdf_fdsa",    
    "aircraft": "Airbus A380",
    "distance": 12000,
    "intercontinental": true
  },
]

```

JSON vs BSON
- The mongo drivers change the json data into a binary json data
- why bson is easier:
    - it supports many datatypes
    - it is faster to query

- to assign ids we just add the **_id** into our tuple/Document or object. 
- using the same id upon insertion throws an error.

CRUD Queries

Create
    - insertOne(data, options)
    - insertMany(data, options)
Read
    - find(filter, options)
    - findOne(filter, options)
Update
    - updateOne(filter, data, options)
    - updateMany(filter, data, options)
    - replaceOne(filter, date, options)
Delete
    - deleteOne(filter, options)
    - deleteMany(filter, options)


Sample
db.flightData.insertOne({...})
Insert many:
    db.flightData.insertMany([{...},{...}])
db.flightData.find({...})
    same for all those that take filter
Update one
    db.updateOne({_id:"asdf_fdsa"}, {$set: {aircraft: "Boeing 777"} })
    - $set: is a reserved keyword, without it it wont add or update
    - gives error when it is not with $set
Update whole document
    db.update(
        {
            _id : ObjectId("5d6d42bc4f7a86fe54c1f5fc")
        },
        {
            delayed: true
        })
    - it removes all document entries and and replaces it with the given
Instead of using update() we should use replaceOne()
    db.flightData.replaceOne(
        {
            "_id" : ObjectId("5d6d42bc4f7a86fe54c1f5fe")
        },
        {
            departure: "AAD",
            distance: 8000,
            aircraft: "Concord 09"
        }
    )
modify a document, equivalent to adding a column
    db.flightData.updateMany({}, {$set: {marker: "toDelete"}})
delete many documents 
    db.flightData.deleteMany(
        {marker: "toDelete"}        
    )
find by filtering exact value
    db.flightData.find(
        {
            intercontinental: true
        }
    )
find by comparison
    - $gt, $lt, $ne, $eq keywords used to compare
    - db.flightData.find(
        {
            distance: {$le: 900}
        }
    )

Passengers data:
```json
[
  {
    "name": "Max Schwarzmueller",
    "age": 29
  },
  {
    "name": "Manu Lorenz",
    "age": 30
  },
  {
    "name": "Chris Hayton",
    "age": 35
  },
  {
    "name": "Sandeep Kumar",
    "age": 28
  },
  {
    "name": "Maria Jones",
    "age": 30
  },
  {
    "name": "Alexandra Maier",
    "age": 27
  },
  {
    "name": "Dr. Phil Evans",
    "age": 47
  },
  {
    "name": "Sandra Brugge",
    "age": 33
  },
  {
    "name": "Elisabeth Mayr",
    "age": 29
  },
  {
    "name": "Frank Cube",
    "age": 41
  },
  {
    "name": "Karandeep Alun",
    "age": 48
  },
  {
    "name": "Michaela Drayer",
    "age": 39
  },
  {
    "name": "Bernd Hoftstadt",
    "age": 22
  },
  {
    "name": "Scott Tolib",
    "age": 44
  },
  {
    "name": "Freddy Melver",
    "age": 41
  },
  {
    "name": "Alexis Bohed",
    "age": 35
  },
  {
    "name": "Melanie Palace",
    "age": 27
  },
  {
    "name": "Armin Glutch",
    "age": 35
  },
  {
    "name": "Klaus Arber",
    "age": 53
  },
  {
    "name": "Albert Twostone",
    "age": 68
  },
  {
    "name": "Gordon Black",
    "age": 38
  }
]

```
using a find() on a big data only gives the partial data and gives a cursor that points to the next data. if the data is too big we can use the cursor to navigate through the remaining data.
    - .find().toArray() gives all the data

    - .find().forEach(...) is used to execute some function on each data/document. we pass an arrow function/ lambda function in.
    - db.passengers.find().forEach(
        (passenger) => {printjson(passenger)}
    )
    - the pretty() function is a function of the cursor returned by the find() function.

    - [a link](https://docs.mongodb.com/manual/reference/method/js-cursor/)

**Projection**
- is selecting a particular column. in the terms of mongodb we want to choose only certain key-value pairs.
    - Using find(): we include an options argument after the filter
        - db.passengers.find({}, {name: 1}): 
            {} means all data, and {name: 1} means include name. adding {_id: 0} would exclude the _id from being displayed as an output.
**Embedded Documents**
    - documents can be nested upto 100 nests
    - Arrays can be treated as documents 
    - Ex: Add an address sub document to each passenger above
        db.passengers.updateMany(
            {}, 
            {
                $set: {
                        Address: {
                            street_name: "Allenby Ave",
                            street_no: 139,
                            postal_code: "M9W 1t2",
                            province: "Ontario",
                            country: "Canada"
                        }
                }
            })

    - Find the postal code of the person called Melanie Palace
    db.passengers.findOne(
        {
            name: "Melanie Palace"
        }).Address.postal_code
    - Change the postal_code of each passenger to M9V 5C2
        db.passengers.updateMany({}, 
            {        
                $set: {
                    "Address.postal_code" : "M9V 5C2"
                }            
            })
        - the embedded document has to be inside a quotation for this query to work
    - Find all passengers with country Canada
        - db.passengers.find(
            {"Address.country": "Canada"}
        ).pretty()

Assignment:
    Create a Patient collection and add 3 documents
```json
    [
        {
            firstName: "Max",
            lastName: "Schwartzmuller",
            age: 29,
            history: [
                {
                    disease: "flu",
                    treatment: "Tylanol",
                    doctor: "Afia Abdela"
                }
            ]
        },

        {
            firstName: "Mosh",
            lastName: "Hamedani",
            age: 33,
            history: [
                {
                    disease: "Giardia",
                    treatment: "Amoxsasilin",
                    doctor: "Meadin Abdela"
                },
                {
                    disease: "Cold",                    
                    doctor: "Meadin Abdela"
                }
            ]
        },
        {
            firstName: "James",
            lastName: "Petrucci",
            age: 37,
            history: [
                {
                    disease: "Pneumonia",
                    treatment: "Bacilixin",
                    doctor: "Nadia Adam"
                }
            ]
        }
    ]

```
Insert patients:
    db.patients.insertMany(...)
Update a patient witn a new name, age and new history entry
    db.patients.updateOne(
        {firstName: "Max"},
        {
            $set: {
                firstName: "Housam",
                lastName: "Harb"
            },
            $push: {
                history: {
                    disease: "Worms",
                    treatment: "Amox",
                    doctor: "Faaya Adam"                        
                }
            }
        }
    )
Find all patients older than 30 years of age
    db.patients.find(
        {
            age: {$gt: 30}
        }
    ).pretty()    
Delete all patients that have cold in their disease history
    db.patients.updateMany(
        {
            "history.disease": "Cold"            
        },
        {
            $set: { marker: "toDelete" }
        }
    )

    db.patients.deleteMany(
        {marker: "toDelete"}
    )