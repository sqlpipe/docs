# API Reference

This page defines SQLpipe's API routes and the data payloads they expect to receive.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe.

## All requests must be authenticated with [HTTP Basic Auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication).

## Transfers

### Create Transfer

Send a `POST` request to:

```
/api/v1/transfers
```

With the following fields:

```
sourceID        int
targetID        int
query           string
targetSchema    string
targetTable     string
overwrite       bool
```

***

### List Transfers

Send a `GET` request to:

```
/api/v1/transfers
```

With the following optional [query parameters](https://branch.io/glossary/query-parameters/):

```
page
page_size
sort
```

* `page` and `page_size` are for pagination.
* `sort`'s value may be `id` or `created_at`

***

### Transfer Details

Send a `GET` request to:

```
/api/v1/transfers/<transfer_id>
```

***

### Cancel Transfer

Send a `PATCH` request to:

```
/api/v1/cancel-transfer/<transfer_id>
```

***

### Delete Transfer

Send a `DELETE` request to:

```
/api/v1/transfers/<transfer_id>
```

***

## Queries

### Create Query

Send a `POST` request to:

```
/api/v1/queries
```

With the following fields:

```
connectionId    int
query           string
```

***

### List Queries

Send a `GET` request to:

```
/api/v1/queries
```

With the following optional [query parameters](https://branch.io/glossary/query-parameters/):

```
page
page_size
sort
```

* `page` and `page_size` are for pagination.
* `sort`'s value may be `id` or `created_at`

***

### Query Details

Send a `GET` request to:

```
/api/v1/queries/<query_id>
```

***

### Cancel Query

Send a `PATCH` request to:

```
/api/v1/cancel-query/<query_id>
```

***

### Delete Query

Send a `DELETE` request to:

```
/api/v1/queries/<query_id>
```

***

## Connections

### Create Connection

Send a `POST` request to:

```
/api/v1/queries
```

With the following fields:

```
connectionId    int
query           string
```

***

### List Connections

Send a `GET` request to:

```
/api/v1/connections
```

With the following optional [query parameters](https://branch.io/glossary/query-parameters/):

```
page
page_size
sort
```

* `page` and `page_size` are for pagination.
* `sort`'s value may be `id` or `created_at`

***

### Connection Details

Send a `GET` request to:

```
/api/v1/connections/<connection_id>
```

***

### Update Connection

Send a `PATCH` request to:

```
/api/v1/connections/<connection_id>
```

Using the same fields as you would to create a connection.

***

### Delete Connection

Send a `DELETE` request to:

```
/api/v1/connection/<connection_id>
```

***

## Users

### Create User

Send a `POST` request to:

```
/api/v1/users
```

With the following fields:

```
username    string
password    string
admin       bool
```

***

### List Users

Send a `GET` request to:

```
/api/v1/users
```

With the following optional [query parameters](https://branch.io/glossary/query-parameters/):

```
page
page_size
sort
```

* `page` and `page_size` are for pagination.
* `sort`'s value may be `id` or `created_at`

***

### User Details

Send a `GET` request to:

```
/api/v1/users/<user_id>
```

***

### Update User

Send a `PATCH` request to:

```
/api/v1/users/<user_id>
```

Using the same fields as you would to create a user.

***

### Delete User

Send a `DELETE` request to:

```
/api/v1/user/<user_id>
```
