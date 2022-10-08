# CLI

SQLpipe does not have any "sub commands" - if you run SQLpipe's binary, it starts the server. However, there are a few command line flags available:

<pre><code>-port
-secure
-token
<strong>-limiter-enabled
</strong>-limiter-rps
-limiter-burst
-version</code></pre>

* `-port` Specifies what port SQLpipe will listen on. Default 9000.
* `-secure` If true, will require clients to provide an API token. Default false.
* `-token` Sets the API token. Must be 32 characters.
  * If `-token` is provided without `-secure`, SQLpipe automatically sets `-secure` to true.
  * If `-secure` is provided without `-token`, SQLpipe will automatically generate a 32 character API token.
* `-limiter-enabled` Enables the rate limiter, which limits the amount of requests that a given client (IP address) can send. Default false.
* `-limiter-rps` Limits the average amount of requests per second from client. Default 10.
* `-limiter-burst` Allows a temporary burst of requests, per the token bucket rate limiting algorithm.
* `-version` Prints the current version (in git hash) of SQLpipe and exits.

### "Production ready" startup example

If you start SQLpipe's container as specified under the "more control" heading of the installation page, running the following command in the root of the container's file system will spin up something that will work fine in a production environment:

```
./sqlpipe -secure
```

That's it! Let's move on to the good stuff - the API.
