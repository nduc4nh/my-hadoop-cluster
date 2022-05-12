# POSTGRES

## INSTALL POSTGRES

* Centos - refer to a link yourself :)

## INIT

when the installation is finished, the postgres creates an user for the system named 'postgres' (because postgres does not allow root user to operate on the db)

```
> su - postgres
$ /usr/pgsql-xx/bin/postgresql-xx-setup initdb
$ exit

> systemctl start postgresql-xx.service
```

The DB sb is now running in the port of 5432

## BACKING UP DATA IN POSTGRES FOR MIGRATION

```
> su - postgres
$ pg_dumball > /backup
exit

# Copy the following configuration files if needed for database accesses in the same network and other configuration
> mv /var/lib/pgsql/data /data.old
```

Now you have a backup data of postgresDB, whenever you want to change version, switch sever, follow this instruction to backup data to use them in the new DB

```
# After finishing database installation

> cp /data.old/../pg_hba.conf /var/lib/pgsql/xx/data
> cp /data.old/../postgresql.conf /var/lib/pgsql/xx/data
> su - postgres
$ psql -d postgres -f /path/to/backup
..

```
