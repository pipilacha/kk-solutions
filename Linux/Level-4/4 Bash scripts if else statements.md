### Task
The Nautilus DevOps team is working on to develop a bash script to automate some tasks. As per the requirements shared with the team database related tasks needed to be automated. Below you can find more details about the same:


    Write a bash script named /opt/scripts/database.sh on Database Server. The mariadb database server is already installed on this server.

    Add code in the script to perform some database related operations as per conditions given below:

    a. Create a new database named kodekloud_db01. If this database already exists on the server then script should print a message Database already exists and if the database does not exist then create the same and script should print Database kodekloud_db01 has been created. Further, create a user named kodekloud_roy and set its password to asdfgdsd, also give full access to this user on newly created database (remember to use wildcard host while creating the user).

    b. Now check if the database (if it was already there) already contains some data (tables)if so then script should print 'database is not empty otherwise import the database dump /opt/db_backups/db.sql and print imported database dump into kodekloud_db01 database.

    c. Take a mysql dump which should be named as kodekloud_db01.sql and save it under /opt/db_backups/ directory.
### Solution
```
#!/bin/bash

db_name="kodekloud_db01"
db_user="kodekloud_roy"
db_password="asdfgdsd"
import_loc="/opt/db_backups/db.sql"
backup_name="kodekloud_db01.sql"
backup_to="/opt/db_backups/$backup_name"
new_db="false"

# Create DB if not exists
mysql -u root <<< "USE $db_name ;" > /dev/null 2>&1
if [[  $? = 0 ]]
then
        echo "Database already exists"
else
        mysql -u root <<< "CREATE DATABASE IF NOT EXISTS $db_name ;"
        echo "Database $db_name has been created"
        new_db="true"
fi


# Create user and grant for db
mysql -u root <<< "CREATE USER IF NOT EXISTS '$db_user'@'*' IDENTIFIED BY '$db_password' ;"
if [[ $? != 0 ]] ; then echo "Error creating user" ; fi

mysql -u root <<< "GRANT ALL ON $db_name.* TO '$db_user'@'*' ;"
if [[ $? != 0 ]] ; then echo "Error granting user privileges" ; fi


# Import dump if empty db
tables=$(mysql -u root -t <<< "SHOW TABLES FROM $db_name ;")
if [[ $new_db = 'new' || -z $tables ]]
then
        mysql -u root --database=$db_name < $import_loc > /dev/null
        if [[ $? != 0 ]]
        then
                echo 'Error importing backup'
        else
                echo 'imported database dump into kodekloud_db01 database'
        fi
else
        echo 'database is not empty'
fi


# Backup db
mysqldump -u root $db_name > $backup_to
if [[ $? != 0 ]] ; then echo "Error creating backup" ; fi
```
