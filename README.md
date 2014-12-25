H2O - an optimized HTTP server with support for HTTP/1.x and HTTP/2
===

[![Build Status](https://travis-ci.org/h2o/h2o.svg?branch=master)](https://travis-ci.org/h2o/h2o)

H2O is a very fast HTTP server written in C.  It can also be used as a library.

### Features

- HTTP/1.0, HTTP/1.1
 - uses [PicoHTTPParser](https://github.com/h2o/picohttpparser)
 - persistent connections
 - chunked encoding
- [HTTP/2](http://http2.github.io/)
 - draft 14
 - negotiation methods: NPN, ALPN, Upgrade, direct
 - weight-based prioritization
- [WebSocket](http://www.ietf.org/rfc/rfc6455.txt)
 - uses [wslay](https://github.com/tatsuhiro-t/wslay/)
 - only usable at library level
- TLS
 - uses [OpenSSL](https://www.openssl.org/)
 - forward secrecy
 - AEAD ciphers
 - session resumption (internal memory)
- static file serving
 - conditional GET using last-modified / etag
 - directory listing
 - mime-type configuration
- reverse proxy
 - HTTP/1 only (no HTTPS)
 - persistent upstream connection
- access-logging
 - apache-like format strings
- graceful restart and self-upgrade
 - via [Server::Starter](http://search.cpan.org/~kazuho/Server-Starter-0.17/start_server)

Using the Standalone Server
---

### Installation

Following softwares are required to build the standalone server.  It is likely that you would be possible to find and install them as part of your operation system (by running yum, apt-get, brew, etc. depending on the OS).

- [cmake](http://www.cmake.org/)
- [libyaml](http://pyyaml.org/wiki/LibYAML)
- [OpenSSL](https://www.openssl.org/) (1.0.2 or above is recommended)

Run the commands below.  Last command installs `h2o` (the standalone server) to `usr/local`.

```
$ cmake -DCMAKE_INSTALL_PREFIX=/usr/local .
$ make
$ sudo make install
```

### Running the Server

First, let's try running the server using a configuration file included in the `examples/` directory.  The command below invokes the standalone server using [examples/h2o/h2o.conf](https://github.com/kazuho/h2o/blob/master/examples/h2o/h2o.conf), which directs the server to listen on port 8080.  Try accessing [http://127.0.0.1:8080/](http://127.0.0.1:8080/).

```
$ h2o -c examples/h2o/h2o.conf
```

Use `--help` to print a list of command line options and configuration directives that can be used.

```
$ ./h2o --help
```

Building the Library
---

H2O can also be used as a software library with [libuv version 1.0.0](https://github.com/joyent/libuv).
Note that prior versions of libuv cannot be used due to massive changes to the APIs in 1.0.0.

Examples can be found within the [examples/](https://github.com/kazuho/h2o/blob/master/examples/) directory.

For the time being, using libh2o as a submodule is the recommend way.

```
$ cmake .
$ make libh2o
```

Benchmarks
---

__HTTP/1.1__

|Server \ size of content|6 bytes|4,096 bytes|
|------------------------|------:|----------:|
|nginx/1.7.4 ([conf](https://gist.github.com/kazuho/c9c12021567e3ab83809))            | 45,866|     47,579|
|H2O @ eef1612           | 73,800|     63,768|

note: `wrk -c 500 -d 30 -t 1` on Ubuntu 14.04 on VMWare Fusion

__HTTP/2__

|Server \ size of content|6 bytes|4,096 bytes|
|------------------------|------:|----------:|
|tiny-nghttpd ([nghttpd @ 9c0760e](https://github.com/tatsuhiro-t/nghttp2/)) |146,506|77,352|
|[trusterd @ 962d031](https://github.com/matsumoto-r/trusterd) |125,482|50,103|
|H2O @ 7505a82           |201,077|     90,810|

note: `h2load -c 500 -m 100 -n 2000000` on Ubuntu 14.04 on VMWare Fusion
