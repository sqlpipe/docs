# CLI usage

This guide will teach you how to use SQLpipe's CLI to transfer data and run queries.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe, or check out our [services page](https://sqlpipe.com/services).

## Prerequisites

* You must have completed the SQLpipe installation guide.
* You must have two supported systems to transfer data between.

## Gather information

Gather the following information about your source and target data systems:

* Data system type (we call this `ds-type`) from the supported systems page.
* Hostname and port, or [Snowflake account ID](https://docs.snowflake.com/en/user-guide/admin-account-identifier.html#where-are-account-identifiers-used)
* Username
* Password
* DB Name

You must also:

* Write a query to extract data from the source system.
* Specify a table name to insert the query result into.
* Depending on the target system, you may also have to specify a target schema.

## Run a transfer

Take the information you gathered, and fill out the following template.

A few "gotchas" are discussed below, be sure to read them before running the command.

```
sqlpipe transfer \
  --source-ds-type "<your source ds-type>" \
  --source-hostname "<your source hostname>" \
  --source-port <your source port> \
  --source-username "<your source username>" \
  --source-password "<your source password>" \
  --source-db-name "<your source db name>" \
  --query "<your query>" \
  --target-ds-type "<your target ds-type>" \
  --target-hostname "<your target hostname>" \
  --target-port <your target port> \
  --target-username "<your target username>"
  --target-password "<your target password>" \
  --target-db-name "<your target db name>" \
  --target-schema "<your target schema>" \
  --target-table "<your target table>" \
  --overwrite
```

To see example `transfer` commands, head to our CLI reference page.

### A few things to note

#### Snowflake

If using a Snowflake DB, replace the hostname and port flags with an account id.

For example, remove the `--target-hostname` and `--target-port` flags and insert `--target-account-id`.

#### Schema support

Not all databases support schemas. For example, MySQL doesn't support schemas. This will require knowledge of your particular system.

* To specify a source DB’s schema name, put it in the query.
* To specify a target DB’s schema name, pass it via command line flag.
* If your source DB doesn't support schemas, you don't need to do anything differently.
* If your target DB doesn't support schemas, do not pass a `--target-schema` flag.

Examples on how to specify schemas is available in the CLI Reference page.

#### Overwrite

The command above uses the `--overwrite` flag. This tells SQLpipe to:

1. Drop the target table in the target database (if it exists)
2. Create a new table with:
   * The same column names
   * The proper column data types to hold the incoming data

If you do not use the `--overwrite` flag, and a table with the proper data types does not already exist, SQLpipe will throw an error.

## Run a query

In addition to transferring data, the SQLpipe CLI allows you to run a query against a single database.

The `query` command requires a lot of the same information as the `transfer` command, but just for one database instead of two. Please refer to the gather information section above.

Also, all of the same "gotchas" apply, such as Snowflake having different requirements, and some systems requiring schemas while others don't. Please read the a few things to note section above.

Take the information you gathered, and fill out the following template:

```
sqlpipe query \
  --connection-ds-type "<your connection ds-type>" \
  --connection-hostname "<your connection hostname>" \
  --connection-port <your connection port> \
  --connection-username "<your connection username>" \
  --connection-password "<your connection password>" \
  --connection-db-name "<your connections DB name" \
  --query "<your query to run>"
```

To see an example `query` command, head to our CLI reference page

Note that the query command will only return a success or error message, not query results.
