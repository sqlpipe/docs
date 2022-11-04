# CLI Reference

This page lists SQLpipe's commands, their options/flags, and gives usage examples.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe.

## Transfer command

This command runs a transfer directly with the CLI without needing to set up a server.

### Command flag options

```
REQUIRED    --source-ds-type string      Source type. Must be one of [postgresql, mysql, mssql, oracle, redshift, snowflake]
            --source-hostname string     Source system's hostname
            --source-port int            Source system's port
            --source-account-id string   Source system's account ID (Snowflake only)
REQUIRED    --source-username string     Source username
REQUIRED    --source-password string     Source password
REQUIRED    --source-db-name string      Source system's DB name
REQUIRED    --query string               Query to run on source system
REQUIRED    --target-ds-type string      Target type. Must be one of [postgresql, mysql, mssql, oracle, redshift, snowflake]
            --target-hostname string     Target system's hostname
            --target-port int            Target system's port
            --target-account-id string   Target system's account ID (Snowflake only)
REQUIRED    --target-username string     Target username
REQUIRED    --target-password string     Target password
REQUIRED    --target-db-name string      Target system's DB name
            --target-schema string       Schema to write query results to
REQUIRED    --target-table string        Table to write query results to
            --overwrite                  Overwrite target table
            --analytics                  Send anonymized usage data to SQLpipe for product improvements (default true)
```

### Usage notes

* Hostname and port are required for all data system types except Snowflake, which requires a [Snowflake account id](https://docs.snowflake.com/en/user-guide/admin-account-identifier.html#where-are-account-identifiers-used).
* When specifying a source DB's schema, put the schema name in the query. When specifying a target DB's schema name, pass it via command line flag.

### Transfer Examples

#### **PostgreSQL to Snowflake transfer**

Below is a command you could use to make a transfer from a PostgreSQL DB to a Snowflake DB.

PostgreSQL and Snowflake both have robust schema support. Notice how PostgreSQL's schema (`public`) is in the query, while Snowflake's (also `public`) is passed via flag.

```
sqlpipe transfer \
  --source-ds-type "postgresql" \
  --source-hostname "my-postgresql-hostname.com" \
  --source-port 5432 \
  --source-username "my-postgresql-username" \
  --source-password "Don7H4ckM3" \
  --source-db-name "my_postgresql_db_name" \
  --query "select * from public.my_table" \
  --target-ds-type "snowflake" \
  --target-account-id "abc12345.us-east-1" \
  --target-username "my_snowflake_username"
  --target-password "Sup3rS3cr3t" \
  --target-db-name "my_snowflake_db_name" \
  --target-schema "public" \
  --target-table "table_to_insert_into" \
  --overwrite
```

#### **A PostgreSQL to MySQL transfer**

Below is a command you could use to make a transfer from a PostgreSQL DB to a MySQL DB.

A few things to notice about this command:

* MySQL doesn't really have support for schemas, so we do not pass a schema name flag.
* Like PostgreSQL, MySQL requires a hostname and port, as opposed to an account ID, which is Snowflake only.

```
sqlpipe transfer \
  --source-ds-type "postgresql" \
  --source-hostname "my-postgresql-hostname.com" \
  --source-port 5432 \
  --source-username "my-postgresql-username" \
  --source-password "Don7H4ckM3" \
  --source-db-name "my_postgresql_db_name" \
  --query "select * from public.my_table" \
  --target-ds-type "mysql" \
  --target-hostname "my-mysql-hostname.com" \
  --target-port 3306 \
  --target-username "my_mysql_username"
  --target-password "Sup3rS3cr3t" \
  --target-db-name "my_mysql_db_name" \
  --target-table "table_to_insert_into" \
  --overwrite
```

***

## Query command

This command sends a query to a database. It does not return any output, unless there is an error.

### Command flag options

```
REQUIRED    --connection-ds-type string      Connection type. Must be one of [postgresql, mysql, mssql, oracle, redshift, snowflake]
            --connection-hostname string     Connection's hostname
            --connection-port int            Connection's port
            --connection-account-id string   Connection's account ID (Snowflake only)
REQUIRED    --connection-username string     Connection username
REQUIRED    --connection-password string     Connection password
REQUIRED    --connection-db-name string      Connection's DB name
REQUIRED    --query string                   Query to run
```

### Query Example

Below is a command you could use to run a query on a PostgreSQL DB.

```
sqlpipe query \
  --connection-ds-type "postgresql" \
  --connection-hostname "my-postgresql-hostname.com" \
  --connection-port 5432 \
  --connection-username "my-postgresql-username" \
  --connection-password "Don7H4ckM3" \
  --connection-db-name "my_postgresql_db_name" \
  --query "create table person (id int, name string)"
```

***

## Initialize command

This command takes a fresh database, and initializes it to be used by a SQLpipe server.

### Command flag options

```
REQUIRED    --dsn string   Database backend connection string
            --force        Do not ask for confirmation
```

### Initialize Example

```
sqlpipe initialize --dsn=postgres://postgres:my-pg-password@localhost/sqlpipe --force
```

## Serve command

This command starts a SQLpipe server. To learn more about running a SQLpipe server, check out our setting up a SQLpipe server tutorial.

### Command flag options

```
            --admin-password string      Admin password
            --admin-username string      Admin username
            --analytics                  Send anonymized usage data to SQLpipe for product improvements (default true)
            --create-admin               Create admin user
REQUIRED    --dsn string                 Database backend connection string
            --limiter-burst int          Rate limiter maximum burst (default 200)
            --limiter-enabled            Enable rate limiter (default true)
            --limiter-rps float          Rate limiter maximum requests per second (default 100)
            --max-concurrency int        Max number of concurrent transfers to run on this server (default 20)
            --max-connections int        Max backend db connections (default 50)
            --max-idle-connections int   Max idle backend db connections (default 50)
            --max-idle-time string       Database backend connection string (default "5m")
            --port int                   The port SQLPipe will run on. Default 9000 (default 9000)
            --secret string              Secret key
```

### A few usage notes

* The only required argument is `--dsn`. The rest of the flags have sane defaults
* You can start multiple SQLpipe servers connected to the same backend DB.
* `secret` is used to encrypt HTTP traffic
  * The default value is random 32 character string created by [this function](https://github.com/sqlpipe/sqlpipe/search?q=randomCharacters).
  * You should probably [provide your own random characters](https://media.giphy.com/media/VekcnHOwOI5So/giphy.gif) for a production deployment.
* `--dsn` must be a PostgreSQL connection string (also known as a DSN).
  * PostgreSQL's DSN format: `postgresql://username:password@hostname/dbname?optionalParameters`
  * An example of a PostgreSQL DSN: `postgresql://my-username:TopS3cret@my-hostname.com/production`
* `--create-admin` inserts a user with admin privileges into the database's `user` table with the supplied `--admin-username` and `--admin-password`.
  * Usually, you will run this command the first time you start a SQLpipe server.
* The default rate limiter settings allow each server to handle 100 requests per second _on average_ with peaks of 200 requests per second.

### Serve Examples

#### **Starting a SQLpipe server for the first time**

```
sqlpipe serve \
    --dsn=postgres://my-username:my-password@my-hostname/my-db-name \
    --create-admin \
    --admin-username=sqlpipe-admin \
    --admin-password=VerySecretPassword999!
```

#### **Adding a SQLpipe server, or restarting a server**

```
sqlpipe serve \
    --dsn=postgres://my-username:my-password@my-hostname/my-db-name
```

***

## Version command

Prints the current SQLpipe version.

### Version example usage / output

```
# Run the command like so
sqlpipe version

# Something similar to this should be printed.

Git hash: d3b390c
Human version: 1.0.0
```

### Notes on output

* The git hash tells you exactly what git commit the binary was compiled from.
* Repo at [github.com/sqlpipe/sqlpipe](https://github.com/sqlpipe/sqlpipe)
