# MySQL, PhpMyAdmin and Python Tutorial 

## Install MySQL on cloud virtual machine with Docker

```shell script
sudo nohup docker network create mysql-network &
sudo nohup docker run --name=container_mysql -p8806:3306 -e MYSQL_ROOT_HOST='%' -e MYSQL_ROOT_PASSWORD=root -d --net mysql-network mysql/mysql-server &
sudo nohup docker run --name container_phpmyadmin --link container_mysql:db -p 8080:80 -d  -e PMA_HOST=container_mysql --net mysql-network phpmyadmin/phpmyadmin &
```

## Create a DB

In PhpMyAdmin dashboard, create a DB named ```justadatabase```.

## Create a table

In PhpMyAdmin dashboard, execute SQL query:

```mysql
create table if not exists final_exam (
  id varchar(50) not null,
  name varchar(50),
  score float,
  primary key (id)
) ENGINE=InnoDB DEFAULT CHARACTER SET=utf8;
```

## Add some rows to the table

In PhpMyAdmin dashboard, execute SQL query:

```mysql
insert into final_exam values ("12247777", "Gorge Bush", 85);
insert into final_exam values ("12248888", "Donald Trump", 88.5);
```

## Allow external access

In PhpMyAdmin dashboard, execute SQL query:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
```

## Connect to MySQL DB with Python

Attention, DO NOT install mysql-connector.

Instead, install **mysql-connector-python** with pip:
```shell script
pip install mysql-connector-python
```

```python
import mysql.connector
from mysql.connector import Error

try:
    connection = mysql.connector.connect(host='116.205.139.7',
                                         database='justadatabase',
                                         port=8806,
                                         user='root',
                                         password='root', 
                                         auth_plugin='mysql_native_password') # In case of error, remove the auth_plugin parameter
    if connection.is_connected():
        db_Info = connection.get_server_info()
        print("Connected to MySQL Server version ", db_Info)
        cursor = connection.cursor()
        cursor.execute("select database();")
        record = cursor.fetchone()
        print("You're connected to database: ", record)

except Error as e:
    print("Error while connecting to MySQL", e)
finally:
    if connection.is_connected():
        cursor.close()
        connection.close()
        print("MySQL connection is closed")
```

## Let's Check what's in the DB

```python
connection = mysql.connector.connect(host='116.205.139.7',
                                         database='justadatabase',
                                         port=8806,
                                         user='root',
                                         password='root', 
                                         auth_plugin='mysql_native_password') # In case of error, remove the auth_plugin parameter
if connection.is_connected():
    db_Info = connection.get_server_info()
    print("Connected to MySQL Server version ", db_Info)
    cursor = connection.cursor()
    cursor.execute("select database();")
    record = cursor.fetchone()
    print("You're connected to database: ", record)
```

```python
def get_all_rows():
    cursor.execute("SELECT * FROM final_exam")
    result = cursor.fetchall()
    for x in result:
      print(x)

get_all_rows()
```

## Let's add a row into the DB

```python
cursor.execute('INSERT INTO final_exam values ("12249999", "Joe Biden", 89)')
get_all_rows()
```

## Close the connection to DB
```python
def disconnect():
    if connection.is_connected():
        cursor.close()
        connection.close()
        print("MySQL connection is closed")
disconnect()
```