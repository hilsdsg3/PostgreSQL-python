<h1 align="center"> PostgreSQL with cmd line and python psycopg2 </h1> 

<br>

## Table of Contents

- [Common Win10 PostgreSQL cmd line commands.](#helpful_cmd_line_commands)
- [Interacting with PostgreSQL dB in Python.](#interacting_postgres)
- [Improvements.](#improvements)
- [Credits](#credits)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


<br>

## Goals
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1. To show how to easily interact with Win10 postgreSQL cmd line and Python using PostgreSQL database adapter package "psycopg2". [See python notebook.](https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/postgres_database.ipynb)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2. My python notebook builds an entity relationship (ER) diagram simplified in the following tutorial.   

<p align="left"><img width=85% src="https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/ER_diagram.png"></p>

<a name='helpful_cmd_line_commands'></a>
## Common Win10 PostgreSQL cmd line commands
<br>

- ### To start the windows10+ command file go to search menu and type "cmd"**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If you are allowed to, select "Run as administrator" 
<br><br>
<p align="left"><img width=85% src="https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/cmd_line_start.png"></p>

<br>

- ### To open the connection to a postgreSQL server<br>
d : dBase name <br>
W : Password prompt - even if there is no password <br>
U : username <br>
Learning : an example dB<br>
postgres : user<br>
[There are more -options at this link](https://www.postgresql.org/docs/11/app-psql.html)
<br>
```
psql -d Learning -U postgres -W
```
output :
<p align="left"><img width=100% src="https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/start_postgres.png"></p>


<br>

- ### A COLON (;) commits any message to the database
```
DROP DATABASE TARGET_dB;
CREATE DATABASE TARGET_db;
```
<br>

- ### Drop existing connections but yours
For PostgreSQL 9.2 and above use the following
```
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'TARGET_dB' -- ← change this to your DB
  AND pid <> pg_backend_pid();
```
<br>

- ### Drop a database<br>
If you don't see a confirmation message,
the command didn't commit (execute) to the dB    
```
DROP DATABASE TARGET_dB;
```
output (confirmation message) :
```
DROP DATABASE
```
<br>

- ### See all a list of databases

```
\l
```
output :
<p align="left"><img width=100% src="https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/list_of_databases.png"></p>

<a name='interacting_postgres'></a>
## Interacting with PostgreSQL dB in Python

- ### Prerequisite - Install in your local python environment the "psycopg2" package<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Psycopg is a PostgreSQL database package that is interactive shell
with PostgreSQL. It allows one to create, read, update, and delete PostgreSQL databases and tables. <br>[More about the installation here.](https://pypi.org/project/psycopg2/)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;One of the most frequent operations in interacting with a database is CRUD which stands for create, read, update, and delete a database, table, or content data. I will go over each of these simple steps.

- ### Preparation of the database driver<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Once you have psycopg2 installed, import the package in python. Next connect to the postgres server in python. Please read the documentation of establishing a connection.<br><br>
database = 'postgres'  # database server name
user = 'postgres'
password = 'password' # this value you may change later
host = '127.0.0.1' # local host computer's IP address
port = '5432' # PostgreSQL's default port

```
# import the package
import psycopg2 # postgreSQL's database driver
import numpy as np
import pandas as pd
from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT

# set the variables
DATABASE_INITIAL_ = 'postgres'
DATABASE_ = 'financial'
USER_ = 'postgres'
PASSWORD_ = 'password'
HOST_ = '127.0.0.1'
PORT_ = '5432'

# python functions (title) - Please refer to the python notebook
def establish_connection(DATABASE_, USER_, PASSWORD_, HOST_, PORT_):
def detect_table_exists(cursor, TABLE_):
def create_database(DATABASE_INITIAL_, DATABASE_, USER_, PASSWORD_, HOST_, PORT_):
def create_table(DATABASE_, USER_, PASSWORD_, HOST_, PORT_, sql_script, TABLE_):
def update_table(DATABASE_, USER_, PASSWORD_, HOST_, PORT_, sql_script, TABLE_):
def drop_table(DATABASE_, USER_, PASSWORD_, HOST_, PORT_, sql_script, TABLE_):


# Setting the connection to TRUE allows cleaner
# commit statements able to be read by postgreSQL 
# conn.autocommit = True. If there is no autocommit,
# there are certain complex statements that don't
# execute cleanly.
conn.autocommit = True

# Creating a cursor object in order to execute statements
cursor = conn.cursor()

```
- ### Create a database <br>
```
# Create a database by using the following function
create_database(DATABASE_INITIAL_, DATABASE_, USER_, PASSWORD_, HOST_, PORT_)
```
output of python
```
financial database created successfully........
CREATE database commit comment :
"CREATE database financial"
financial database now closed successfully........
```
output if the database exists :
```
The dB of the name financial exists already
```
<p align="left"><img width=100% src="https://github.com/hilsdsg3/PostgreSQL-python/blob/master/data/CREATE_dB.png"></p>
</details>

- ### Create a table <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Once you have created the database and connected, CREATE a table. The table create statement looks complex, so I output a copy of the SQL statement below. The following statement is simplified.
<br>
TABLE_ = 'exchange'<br>
DATABASE_ = 'financial'<br>

```
# create exchange table
TABLE_ = 'exchange'
sql = '''CREATE TABLE IF NOT EXISTS ''' + TABLE_ + ''' (
  id SERIAL PRIMARY KEY,
  name VARCHAR(10) NOT NULL,
  currency CHAR(3) NULL DEFAULT NULL,
  created_date TIMESTAMPTZ NULL DEFAULT TRANSACTION_TIMESTAMP(),
  last_updated  TIMESTAMPTZ NULL DEFAULT TRANSACTION_TIMESTAMP())'''

create_database(DATABASE_INITIAL_, DATABASE_, USER_, PASSWORD_, HOST_, PORT_)
```

output in python :
```
TABLE : "exchange" DOES NOT EXIST

CREATING TABLE : exchange ....

Table created successfully........

CREATE sql commit comment :
CREATE TABLE IF NOT EXISTS exchange (
  id SERIAL PRIMARY KEY,
  name VARCHAR(10) NOT NULL,
  currency CHAR(3) NULL DEFAULT NULL,
  created_date TIMESTAMPTZ NULL DEFAULT TRANSACTION_TIMESTAMP(),
  last_updated  TIMESTAMPTZ NULL DEFAULT TRANSACTION_TIMESTAMP())
READ sql commit comment :                              
    SELECT column_name, data_type, is_nullable
    FROM information_schema.columns
    WHERE table_name = 'exchange';

Pandas schema READ :
"    column_name                 data_type is_nullable
0            id                   integer          NO
1          name         character varying          NO
2      currency                 character         YES
3  created_date  timestamp with time zone         YES
4  last_updated  timestamp with time zone         YES"

financial database now closed successfully.
```

<br>

- ### Update the table<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; When the table schema is created, then update with values as the following. The python viewing is in pandas df because of the simplicity in display. This would not be feasible with reading 1 million rows for the database so a UPDATE LIMIT statement would be needed.<br>
```
# update exchange table
TABLE_ = 'exchange'
sql = '''INSERT INTO ''' + TABLE_ + ''' (
  name, currency, created_date, last_updated)
  VALUES ('NASDAQ', 'USD', '2000-01-01', '2001-02-01');'''
create_table(DATABASE_,
                 USER_,
                 PASSWORD_,
                 HOST_,
                 PORT_,
                 sql,
                 TABLE_)
```
output in python :

```
TABLE : "exchange" EXISTS

Data inserted successfully........
Table INSERT sql commit comment :
"INSERT INTO exchange (
  name, currency, created_date, last_updated)
  VALUES ('NASDAQ', 'USD', '2000-01-01', '2001-02-01');"


Data fetched successfully........
Table READ LIMIT 5 sql commit comment :
"SELECT * FROM exchange"

Pandas display :
id	name	currency	created_date	last_updated
0	1	NASDAQ	USD	2000-01-01 00:00:00-08:00	2001-02-01 00:00:00-08:00
```


- ### Delete/Drop a table<br>

```
# drop table exchange
drop_table(DATABASE_, USER_, PASSWORD_, HOST_, PORT_, sql_script, TABLE_):

```
output in python :
```
financial database now opened successfully........

TABLE : "exchange" EXISTS
exchange DROP sql commit comment :
"DROP TABLE IF EXISTS exchange;"
exchange dropped successfully.
financial database now closed successfully.

```
<br>

<a name='improvements'></a>
## Improvements<br>
- create try and fail statements


</details>
</details>

<br>

<a name='credits'></a>
## Credits

- PostgreSQL: Documentation. (2000). Retrieved from https://www.postgresql.org/docs/

- psycopg2. (2020, September 7). Retrieved from https://pypi.org/project/psycopg2/
