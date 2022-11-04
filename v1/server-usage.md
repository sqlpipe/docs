# Server usage

This guide will teach you how to set up and use a SQLpipe server.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe.

## Prerequisites

* You must have completed the SQLpipe installation guide.
* You must have a PostgreSQL database for SQLpipe to use as a backend.
* Optionally, you can prepare two supported systems to transfer data between.

If you are not sure how to set up a PostgreSQL database, there are many tutorials on the internet, such as this one: [Set up a PostgreSQL DB locally with Docker](https://sqlref.com/tutorials/intro\_to\_sql/create\_local\_database.html).

## Prepare the PostgreSQL DB

A SQLpipe server uses PostgreSQL for:

* User authentication
* Storing connection data
* Recording transfer and query results

Below we show how to create a new DB and intialize it for SQLpipe.

### Create a new DB

Connect to PostgreSQL and create a fresh database. You can call it whatever you want.

```
CREATE DATABASE sqlpipe;
```

### Initialize the DB

SQLpipe's `initialize` command creates the necessary schema which a SQLpipe server needs to run.

You must pass a [PostgreSQL connection string](https://www.postgresql.org/docs/14/libpq-connect.html#id-1.7.3.8.3.6) via the `dsn` flag, like so:

```
sqlpipe initialize --dsn="postgres://<username>:<password>@<hostname>/<db-name>"
```

You can optionally skip confirmation by passing the `--force` flag.

## Start the server and create an admin user

Next, we need to start the server, and create an admin user that is able to log in to system.

Starting a SQLpipe server is accomplished with the `serve` command. If you pass the `--create-admin` flag, as well as `--admin-username` and `--admin-password`, it will also create a user with admin privileges.

Use the following command as a template:

```
sqlpipe serve \
    --create-admin \
    --admin-username=<desired-admin-username> \
    --admin-password=<desired-admin-password> \
    --dsn="postgres://<username>:<password>@<hostname>/<db-name>"
```

You can view example usage of the `serve` command on the CLI reference page.

If the server starts succesfully, the terminal will print messages like this:

```
{"level":"INFO","time":"2022-02-10T10:14:45Z","message":"database connection pool established"}
{"level":"INFO","time":"2022-02-10T10:14:45Z","message":"successfully created admin user"}
{"level":"INFO","time":"2022-02-10T10:14:45Z","message":"starting server","properties":{"addr":":9000"}}
```

## Login to the web UI

SQLpipe's default port is 9000. You can change that port with the `serve` command's `--port` flag.

To login to the UI, visit [https://localhost:9000](https://localhost:9000). You should see the following screen:

Type the admin username and password that you passed to the `serve` command to login.

If login is successful, you will see the transfers screen.

## Create a connection (or two!)

One of SQLpipe's supported systems is PostgreSQL, which is the database SQLpipe uses as a backend. This means we have two options for running our first transfer, we can either:

* Transfer data between two data systems that we have previously prepared.
* Transfer data from our PostgreSQL backend, through SQLpipe, then back to our PostgreSQL backend.

Transferring data from SQLpipe's backend, to SQLpipe's backend, is not very useful, but it is an easy way to get started. And so, that is what this guide will show you how to do.

However, feel free to add two connections of your own, and transfer between those systems, instead of following along with this contrived example.

### Create your first connection

Navigate to the Connections page, and click on the blue plus button to create a new connection.

You will be greeted by a form asking for connection information:

A few notes on these fields:

* Name can be anything you want, but must be unique.
* Hostname and Port are required, unless Data system type is Snowflake. If it is Snowflake, then Account ID is required.
* Skip connection test will skip SQLpipe's automatic check to see if it can connect with the given information.

### Fill out the fields

If you want to transfer from SQLpipe's backend, to SQLpipe's backend, and are using a local PostgreSQL DB, enter the following information:

* Name: `My First Connection`
* Data system type: `PostgreSQL`
* Hostname: `localhost`
* Port: `5432`
* DB Name: The name of the PostgreSQL DB that you created previously
* Username: Your PostgreSQL username
* Password: Your PostgreSQL user's password
* Do not check the "Skip connection test" box

Otherwise, enter the data of your own data systems. My form looks like this:

Submit the form, and if everything works, you will get a success message.

## Create a transfer

Time to get to the good stuff!

Navigate to the transfers page, and click the blue plus button to create a transfer.

You will be greeted by another form:

An explanation of the fields:

* Source is the connection that SQLpipe will extract data from
* Target is the connection SQLpipe will insert into
* Target schema is the schema that SQLpipe will insert into.
  * This field is only required on systems that have schema support.
  * PostgreSQL supports schemas. It's default schema is `public`.
* Target table is the table SQLpipe will insert into
* Query is the query that will run on the source system.
* Overwrite will drop the target table (if it exists) and create a new table with:
  * The same column names as the query result
  * Column types that will accept the query result's data

One of the tables that SQLpipe uses to operate is `connections`. If you were able to add the backend DB connection, there should be a record in it. So, let's transfer the `connections` table to another table. Here is what my form looks like:

### Viewing a transfer's details

After submitting the form, you will come to a screen showing the transfer's details. Notice how the status is `queued`. All transfers and queries submitted to a SQLpipe server start as `queued`, before eventually going `active` (when the server has resources to spare), and then hopefully being marked as `complete`.

However, if a query or transfer encounters an error, the status will be marked as `error`, and the error will show on the page.

Also, transfers can be cancelled with the red X button in the top right of the transfer details page. This will mark the transfer's status as `cancelled`.

Refresh the page, and the transfer's status should be `complete` by now:

### Check the transferred data in the DB

Time for the moment of truth - did the data really transfer?

Login to your target database and look at the target table that you specified.

In my case, the target table was `my_new_table`, and it should show the connection I just added.

```
sqlpipe=# select * from my_new_table;
 id |       created_at       |        name         |  ds_type   | username | password  | account_id | hostname  | port | db_name | version 
----+------------------------+---------------------+------------+----------+-----------+------------+-----------+------+---------+---------
  1 | 2022-02-10 10:56:43+00 | My First Connection | postgresql | postgres | NotTellin |            | localhost | 5432 | sqlpipe |       1
(1 row)
```

And there it is!

## Using the API

You can also use SQLpipe's API to trigger transfers - this makes it a great solution for triggering transfers with a workflow automation tool like [Airflow](https://airflow.apache.org), or anything that can send a POST request.

### Authentication

SQLpipe requires every API request to authenticate with [HTTP basic auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication).

### Routes

SQLpipe's API is quite flexible - anything you can do on the UI can be done via the API.

It allows you to perform [CRUD operations](https://www.codecademy.com/article/what-is-crud) on:

* Transfers
* Queries
* Connections
* Users

All routes are listed on our API reference page.

### Example API request

Let's create another transfer via the API. We must send a `POST` request with the following fields:

```
sourceID        int
targetID        int
query           string
targetSchema    string
targetTable     string
overwrite       bool
```

To the following route:

```
/api/v1/transfers
```

We can use curl to send this request:

```
curl -k -i \
    -u <your-sqlpipe-admin-username>:<your-sqlpipe-password> \
    -d '{"sourceId": 1, "targetId": 1, "overwrite": true, "targetSchema": "public", "targetTable":  "my_second_table", "query": "select * from connections"}' \
    https://localhost:9000/api/v1/transfers
```

If everything goes OK, you will receive a response like this:

```
HTTP/2 202 
content-type: application/json
content-length: 347
date: Thu, 10 Feb 2022 12:09:55 GMT

{
        "transfer": {
                "id": 2,
                "createdAt": "2022-02-10T12:09:55Z",
                "sourceID": 1,
                "targetID": 1,
                "query": "select * from connections",
                "targetSchema": "public",
                "targetTable": "my_second_table",
                "overwrite": true,
                "status": "queued",
                "error": "",
                "errorProperties": "",
                "stoppedAt": "0001-01-01T00:00:00Z",
                "version": 1
        }
}
```

And the SQLpipe server logs will show that it received your request:

```
{"level":"INFO","time":"2022-02-10T12:09:54Z","message":"Request received","properties":{"Method":"POST","Protocol":"HTTP/2.0","Remote address":"[::1]:60504","Requested address":"/api/v1/transfers"}}
{"level":"INFO","time":"2022-02-10T12:09:55Z","message":"now running a transfer","properties":{"CreatedAt":"10 Feb 2022 12:09:55 UTC","ID":"2","Overwrite":"true","Query":"select * from connections","SourceID":"0","Status":"active","TargetID":"0","TargetSchema":"public","TargetTable":"my_second_table"}}
```

## Wrapping up

Hopefully you are finding that SQLpipe is easy to use, and somewhat self explanatory.

Also, please [contact us](https://sqlpipe.com/contact) if you think you found an error in this guide.
