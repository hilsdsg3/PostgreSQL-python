<h1 align="center"> PostgreSQL with cmd line and python  </h1> 

<br>

## Table of Contents

- [Helpful cmd line commands.](#helpful_cmd_line_commands)
- [Interacting with PostgreSQL dB in Python.](#interacting_postgres)
- [Credits](#credits)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<br>

## Goal
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; To show how to easily interact with Win10 postgreSQL cmd line and Python using PostgreSQL database adapter package "psycopg2".

<a name='helpful_cmd_line_commands'></a>
## Helpful Win10 command (cmd) line commands
<br>

- ### To start the windows10+ command file go to search menu and type "cmd"**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If you are allowed to, select "Run as administrator" 
<br><br>
<p align="left"><img width=85% src="C:\Users\champ\Python_proj\postgreSQL_repo\data\cmd_line_start.png"></p>

<br>

- ### To start postgreSQL server<br>
-d : dBase name <br>
-W : Password prompt - even if there is no password <br>
-U : username <br>
Learning : an example dB
postgres : user
[There are more -options at this link](https://www.postgresql.org/docs/11/app-psql.html)
<br>
```
psql -d Learning -U postgres -W
```
output :
<p align="left"><img width=85% src="C:\Users\champ\Python_proj\postgreSQL_repo\data\start_postgres.png"></p>


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
<br>
**See all a list of databases** -
<br>
```
\l
```
output :
<p align="left"><img width=85% src="C:\Users\champ\Python_proj\postgreSQL_repo\data\list_of_databases.png"></p>

<a name='interacting_postgres'></a>
## Interacting with PostgreSQL dB in Python

### Install in your local python environment "psycopg2" package - **<br>
Psycopg is a PostgreSQL database package that is interactive shell
with PostgreSQL. It allows one to create, read, update, and delete PostgreSQL databases and tables. <br>[More about the installation here.](https://pypi.org/project/psycopg2/)
One of the most frequent operations in interacting with a database is CRUD which stands for create, read, update, and delete a database, table, or content data. I will go over each of these simple steps.

- ### Create a database<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Once you have psycopg2 installed, import the package in python. Next connect to the postgres server in python. Please read the documentation of establishing a connection.<br><br>
- database = 'postgres'  # database server name
- user = 'postgres'
- password = 'password' # this value you may change later
- host = '127.0.0.1' # local host computer's IP address
- port = '5432' # PostgreSQL's default port

```
# import the package
import psycopg2

# set the variables
DATABASE_INITIAL_ = 'postgres'
USER_ = 'postgres'
PASSWORD_ = 'password'
HOST_ = '127.0.0.1'
PORT_ = '5432'
DATABASE_ = 'financial' # sample database that is created 

#establishing the connection
conn = psycopg2.connect(
   database = DATABASE_INITIAL_,
   user = USER_,
   password = PASSWORD_,
   host = HOST_,
   port = PORT_
)

# Setting the connection to TRUE allows cleaner
# commit statements able to be read by postgreSQL 
# conn.autocommit = True. If there is no autocommit,
# there are certain complex statements that don't
# execute cleanly.
conn.autocommit = True

# Creating a cursor object in order to execute statements
cursor = conn.cursor()

# Preparing query to create a database
sql = '''CREATE database '''+DATABASE_;

# Creating a database
cursor.execute(sql)
print(f'\n{DATABASE_} database created successfully........')
print(f'\nCREATE database commit comment :\n"{sql}"\n')

# Closing the connection - always close the database when not interacting 
conn.close()
print(f'\n{DATABASE_} database now closed successfully........')
```
output in python and cmd line :
```
financial database created successfully........
CREATE database commit comment :
"CREATE database financial"
financial database now closed successfully........
```
<p align="left"><img width=85% src="C:\Users\champ\Python_proj\postgreSQL_repo\data\CREATE_dB.png"></p>
</details>

- ### Create a table<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Once you have created the database and connected, CREATE a table. The table create statement looks complex, so I output a copy of the SQL statement below. This statement could be used as a simplification.
<br>
TABLE_ = 'realized_profit_loss_tranactions'<br>
DATABASE_ = 'financial' # sample database that is created 

```
#establishing the connection
conn = psycopg2.connect(
   database=DATABASE_,
   user = USER_,
   password = PASSWORD_,
   host = HOST_,
   port = PORT_
)

conn.autocommit = True

# #Creating a cursor object using the cursor() method
cursor = conn.cursor()

# table sample column organization
columns = {1:'Closed_Date',
           2:'Symbol',
           3:'Name',
           4:'Quantity',
           5:'Proceeds',
           6:'CostBasis',
           7:'Personal',
           8:'Total_Gain_Loss_pc',
           9:'Long_Term_Gain_Loss_amt',
           10:'Long_Term_Gain_Loss_pc',
           11:'Short_Term_Gain_Loss_amt',
           12:'Short_Term_Gain_Loss_pc',
           13:'Wash_Sale',
           14:'account'}

# table datatype setup
datatype = {'FT' : 'FLOAT',
            'DT' : 'DATE',
            'INT' : 'INTEGER',
            'CHAR_S' : 'CHAR(20)',
            'CHAR_L' : 'CHAR(40)'}

# table space_nulls setup
spaces_nulls = {'NLL' : 'NULL',
                'NNULL' : 'NOT NULL',
                'sp' : ''' ''',
                'cma' : ''',''',
                'nl' : '''\n'''}


#Creating table as per requirement
sql ='''CREATE TABLE '''+TABLE_+'''\n('''\
    +columns[1]+spaces_nulls['sp']+datatype['DT']+spaces_nulls['sp']+spaces_nulls['NNULL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[2]+spaces_nulls['sp']+datatype['CHAR_S']+spaces_nulls['sp']+spaces_nulls['NNULL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[3]+spaces_nulls['sp']+datatype['CHAR_L']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[4]+spaces_nulls['sp']+datatype['INT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[5]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[6]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[7]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[8]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[9]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[10]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[11]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[12]+spaces_nulls['sp']+datatype['FT']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[13]+spaces_nulls['sp']+datatype['CHAR_S']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[14]+spaces_nulls['sp']+datatype['CHAR_S']+spaces_nulls['sp']+spaces_nulls['NLL']+spaces_nulls['nl']\
+''')'''

cursor.execute(sql)

print(f'\nTable created successfully........')
print(f'\nTable CREATE sql commit comment :\n"{sql}"\n')


arr = ['column_name','data_type', 'is_nullable']
sql = """                              
SELECT """+arr[0]+""", """+arr[1]+""", """+arr[2]+"""
FROM information_schema.columns
WHERE table_name = '"""+TABLE_+"""'""";

cursor.execute(sql)
print(f'\n READ database commit comment :\n"{sql}"\n')

data = cursor.fetchall()

panda_df = pd.DataFrame(data = data,  
                        columns = arr)

print(f'\nPandas schema READ :\n"{panda_df}"\n')

# #Closing the connection
conn.close()
print(f'\n{DATABASE_} database now closed successfully........')
```
output in python :
```
Table created successfully........

Table CREATE sql commit comment :
"CREATE TABLE realized_profit_loss_tranactions
(Closed_Date DATE NOT NULL,
Symbol CHAR(20) NOT NULL,
Name CHAR(40) NULL,
Quantity INTEGER NULL,
Proceeds FLOAT NULL,
CostBasis FLOAT NULL,
Personal FLOAT NULL,
Total_Gain_Loss_pc FLOAT NULL,
Long_Term_Gain_Loss_amt FLOAT NULL,
Long_Term_Gain_Loss_pc FLOAT NULL,
Short_Term_Gain_Loss_amt FLOAT NULL,
Short_Term_Gain_Loss_pc FLOAT NULL,
Wash_Sale CHAR(20) NULL,
account CHAR(20) NULL
)"


 READ database commit comment :
"                              
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'realized_profit_loss_tranactions'"


Table schema READ sql commit comment :
"                 column_name         data_type is_nullable
0                closed_date              date          NO
1                     symbol         character          NO
2                       name         character         YES
3                   quantity           integer         YES
4                   proceeds  double precision         YES
5                  costbasis  double precision         YES
6                   personal  double precision         YES
7         total_gain_loss_pc  double precision         YES
8    long_term_gain_loss_amt  double precision         YES
9     long_term_gain_loss_pc  double precision         YES
10  short_term_gain_loss_amt  double precision         YES
11   short_term_gain_loss_pc  double precision         YES
12                 wash_sale         character         YES
13                   account         character         YES"


financial database now closed successfully........
```
cmd line output :
```
financial=# psql -d financial -U postgres -W
financial-# \d
                      List of relations
 Schema |               Name               | Type  |  Owner
--------+----------------------------------+-------+----------
 public | realized_profit_loss_tranactions | table | postgres
(1 row)

financial=# SELECT column_name, data_type, is_nullable
financial-# FROM information_schema.columns
financial-# WHERE table_name = 'realized_profit_loss_tranactions';

       column_name        |    data_type     | is_nullable
--------------------------+------------------+-------------
 closed_date              | date             | NO
 symbol                   | character        | NO
 name                     | character        | YES
 quantity                 | integer          | YES
 proceeds                 | double precision | YES
 costbasis                | double precision | YES
 personal                 | double precision | YES
 total_gain_loss_pc       | double precision | YES
 long_term_gain_loss_amt  | double precision | YES
 long_term_gain_loss_pc   | double precision | YES
 short_term_gain_loss_amt | double precision | YES
 short_term_gain_loss_pc  | double precision | YES
 wash_sale                | character        | YES
 account                  | character        | YES
(14 rows)

```
<br>

- ### Update the table<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; When the table schema is created, then update with values as the following. The python viewing is in pandas df because of the simplicity in display. This would not be feasible with reading 1 million rows for the database. When the table schema is as many columns as this sample. The cmd line output does not display well. It is better to view it in python with pandas in python not the following output.

```
import pandas as pd

#establishing the connection
conn = psycopg2.connect(
   database=DATABASE_,
   user = USER_,
   password = PASSWORD_,
   host = HOST_,
   port = PORT_
)

conn.autocommit = True

# #Creating a cursor object using the cursor() method
cursor = conn.cursor()

# Preparing SQL queries to INSERT a record into the database.
sql = '''INSERT INTO '''+TABLE_+'''\n('''\
    +columns[1]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[2]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[3]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[4]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[5]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[6]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[7]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[8]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[9]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[10]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[11]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[12]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[13]+spaces_nulls['cma']+spaces_nulls['nl']\
    +columns[14]+spaces_nulls['nl']\
   +''') VALUES (
   '2017-06-15',
   'TLT',
   'ISHARES',
   132,
   15847.18,
   16023.15,
   -43.99,
   -0.27,
   0,
   0,
   -43.99,
   -0.27,
   'Yes',
   'Personal'
   )'''
cursor.execute(sql)

print(f'\nData inserted successfully........')
print(f'Table INSERT sql commit comment :\n"{sql}"/n')

sql = "SELECT * FROM "+TABLE_
cursor.execute(sql)

# Formatting the dataframe
rows = cursor.fetchall()
arr = np.array(rows).flatten()
df = pd.DataFrame(np.zeros((1,len(columns))), columns=columns.values())
for i,nr in enumerate(columns):
    df.loc[0,columns[nr]] = arr[i]
df['Closed_Date'] = pd.to_datetime(df['Closed_Date'])

print(f'\nData fetched successfully........')
print(f'Table READ sql commit comment :\n"{sql}"\n')
print(f'\nPandas schema READ :')
df = display(df)

# Closing the connection
conn.close()
print(f'\n{DATABASE_} database now closed successfully........')
```
python output :
```
Data inserted successfully........
Table INSERT sql commit comment :
"INSERT INTO realized_profit_loss_tranactions
(Closed_Date,
Symbol,
Name,
Quantity,
Proceeds,
CostBasis,
Personal,
Total_Gain_Loss_pc,
Long_Term_Gain_Loss_amt,
Long_Term_Gain_Loss_pc,
Short_Term_Gain_Loss_amt,
Short_Term_Gain_Loss_pc,
Wash_Sale,
account
) VALUES (
   '2017-06-15',
   'TLT',
   'ISHARES',
   132,
   15847.18,
   16023.15,
   -43.99,
   -0.27,
   0,
   0,
   -43.99,
   -0.27,
   'Yes',
   'Personal'
   )"/n

Data fetched successfully........
Table READ sql commit comment :
"SELECT * FROM realized_profit_loss_tranactions"

Pandas schema READ :
Closed_Date	Symbol	Name	Quantity	Proceeds	CostBasis	Personal	Total_Gain_Loss_pc	Long_Term_Gain_Loss_amt	Long_Term_Gain_Loss_pc	Short_Term_Gain_Loss_amt	Short_Term_Gain_Loss_pc	Wash_Sale	account
0	2017-06-15	TLT	ISHARES	132.0	15847.18	16023.15	-43.99	-0.27	0.0	0.0	-43.99	-0.27	Yes	Personal

financial database now closed successfully........
```
output in cmd line :
```
financial=# select * FROM realized_profit_loss_tranactions;
 closed_date | symbol |    name    | quantity | proceeds | costbasis | personal | total_gain_loss_pc | long_term_gain_loss_amt | long_term_gain_loss_pc | short_term_gain_loss_amt | short_term_gain_loss_pc | wash_sale |  account
-------------+--------+------------+----------+----------+-----------+----------+--------------------+-------------------------+------------------------+--------------------------+-------------------------+-----------+------------
 2017-06-15  | TLT    | ISHARES    |      132 | 15847.18 |  16023.15 |   -43.99 |              -0.27 |                       0 |                      0 |                   -43.99 |                   -0.27 | Yes       | Personal
(1 row)
```
<br>

- ### Delete/Drop a table<br>

```
#establishing the connection
conn = psycopg2.connect(
    database = DATABASE_,
    user = USER_,
    password = PASSWORD_,
    host = HOST_,
    port = PORT_
)
conn.autocommit = True

#Creating a cursor object using the cursor() method
cursor = conn.cursor()

#Preparing query to create a database
sql = '''DROP TABLE '''+TABLE_;
cursor.execute(sql)
print(f"\n{TABLE_} dropped successfully........")

#Closing the connection
conn.close()
print(f'\n{DATABASE_} database now closed successfully........')

```
output in python :
```
realized_profit_loss_tranactions dropped successfully........

financial database now closed successfully........
```



</details>
</details>

<br>
<br>

## Credits

- PostgreSQL: Documentation. (2000). Retrieved from https://www.postgresql.org/docs/

- psycopg2. (2020, September 7). Retrieved from https://pypi.org/project/psycopg2/
