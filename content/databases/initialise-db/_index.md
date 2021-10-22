---
title: "Initialise Database"
date: 2021-10-22
---

Here we outline how we initialise our database `feather`

---

### Step 1. Install Dependencies

Our dependencies include

```txt
flask-sqlalchemy==2.3.2
mysql-connector==2.2.9
protobuf==3.19.0
```

### Step 2. Ensure the server is online

Open up MySQL Workbench. Navigate to Startup/Shutdown. Click Start server (if not already running). Add our username `host` password (check lastpass for current pw).

### Step 3. Create_db.py

We create a python script to create a database that we can use later.

the script appears as follow.

```py
import mysql.connector

mydb = mysql.connector.connect(
    host="localhost",
    user="root",
    passwd ="pickle",
)

my_cursor = mydb.cursor()

my_cursor.execute("CREATE DATABASE feather")

my_cursor.execute("SHOW DATABASES")
for db in my_cursor:
    print(db)
```

our output appears as a list of all databases:

```
('feather',)
('information_schema',)
('mysql',)
('performance_schema',)
('pickledb',)
('sakila',)
('sys',)
('world',)
```
We now have a database called feathers for our application's use.
