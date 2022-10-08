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

The rest of this page will explain how SQLpipe's authentication works, then explain each endpoint and give an example or two how to use them.

### Authentication

If you start SQLpipe with the `-secure` flag, it will automatically generate a 32 character API key and print it to the command line. You can also specify the API key if you wish, by passing the flag:`-token=<your_32_character_token>.`

Then, every client must pass that 32 character token as a bearer token in every request it makes to the server. For example, if you're using curl to send requests, the command would look something like this:

```
curl -H 'Authorization: Bearer <api_key_goes_here>' -d <json_payload_goes_here} localhost:9000/<endpoint>
```

If you _don't_ start the server with the `-secure` flag, SQLpipe will not require any authentication.

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

### /v2/transfer

This endpoint runs a query on a source system and transfers the result to a target system by running insert commands. The killer feature of this command is that it automatically generates create table commands.

This command works well for small to medium size result sets (say, a few hundred to maybe a few million rows), but we recommend exporting to a CSV for larger transfers.

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
    "table": "<target_table_name>"
  },
  "query": "select * from my_table"
}
```

If you are inserting into PostgreSQL, Redshift, or Snowflake, you will need to specify a schema by adding a `schema` key-value pair to the `target` object.

