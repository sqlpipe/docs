# SQLpipe introduction

This page introduces SQLpipe, explains how it works and describes how to use it.

All new development work should use v2 of SQLpipe. You can always contact us for help.

## What is SQLpipe?

SQLpipe is an [open-source](https://github.com/sqlpipe/sqlpipe) program that moves data between databases.

It is written in Go, requires no dependencies, and allows you to:

* Transfer data between databases of different types
* Export data to a CSV or S3 bucket
* Run arbitrary queries (drops, renames, table creates, etc).

## How does it work?

At a high level, it works by:

1. Running a given query on a source system
2. Converting the query result's data types to be compatible with a target system
3. Inserting the query result into the target

## What is the difference between v1 and v2?

The second version of SQLpipe should be used for new development. V2 makes use of ODBC database drivers, while v1 relies on pure-go drivers. In practical terms, this means v2 can connect to almost all modern databases. Also, v2 can export CSVs directly to S3 buckets - a much asked for feature.

Anecdotally, we also think the code is much better in v2. It was a "from scratch" re-write, reflecting lessons learned while developing v1. These code improvements show up in much improved input-checking, error handling, and a smaller, more focused (but also more flexible) API.

## Get started

Move on to the installation guide for step by step instructions on installing SQLpipe.
