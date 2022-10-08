# Installation

The best way to get started with SQLpipe is with the [official SQLpipe Docker image.](https://hub.docker.com/repository/docker/sqlpipe/sqlpipe)

### Quick and easy

SQLpipe has a number of command line flags available when starting a server, but the defaults are fine for experimentation, or even for use in a protected environment.&#x20;

The following command is all you need to run a SQLpipe API server on port 9000:

`docker run --name sqlpipe sqlpipe/sqlpipe`

### More control

The following command will open a bash shell inside a running SQLpipe container:

`docker run --name sqlpipe -it sqlpipe/sqlpipe bash`

Then, you will have the ability to run the SQLpipe binary manually, which gives you access to command line flags. FYI, the SQLpipe binary is in the root of the Docker container's file system.&#x20;

For example, if you want to generate an API key that must be provided by clients to make requests to the server, you could run:

`./sqlpipe --secure`

The next section will include more information about the available command line flags.

### Adding ODBC drivers

SQLpipe's Docker image currently comes pre-installed with unixODBC and the following drivers:

* SQL Server
* Snowflake
* PostgreSQL (also works for Redshift)
* MySQL (also works for MariaDB)

Almost every modern data system has an ODBC driver available. If you'd like to add your own drivers to connect to additional sources, check out the [Dockerfile](https://github.com/sqlpipe/sqlpipe/blob/main/Dockerfile) that builds the image to see how that is done.

If you get frustrated trying to add a driver, [contact us](https://sqlpipe.com/contact) for help. We can add a driver to the image for a one-time fee, and offer a money back guarantee if you don't end up using it.

### Next steps

Move on to the next page to learn how to interact with SQLpipe's CLI.
