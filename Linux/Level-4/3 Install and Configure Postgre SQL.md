### Task
The Nautilus application development team has shared that they are planning to deploy one newly developed application on Nautilus infra in Stratos DC. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:


PostgreSQL database server is already installed on the Nautilus database server.

a. Create a database user kodekloud_gem and set its password to 8FmzjvFU6S.

b. Create a database kodekloud_db1 and grant full permissions to user kodekloud_gem on this database.

Note: Please do not try to restart PostgreSQL server service.

### Solution
1. Postgres is up and running
```
[peter@stdb01 ~]$ sudo systemctl list-units --type=service -all | grep postgre
  postgresql.service                     loaded    active   running PostgreSQL database server

[peter@stdb01 ~]$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL database server
     Loaded: loaded (/usr/lib/systemd/system/postgresql.service; disabled; preset: disabled)
     Active: active (running) since Thu 2024-08-08 20:30:24 UTC; 4min 31s ago
    Process: 2632 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
   Main PID: 2654 (postmaster)
      Tasks: 8 (limit: 1340692)
     Memory: 19.6M
     CGroup: /docker/48be0fc0436ac178c81e19266474bd5d50472fc005a6cbad706e872530175419/system.slice/postgresql.service
```
2. Let's do the corresponding actions. [Docs](https://www.postgresql.org/docs/13/index.html)
```
[root@stdb01 peter]# find / -type d -name *pg*
/var/lib/pgsql
/var/lib/pgsql/data/pg_multixact
/var/lib/pgsql/data/pg_xact
/var/lib/pgsql/data/pg_snapshots
/var/lib/pgsql/data/pg_tblspc
/var/lib/pgsql/data/pg_commit_ts
/var/lib/pgsql/data/pg_stat
/var/lib/pgsql/data/pg_subtrans
/var/lib/pgsql/data/pg_wal
/var/lib/pgsql/data/pg_logical
/var/lib/pgsql/data/pg_serial
/var/lib/pgsql/data/pg_twophase
/var/lib/pgsql/data/pg_notify
/var/lib/pgsql/data/pg_dynshmem
/var/lib/pgsql/data/pg_replslot
/var/lib/pgsql/data/pg_stat_tmp
...

[root@stdb01 peter]# cd /var/lib/pgsql/data

# Postgres is configured to the above directory

[root@stdb01 data]# su postgres
bash-5.1$ pg_ctl status -D .
pg_ctl: server is running (PID: 2654)
/usr/bin/postgres "-D" "/var/lib/pgsql/data"

bash-5.1$ createuser --pwprompt kodekloud_gem
Enter password for new role: 
Enter it again:

bash-5.1$ createdb -O kodekloud_gem kodekloud_db1
```
- A second option would be to use SQL directly
```
bash-5.1$ psql
psql (13.14)
Type "help" for help.

postgres=#

# User creation query
# DB creation
# ...
```
