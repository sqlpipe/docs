# Installation guide

## Installing SQLpipe <a href="#installing-sqlpipe" id="installing-sqlpipe"></a>

This page will show you how to install and configure V1 of SQLpipe.

[Contact us](https://sqlpipe.com/contact) for help getting started with SQLpipe, or check out our [services page](https://sqlpipe.com/services).

#### &#x20;Installation steps <a href="#installation-steps" id="installation-steps"></a>

1. Download SQLpipe
2. Grant permission to run SQLpipe
3. Prepare SQLpipe for operation
   1. Option 1 - As a command line tool
   2. Option 2 - As a server

### &#x20;Download SQLpipe <a href="#download-sqlpipe" id="download-sqlpipe"></a>

You can download SQLpipe from our [downloads page](https://sqlpipe.com/downloads) with a browser, or with a cli tool like curl.

#### &#x20;Build SQLpipe from source <a href="#build-sqlpipe-from-source" id="build-sqlpipe-from-source"></a>

If we do not offer a download for your specific system… Or you just like doing things the hard way :-) You may [compile SQLpipe from source](https://github.com/sqlpipe/sqlpipe).

### &#x20;Grant permission to run SQLpipe <a href="#grant-permission-to-run-sqlpipe" id="grant-permission-to-run-sqlpipe"></a>

If using Linux, macOS or FreeBSD, grant the current user permission to execute the file:

```
sudo chmod +x sqlpipe
```

### Prepare SQLpipe for operation <a href="#prepare-sqlpipe-for-operation" id="prepare-sqlpipe-for-operation"></a>

SQLpipe can be used as:

* A command line tool
* A long-lived server

First, choose which operation mode you want, then follow the appropriate setup steps below.

#### &#x20;Option 1 - As a command line tool <a href="#option-1---as-a-command-line-tool" id="option-1---as-a-command-line-tool"></a>

If you are in the same directory as SQLpipe, you may run it like so:

```
./sqlpipe <command>
```

Where `<command>` is one of SQLpipe’s commands.

However, we recommend adding SQLpipe to your `/usr/local/bin` directory:

```
sudo mv sqlpipe /usr/local/bin
```

Then, you can test your installation with the following command:

```
sqlpipe version
```

If SQLpipe prints a version number, you’re good to go!

&#x20;**Next steps with SQLpipe CLI**

Visit our SQLpipe CLI Usage Guide to learn how to use SQLpipe as a command line tool.

***

#### &#x20;Option 2 - As a server <a href="#option-2---as-a-server" id="option-2---as-a-server"></a>

SQLpipe requires TLS to encrypt HTTP traffic. If you’re unfamiliar with TLS, [check out this article](https://www.cloudflare.com/learning/ssl/transport-layer-security-tls/).

To use SQLpipe as a server, you must:

1. Create a directory
2. Move the SQLpipe executable to the directory
3. Create a folder for TLS certificates
4. Create TLS certificates

&#x20;**Create directory**

You can call the directory whatever you want.

```
mkdir ~/sqlpipe-server-directory
```

&#x20;**Move SQLpipe**

```
mv sqlpipe ~/sqlpipe-server-directory
```

&#x20;**Create TLS directory**

```
cd ~/sqlpipe-server-directory
mkdir tls
```

&#x20;**Create TLS certificates**

The following command will create a self signed TLS certificate, using [openssl](https://www.openssl.org).

```
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out tls/cert.pem -keyout tls/key.pem
```

The program will prompt you for the following information. However, for the purposes of a self signed certificate, none of this really matters.

You must enter a value for at least one field, but it doesn’t have to be real information. For example, you could enter `my-org` for the organization name, and leave everything else blank.

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

If SQLpipe prints a version number, you’re good to go!

&#x20;**Next steps with SQLpipe server**

Visit our SQLpipe Server Usage Guide to learn how to run a SQLpipe server.
