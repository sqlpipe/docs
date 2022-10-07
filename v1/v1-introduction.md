# v1 introduction

This page introduces SQLpipe, explains how it works and describes how to use it.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe, or check out our [services page](https://sqlpipe.com/services).

## What is SQLpipe?

SQLpipe is an [open-source](https://github.com/sqlpipe/sqlpipe) program that moves data between databases.

It is written in Go, requires no dependencies, and allows you to:

* Build ETL / data pipelines
* Perform one time data transfers
* Run arbitrary queries

## How does it work?

At a high level, it works by:

1. Running a given query on a source system
2. Converting the query result's data types to be compatible with a target system
3. Inserting the query result into the target

## How do I use SQLpipe?

SQLpipe can be used in two ways:

* A command line tool
* A long-lived server

### Command line tool

SQLpipe's CLI can transfer data with a single command, and it is the easiest way to get started.

This operation mode is great for one-time data transfers, migrations, or jobs run locally via cron.

### Server

Running a SQLpipe server takes more effort to set up, but grants additional features:

* Rest API
* Web UI
* User authentication
* Ability to save connection information
* Horizontal scalability

## Get started

Visit the SQLpipe installation guide for step by step instructions on installing SQLpipe.
