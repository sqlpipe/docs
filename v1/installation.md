# Installation

This page will show you how to install and configure SQLpipe.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe, or check out our [services page](https://sqlpipe.com/services).

## Download SQLpipe

You can [download SQLpipe](https://sqlpipe.com/downloads) with a browser, or with a command line tool like [curl](https://curl.se).

### With a browser

Head to our [downloads page](https://sqlpipe.com/downloads) and download the correct version of SQLpipe for your system.

### With curl

SQLpipe is available for download according to the following URL convention:

```
https://downloads.sqlpipe.com/<version>/<operating-system>/<processor-architecture>/sqlpipe[.exe]
```

* Replace `<version>` with the version you want, or `latest`.
* Replace `<operating-system>` with one of `[macos, linux, windows, freebsd]`
* Replace `<processor-architecture>` with one of `[arm, x86]`
* If downloading a Windows version, add `.exe` to the end

#### Copy/pastable curl commands, for your convenience

The following curl commands will download SQLpipe version 1.0.1:

```
# Linux x86
curl -O https://downloads.sqlpipe.com/1.0.1/linux/x86/sqlpipe

# macOS x86
curl -O https://downloads.sqlpipe.com/1.0.1/macos/x86/sqlpipe

# Linux ARM
curl -O https://downloads.sqlpipe.com/1.0.1/linux/arm/sqlpipe

# macOS ARM
curl -O https://downloads.sqlpipe.com/1.0.1/macos/arm/sqlpipe
```

### Build SQLpipe from source

If we do not offer a download for your specific system... Or you just like doing things the hard way :-) You may [compile SQLpipe from source](https://github.com/sqlpipe/sqlpipe).

***

## Grant permission to run SQLpipe

If using Linux, macOS or FreeBSD, grant the current user permission to execute the file:

```
sudo chmod +x sqlpipe
```

***

## Prepare SQLpipe for operation

SQLpipe can be used as:

* A command line tool
* A long-lived server

First, choose which operation mode you want, then follow the appropriate setup steps below.

***

### Option 1 - As a command line tool

If you are in the same directory as SQLpipe, you may run it like so:

```
./sqlpipe <command>
```

Where `<command>` is one of SQLpipe's commands.

However, we recommend adding SQLpipe to your `/usr/local/bin` directory:

```
sudo mv sqlpipe /usr/local/bin
```

Then, you can test your installation with the following command:

```
sqlpipe version
```

If SQLpipe prints a version number, you're good to go!

#### Next steps with SQLpipe CLI

Visit our SQLpipe CLI Usage Guide to learn how to use SQLpipe as a command line tool.

***

### Option 2 - As a server

SQLpipe requires TLS to encrypt HTTP traffic. If you're unfamiliar with TLS, [check out this article](https://www.cloudflare.com/learning/ssl/transport-layer-security-tls/).

To use SQLpipe as a server, you must:

1. Create a directory
2. Move the SQLpipe executable to the directory
3. Create a folder for TLS certificates
4. Create TLS certificates

#### Create directory

You can call the directory whatever you want.

```
mkdir ~/sqlpipe-server-directory
```

#### Move SQLpipe

```
mv sqlpipe ~/sqlpipe-server-directory
```

#### Create TLS directory

```
cd ~/sqlpipe-server-directory
mkdir tls
```

#### Create TLS certificates

The following command will create a self signed TLS certificate, using [openssl](https://www.openssl.org).

```
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out tls/cert.pem -keyout tls/key.pem
```

The program will prompt you for the following information. However, for the purposes of a self signed certificate, none of this really matters.

You must enter a value for at least one field, but it doesn't have to be real information. For example, you could enter `my-org` for the organization name, and leave everything else blank.

```
Country Name (2 letter code) []:        
State or Province Name (full name) []:
Locality Name (eg, city) []:
Organization Name (eg, company) []:
Organizational Unit Name (eg, section) []:
Common Name (eg, fully qualified host name) []:
Email Address []:
```

Then, you can test your installation with the following command:

```
./sqlpipe version
```

If SQLpipe prints a version number, you're good to go!

#### Next steps with SQLpipe server

Visit the SQLpipe Server Usage Guide to learn how to run a SQLpipe server.
