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

