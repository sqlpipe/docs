# API

SQLpipe exposes its functionality via a REST API. The endpoints are:

```
/v2/query           -- Runs an arbitrary query on a system and pretty-prints the result
/v2/transfer        -- Transfers data from a source system to a supported target system
/v2/csv/download    -- Runs a query on a source and downloads the results as a CSV
/v2/csv/s3          -- Runs a query on a source and uploads the result as a CSV to S3
/v2/csv/save        -- Runs a query on a source and saves the result as a CSV on the server

/v2/healthcheck
```

The rest of this page will explain how SQLpipe's authentication works, mention a few things about ODBC, then explain each endpoint and give an example or two how to use them.

### Authentication

If you start SQLpipe with the `-secure` flag, it will automatically generate a 32 character API key and print it to the command line. You can also specify the API key if you wish, by passing the flag:`-token=<your_32_character_token>.`

Then, every client must pass that 32 character token as a bearer token in every request it makes to the server. For example, if you're using curl to send requests, the command would look something like this:

```
curl -H 'Authorization: Bearer <api_key_goes_here>' -d <json_payload_goes_here} <sqlpipe_host>:9000/<endpoint>
```

If you start the server _without_ the `-secure` flag, SQLpipe will not require any authentication.

### ODBC

#### Connection strings

ODBC is a database API specification created by Microsoft in the 90s. Pretty much every database, and even many APIs, have one or more driver that allows application to access them in a standardized way.

Connecting to a system through ODBC requires you to write a connection string in a standardized format. Most of the time, they look like this:

```
Driver=<odbc_driver_name>;Server=<hostname>;Port=<port_number>;Database=<db_name>;Uid=<username>;Pwd=<your_password>;
```

Many drivers allow you to specify additional fields that change the behavior of the driver. For example, when using FreeTDS, a SQL Server driver, you may have to specify `TDS_Version` according to which version of SQL Server you are trying to connect to.&#x20;

#### Drivers

ODBC driver names are specified by the user when installed on the server. The driver names on SQLpipe's Docker image are named as follows:

* [PostgreSQL](https://odbc.postgresql.org/)
* [MSSQL](https://www.freetds.org/)
* [MySQL](https://dev.mysql.com/doc/connector-odbc/en/)
* [Snowflake](https://docs.snowflake.com/en/user-guide/odbc.html)

When using the SQLpipe Docker image, use those names (case sensitive!) for the "Driver" field. Click on the names above to be taken to the driver's documentation to learn about additional configuration options.

### /v2/query

Sending a request to this endpoint runs a query on a given system. It pretty prints the results if there are any, and returns a success message for queries that don't return a result (like `CREATE TABLE` or other DDL commands).

This endpoint is very useful for things like creating and dropping schemas, or running transform queries after you've run a data pipeline.

Here is an example of what the JSON payload to that endpoint should look like:

```
{
  "source": {
    "odbc_dsn": "Driver=<odbc_driver_name>;Server=<hostname>;Port=<port_number>;Database=<db_name>;Uid=<username>;Pwd=<your_password>;"
  },
  "query": "select * from my_table"
}
```

And a full curl command example:

```
curl -d '{"source": {"odbc_dsn": "Driver=PostgreSQL;Server=my-postgres-host.com;Port=5432;Database=postgres;Uid=postgres;Pwd=MySuperTopSecretPWD654;"}, "query": "select * from my_table"}' localhost:9000/v2/query
```

### /v2/csv/download

This endpoint requires the same input as the `/v2/query` endpoint, but instead of pretty-printing the results, it responds with a CSV file. So, if you wanted to run the same query as shown above, but download the result as a CSV, you could run:

```
curl -o results.csv -d '{"source": {"odbc_dsn": "Driver=PostgreSQL;Server=my-postgres-host.com;Port=5432;Database=postgres;Uid=postgres;Pwd=MySuperTopSecretPWD654;"}, "query": "select * from my_table"}' localhost:9000/v2/csv/download
```

If you're not familiar with curl, the `-o results.csv` is telling curl to save the response as a file named `results.csv` If you don't pass this flag, it will just print the response, instead of saving it.

FYI, this command creates a CSV file and saves it in your temp directory (`/tmp` in the official Docker image), where it will eventually be deleted by your OS.

### /v2/csv/save

This endpoint will save the result of the query on the server in a location you specify. Its input requirements are similar to `/v2/query`, but you must also pass a `write_location` field:

```
{  
  "source": {
    "odbc_dsn": "Driver=<odbc_driver_name>;Server=<hostname>;Port=<port_number>;Database=<db_name>;Uid=<username>;Pwd=<your_password>;"
  },
  "write_location": "/tmp/results.csv",
  "query": "select * from my_table"
}
```

### /v2/csv/s3

Last but not least, SQLpipe can export the CSV result of a query directly to an s3 bucket. Just like `/v2/csv/download`, it writes the CSV to disk before uploading.

There are a few additional fields required to authenticate the request to AWS. Here is what a payload should look like for this endpoint:

```
{
  "source": {
    "odbc_dsn": "Driver=<driver_name>;Server=<source_host>;Port=<source_port>;Database=<db_name>;Uid=<username>;Pwd=<password>;"
  },
  "target": {
    "aws_region": "<aws-region>",
    "aws_key": "<aws_key>",
    "aws_secret": "<aws_secret>",
    "s3_bucket": "<s3_bucket_name... do not prefix with 's3://'>",
    "s3_dir": "<s3_bucket_dir ... optional>",
    "file_name": "my-file.csv"
  },
  "query": "select * from my_table"
}
```

A full curl command example would look something like:

```
curl -d '{"source": {"odbc_dsn": "Driver=PostgreSQL;Server=localhost;Port=5432;Database=postgres;Uid=postgres;Pwd=Hackeroonie345!;"}, "target": {"aws_region": "us-east-1", "aws_key": "KJDHFDS43534kjhsdf", "aws_secret": "kjhsaduKJHGKJHGdg7876&*^jhdksjh", "s3_bucket": "my-target-bucket", "s3_dir": "data-dir", "file_name": "my-file.csv"}, "query": "select * from my_table"}' localhost:9000/v2/csv/s3
```

### /v2/transfer

This endpoint runs a query on a source system, creates a new table in the target system, and transfers the query result by running insert commands.&#x20;

The killer feature of this command is that it can automatically generate create table commands, with the proper column names and types.

This command works well for small to medium size result sets (say, a few hundred to maybe a few million rows), but we recommend exporting to a CSV for larger transfers.&#x20;

A cool "hack" is to run a transfer with a query like `select * from my_table limit 0` to create a new table with the correct types, then use one of the CSV functions to export a CSV. Most databases have a way to import CSV files in bulk with high performance, and thus you can get the best of both worlds: An automatically created table, along with high performance.

FYI, SQLpipe can _export_ data from any system with an ODBC driver, but currently can only insert data to PostgreSQL (with limited Redshift support), MySQL, MariaDB, Snowflake, and SQL Server.

Here are the endpoint's required payload keys:

```
{
  "source": {
    "odbc_dsn": "Driver=<driver_name>;Server=<hostname>;Port=<port_number>;Database=<db_name>;Uid=<username>;Pwd=<password>;"
  },
  "target": {
    "system_type": "<postgresql, mysql, mssql, or snowflake>",
    "odbc_dsn": "Driver=<driver_name>;Server=<hostname>;Port=<port_number>;Database=<db_name>;Uid=<username>;Pwd=<password>;",
    "table": "<target_table_name>",
    "schema" "<OPTIONAL_schema"
  },
  "query": "select * from my_table"
}
```

If you are inserting into PostgreSQL, Redshift, or Snowflake, you will need to specify a schema by adding a `schema` key-value pair to the `target` object.

Here is an example curl request:

```
curl -d '{"source": {"odbc_dsn": "DRIVER=MSSQL;SERVER=my-sql-server-host.com;PORT=1433;UID=sa;PWD=MyTopS3cretpass193;TDS_Version=7.0"}, "target": {"system_type": "postgresql", "odbc_dsn": "Driver=PostgreSQL;Server=postgresql-host.com;Port=5432;Database=postgres;Uid=postgres;Pwd=dontHackMe!;", "schema": "public", "table": "my_new_table"}, "query": "select * from whatever_table"}' localhost:9000/v2/transfer
```

