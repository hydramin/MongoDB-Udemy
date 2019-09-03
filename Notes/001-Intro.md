Nomenclature: it comes from the word Humongous
- It uses collections instead of tables
- It stores data in a json format
- It is flexible interms of the data which is in a key-value pair
- It is schemaless
- BSON: the json data is stored as a binary data in the background.
- We dont need to do complex joins to fetch data.

MongoDB Ecosystem
- the company is called MongoDB
- The MongoDB Database
    - Community version and Enterprise
    - Cloud Solution(Atlas)
    - Mobile solution: like sqlite
    - GUI for data access: Compass
    - BI Connectors, MongoDB Charts for Data science
- Stitch: is a serverless datastorage
    - provides a servelrless query API 
    - helps query from the client side
    - Serverless Functions similar to AWS lambda, 
    - Database Triggers
    - Real-Time Sync to sync data from devices.

Getting Started
- Install mongodb locally
- Get Mongodb > Community Server > install or extract
- on linux create a .../data/db and configure the mongoDB to store any data into this folder.
- need to modify the .bash_profile file and add the location of the mongo shell in the extracted mongoDB binaries
- we need to run mongod file.
- mongo is the shell client and mongod is the database.
- mongod --dbpath "location of the data/db"

The Mongo Shell
- show dbs
- use shop
- db.products.insertOne({name: "Laptop", price: 1999.99})
- db.products.find()
- db.products.find().pretty()
- Alt+arrow keys, skip throuth characters in a line

Mongo Shell vs Drivers
- Find mongo drivers for any language.

Working With MongoDB
- There is a Frontend and Backendlayer and the Datastorage
- in the backend there are drivers that access the mongodb server(mongod)
- the mongodb server works with a "storage engine" the engine stores it.
- The shell can also be used to administair the data base
- The storage engine reads and writes to mem or disk to optimize data retrival and storage

Course Outline
    - Intro
    - Basic CRUD ops
    - Data schema and Relation between documents
    - Working with the shell
    - MongoDB Compass
    - Advanced CRUD
        - Read, Update, Delete
    - Indexes
    - GeoSpatial Data
    - Aggregation Framework: faster data retrival
    - Numeric Data
    - Security and Authentication
    - Performance, Fault Tolerance and Deployment on the cloud
    - Transactions (4.0)
    - Shell to Drivers (Node + React)
    - Mongo Stitch
