# Postgres

Postgres is a high-performance, open source database engine, and also the coolest database engine in existence. If you are using MySQL instead of Postgres you are literally a monster.

## psql cheat sheet

Use the `psql` command line tool to explore and manage your databases.

```bash
# connect to your personal database
psql

# connect to a specific database as current $USER
psql mydatabase

# connect to a specific database as a specific user with a password
psql mydatabase -U user -W

# use a specific host and port
psql mydatabase -h localhost -p 5432
```

Once in `psql`, you can use special commands starting with a backslash as well as standard SQL queries.

| Command | What it does |
| :--- | :--- |
| \l | list databases |
| \c db user | connect to the `db` database as `user` \(user is optional\) |
| \d | list relations \(tables, sequences, views, indices\) |
| \d table | describe `table` \(show columns, types, indices\) |
| \dt | list tables in the current database |
| \e | use your $EDITOR to write a long query |
| \timing | toggle display of query execution times |

## Securing Postgres

This is by no means a comprehensive guide, and it's probably going to grow over time.

### pg\_hba.conf

Using the `/etc/postgresql/[version]/[database]/pg_hba.conf` config file you can easily control which client can log in and to which database, as explained in the [documentation](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html). Most common authentication methods:

* `trust` - use in local development only, unconditionally allows all roles to connect
* `reject` - deny all access from the given IPs or for given user
* `scram-sha-256` - send a hashed password \(use this instead of the old `md5` method for password-based authentication
* `peer` - for **local** connections, use current user's username  

## Copy a database from one machine to another

1. Create a database dump on the source machine using the `-O` switch to disable outputting ownership data: `psql -U postgres -O <databasename> > dump.sql`
2. Create a new role and a new database on the target system: `psql -c "CREATE ROLE <username> WITH LOGIN; psql -c "CREATE DATABASE <databasename> WITH OWNER <username> ENCODING = 'UNICODE';`  
3. Import the data: `psql -U <username> -d <databasename> -f dump.sql`

## Copy to/from CSV

If you ever need to share some data, you can easily export a request to CSV straight into a file:

```text
\copy (select * from ...) to '/tmp/export.csv' with csv
```

Or maybe directly to STDOUT?

```text
\copy (select * from ...) to stdout with csv
```

Or maybe you want to copy **from** a CSV file into a table?

```text
\copy table from '~/input.csv' with delimiter ',' csv header
```







