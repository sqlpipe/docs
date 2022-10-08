# Installation

The easiest way to get started with SQLpipe is with the [official SQLpipe Docker image.](https://hub.docker.com/repository/docker/sqlpipe/sqlpipe) We very much recommend using and building upon the Docker image.

#### Quick and easy

SQLpipe has a number of command line flags available when starting a server, but the defaults are fine for experimentation, or even for use in a protected environment.&#x20;

The following command is all you need to run a SQLpipe API server on port 9000:

`docker run --name sqlpipe sqlpipe/sqlpipe`

#### More control

The following command will open a bash shell inside a running SQLpipe container:

`docker run --name sqlpipe -it sqlpipe/sqlpipe bash`

Then, you will have the ability to run the SQLpipe binary manually, which gives you access to command line flags. FYI, the SQLpipe binary is in the root of the Docker container's file system.&#x20;

For example, if you want to generate an API key that must be provided by clients to make requests to the server, you could run:

`./sqlpipe --secure`

To learn more about available command line flags, check out the CLI reference.

