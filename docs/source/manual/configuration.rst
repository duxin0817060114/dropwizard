.. _man-configuration:

##################################
Dropwizard Configuration Reference
##################################

.. highlight:: text

.. _man-configuration-servers:

Servers
=======

Tweaking some of the options will require good understanding of how Jetty is working. See the `Jetty architecture chapter`_ for reference.

.. code-block:: yaml

    server:
      type: default
      maxThreads: 1024

.. _Jetty architecture chapter: https://www.eclipse.org/jetty/documentation/current/#architecture


.. _man-configuration-all:

All
---

=================================== ===============================================  =============================================================================
Name                                Default                                          Description
=================================== ===============================================  =============================================================================
type                                default                                          - default
                                                                                     - simple
maxThreads                          1024                                             The maximum number of threads the thread pool is allowed to grow. Jetty will throw ``java.lang.IllegalStateException: Insufficient threads:`` in case of too aggressive limit on the thread count.
minThreads                          8                                                The minimum number of threads to keep alive in the thread pool. Note that each Jetty connector consumes threads from the pool. See :ref:`HTTP connector <man-configuration-http>` how the thread counts are calculated.
maxQueuedRequests                   1024                                             The maximum number of requests to queue before blocking
                                                                                     the acceptors.
responseMeteredLevel                COARSE                                           The response metered level to decide what response code meters are included
metricPrefix                        (none)                                           The metricPrefix to use in the metric name for jetty metrics
idleThreadTimeout                   1 minute                                         The amount of time a worker thread can be idle before
                                                                                     being stopped.
nofileSoftLimit                     (none)                                           The number of open file descriptors before a soft error is issued.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
nofileHardLimit                     (none)                                           The number of open file descriptors before a hard error is issued.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
gid                                 (none)                                           The group ID to switch to once the connectors have started.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
uid                                 (none)                                           The user ID to switch to once the connectors have started.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
user                                (none)                                           The username to switch to once the connectors have started.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
group                               (none)                                           The group to switch to once the connectors have started.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
umask                               (none)                                           The umask to switch to once the connectors have started.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
startsAsRoot                        (none)                                           Whether or not the Dropwizard application is started as a root user.
                                                                                     Requires Jetty's ``libsetuid.so`` on ``java.library.path``.
shutdownGracePeriod                 30 seconds                                       The maximum time to wait for Jetty, and all Managed instances,
                                                                                     to cleanly shutdown before forcibly terminating them.
allowedMethods                      ``GET``, ``POST``, ``PUT``, ``DELETE``,          The set of allowed HTTP methods. Others will be rejected with a
                                    ``HEAD``, ``OPTIONS``, ``PATCH``                 405 Method Not Allowed response.
rootPath                            ``/*``                                           The URL pattern relative to ``applicationContextPath`` from which
                                                                                     the JAX-RS resources will be served.
registerDefaultExceptionMappers     true                                             Whether or not the default Jersey ExceptionMappers should be registered.
                                                                                     Set this to false if you want to register your own.
enableThreadNameFilter              true                                             Whether or not to apply the ``ThreadNameFilter`` that adjusts thread names to include the request method and request URI.
dumpAfterStart                      false                                            Whether or not to dump `Jetty Diagnostics`_ after start.
dumpBeforeStop                      false                                            Whether or not to dump `Jetty Diagnostics`_ before stop.
=================================== ===============================================  =============================================================================

.. _Jetty Diagnostics: https://www.eclipse.org/jetty/documentation/9.4.x/jetty-dump-tool.html

.. _man-configuration-gzip:

GZip
....

.. code-block:: yaml

    server:
      gzip:
        bufferSize: 8KiB


+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
|     Name                  | Default             | Description                                                                                          |
+===========================+=====================+======================================================================================================+
| enabled                   | true                | If true, all requests with ``gzip`` in the ``Accept-Encoding`` header will have their                |
|                           |                     | response entities compressed and requests with ``gzip`` in the ``Content-Encoding``                  |
|                           |                     | header will have their request entities decompressed.                                                |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| minimumEntitySize         | 256 bytes           | All response entities under this size are not compressed.                                            |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| bufferSize                | 8KiB                | The size of the buffer to use when compressing.                                                      |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| excludedUserAgentPatterns | []                  | The set of user agent patterns to exclude from compression.                                          |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| compressedMimeTypes       | Jetty's default     | The list of mime types to compress. The default is all types apart                                   |
|                           |                     | the commonly known image, video, audio and compressed types.                                         |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| includedMethods           | Jetty's default     | The list of HTTP methods to compress. The default is to compress only GET responses.                 |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| deflateCompressionLevel   | -1                  | The compression level used for deflation(compression).                                               |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| gzipCompatibleInflation   | true                | This option is unused and deprecated as compressed requests without header info are unsupported      |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+
| syncFlush                 | false               | The flush mode. Set to true if the application wishes to stream (e.g. SSE) the data,                 |
|                           |                     | but this may hurt compression performance (as all pending output is flushed).                        |
+---------------------------+---------------------+------------------------------------------------------------------------------------------------------+

.. _man-configuration-requestLog:

Request Log
...........

The new request log uses the `logback-access`_ library for processing request logs, which allow to use an extended set
of logging patterns. See the `logback-access-pattern`_ docs for the reference.

.. code-block:: yaml

    server:
      requestLog:
        appenders:
          - type: console

.. _logback-access: http://logback.qos.ch/access.html
.. _logback-access-pattern: http://logback.qos.ch/manual/layouts.html#AccessPatternLayout

====================== ================ ======================================================================
Name                   Default          Description
====================== ================ ======================================================================
appenders              console appender The set of AppenderFactory appenders to which requests will be logged.
                                        See :ref:`logging <man-configuration-logging>` for more info.
====================== ================ ======================================================================


Classic Request Log
...................

The classic request log uses the `logback-classic`_ library for processing request logs. It produces logs only in the
standard `NCSA common log format`_, but allows to use an extended set of appenders.

.. code-block:: yaml

    server:
      requestLog:
        type: classic
        timeZone: UTC
        appenders:
          - type: console

.. _logback-classic: http://logback.qos.ch/
.. _NCSA common log format: https://en.wikipedia.org/wiki/Common_Log_Format

====================== ================ ======================================================================
Name                   Default          Description
====================== ================ ======================================================================
timeZone               UTC              The time zone to which request timestamps will be converted.
appenders              console appender The set of AppenderFactory appenders to which requests will be logged.
                                        See :ref:`logging <man-configuration-logging>` for more info.
====================== ================ ======================================================================

.. _man-configuration-server-push:

Server Push
...........

Server push technology allows a server to send additional resources to a client along with the requested resource.
It works only for HTTP/2 connections.

.. code-block:: yaml

    server:
      serverPush:
        enabled: true
        associatePeriod: '4 seconds'
        maxAssociations: 16
        refererHosts: ['dropwizard.io', 'dropwizard.github.io']
        refererPorts: [8444, 8445]


+-----------------+------------+------------------------------------------------------------------------------------------------------+
|     Name        | Default    | Description                                                                                          |
+=================+============+======================================================================================================+
| enabled         | false      | If true, the filter will organize resources as primary resources (those referenced by the            |
|                 |            | ``Referer`` header) and secondary resources (those that have the ``Referer`` header). Secondary      |
|                 |            | resources that have been requested within a time window from the request of the primary resource     |
|                 |            | will be associated with it. The next time a client requests the primary resource, the server will    |
|                 |            | send to the client the secondary resources along with the primary in a single response.              |
+-----------------+------------+------------------------------------------------------------------------------------------------------+
| associatePeriod | 4 seconds  | The time window within which a request for a secondary resource will be associated to a              |
|                 |            | primary resource.                                                                                    |
+-----------------+------------+------------------------------------------------------------------------------------------------------+
| maxAssociations | 16         | The maximum number of secondary resources that may be associated to a primary resource.              |
+-----------------+------------+------------------------------------------------------------------------------------------------------+
| refererHosts    | All hosts  | The list of referrer hosts for which the server push technology is supported.                        |
+-----------------+------------+------------------------------------------------------------------------------------------------------+
| refererPorts    | All ports  | The list of referrer ports for which the server push technology is supported.                        |
+-----------------+------------+------------------------------------------------------------------------------------------------------+


.. _man-configuration-simple:

Simple
------

Extends the attributes that are available to :ref:`all servers <man-configuration-all>`

.. code-block:: yaml

    server:
      type: simple
      applicationContextPath: /application
      adminContextPath: /admin
      connector:
        type: http
        port: 8080



========================  ===============   =====================================================================
Name                      Default           Description
========================  ===============   =====================================================================
connector                 http connector    HttpConnectorFactory HTTP connector listening on port 8080.
                                            The ConnectorFactory connector which will handle both application
                                            and admin requests. TODO link to connector below.
applicationContextPath    /application      The context path of the application servlets, including Jersey.
adminContextPath          /admin            The context path of the admin servlets, including metrics and tasks.
========================  ===============   =====================================================================


.. _man-configuration-default:

Default
-------

Extends the attributes that are available to :ref:`all servers <man-configuration-all>`

.. code-block:: yaml

    server:
      adminMinThreads: 1
      adminMaxThreads: 64
      adminContextPath: /
      applicationContextPath: /
      applicationConnectors:
        - type: http
          port: 8080
        - type: https
          port: 8443
          keyStorePath: example.keystore
          keyStorePassword: example
          validateCerts: false
      adminConnectors:
        - type: http
          port: 8081
        - type: https
          port: 8444
          keyStorePath: example.keystore
          keyStorePassword: example
          validateCerts: false


========================  =======================   =====================================================================
Name                      Default                   Description
========================  =======================   =====================================================================
applicationConnectors     An `HTTP connector`_      A set of :ref:`connectors <man-configuration-connectors>` which will
                          listening on port 8080.   handle application requests.
adminConnectors           An `HTTP connector`_      An `HTTP connector`_ listening on port 8081.
                          listening on port 8081.   A set of :ref:`connectors <man-configuration-connectors>` which will
                                                    handle admin requests.
adminMinThreads           1                         The minimum number of threads to use for admin requests.
adminMaxThreads           64                        The maximum number of threads to use for admin requests.
adminContextPath          /                         The context path of the admin servlets, including metrics and tasks.
applicationContextPath    /                         The context path of the application servlets, including Jersey.
========================  =======================   =====================================================================

.. _`HTTP connector`:  https://github.com/dropwizard/dropwizard/blob/master/dropwizard-jetty/src/main/java/io/dropwizard/jetty/HttpConnectorFactory.java

.. _man-configuration-connectors:

Connectors
==========


.. _man-configuration-http:

HTTP
----

.. code-block:: yaml

    # Extending from the default server configuration
    server:
      applicationConnectors:
        - type: http
          port: 8080
          bindHost: 127.0.0.1 # only bind to loopback
          inheritChannel: false
          headerCacheSize: 512 bytes
          outputBufferSize: 32KiB
          maxRequestHeaderSize: 8KiB
          maxResponseHeaderSize: 8KiB
          inputBufferSize: 8KiB
          idleTimeout: 30 seconds
          minBufferPoolSize: 64 bytes
          bufferPoolIncrement: 1KiB
          maxBufferPoolSize: 64KiB
          minRequestDataPerSecond: '0 bytes'
          minResponseDataPerSecond: '0 bytes'
          acceptorThreads: 1
          selectorThreads: 2
          acceptQueueSize: 1024
          reuseAddress: true
          useServerHeader: false
          useDateHeader: true
          useForwardedHeaders: false
          useProxyProtocol: false
          httpCompliance: RFC7230


======================== ==================  ======================================================================================
Name                     Default             Description
======================== ==================  ======================================================================================
port                     8080                The TCP/IP port on which to listen for incoming connections.
bindHost                 (none)              The hostname to bind to.
inheritChannel           false               Whether this connector uses a channel inherited from the JVM.
                                             Use it with `Server::Starter`_, to launch an instance of Jetty on demand.
headerCacheSize          512 bytes           The size of the header field cache.
outputBufferSize         32KiB               The size of the buffer into which response content is aggregated before being sent to
                                             the client. A larger buffer can improve performance by allowing a content producer
                                             to run without blocking, however larger buffers consume more memory and may induce
                                             some latency before a client starts processing the content.
maxRequestHeaderSize     8KiB                The maximum allowed size in bytes for the HTTP request line and HTTP request headers.
                                             Larger headers will allow for more and/or larger cookies plus larger form content
                                             encoded in a URL. However, larger headers consume more memory and can make a server
                                             more vulnerable to denial of service attacks.
maxResponseHeaderSize    8KiB                The maximum size of a response header. Larger headers will allow for more and/or
                                             larger cookies and longer HTTP headers (eg for redirection).  However, larger headers
                                             will also consume more memory.
inputBufferSize          8KiB                The size of the per-connection input buffer.
idleTimeout              30 seconds          The maximum idle time for a connection, which roughly translates to the
                                             `java.net.Socket#setSoTimeout(int)`_ call, although with NIO implementations
                                             other mechanisms may be used to implement the timeout.
                                             The max idle time is applied when waiting for a new message to be received on a connection
                                             or when waiting for a new message to be sent on a connection.
                                             This value is interpreted as the maximum time between some progress being made on the
                                             connection. So if a single byte is read or written, then the timeout is reset.
minBufferPoolSize        64 bytes            The minimum size of the buffer pool.
bufferPoolIncrement      1KiB                The increment by which the buffer pool should be increased.
maxBufferPoolSize        64KiB               The maximum size of the buffer pool.
minRequestDataPerSecond       0                   The minimum request data rate in bytes per second; or <= 0 for no limit.
minResponseDataPerSecond      0                   The minimum response data rate in bytes per second; or <= 0 for no limit.
acceptorThreads          (Jetty's default)   The number of worker threads dedicated to accepting connections.
                                             By default is *max(1, min(4, #CPUs/8))*.
selectorThreads          (Jetty's default)   The number of worker threads dedicated to sending and receiving data.
                                             By default is *max(1, min(4, #CPUs/2))*.
acceptQueueSize          (OS default)        The size of the TCP/IP accept queue for the listening socket.
reuseAddress             true                Whether or not ``SO_REUSEADDR`` is enabled on the listening socket.
useServerHeader          false               Whether or not to add the ``Server`` header to each response.
useDateHeader            true                Whether or not to add the ``Date`` header to each response.
useForwardedHeaders      false               Whether or not to look at ``X-Forwarded-*`` headers added by proxies. See
                                             `ForwardedRequestCustomizer`_ for details.
useProxyProtocol         false               Whether or not to accept ``PROXY`` protocol requests from a reverse proxy such as `HAProxy`_.
                                             `ProxyConnectionFactory`_ supports version 1 and 2 of the ``PROXY`` protocol.
httpCompliance           RFC7230             This sets the http compliance level used by Jetty when parsing http, this
                                             can be useful when using a non-RFC7230 compliant front end, such as nginx,
                                             which can produce multi-line headers when forwarding client certificates
                                             using ``proxy_set_header X-SSL-CERT $ssl_client_cert;``
                                             Possible values are set forth in the ``org.eclipse.jetty.http.HttpCompliance``
                                             enum:

                                             * RFC7230: Disallow header folding.
                                             * RFC2616: Allow header folding.
requestCookieCompliance  RFC6265             This sets the cookie compliance level used by Jetty when parsing request ``Cookie``
                                             headers, this can be useful when needing to support Version=1 cookies defined in
                                             RFC2109 (and continued in RFC2965) which allows for special/reserved characters
                                             (control, separator, et al) to be enclosed within double quotes when used in a
                                             cookie value;
                                             Possible values are set forth in the ``org.eclipse.jetty.http.CookieCompliance``
                                             enum:

                                             * RFC6265: Special characters in cookie values must be encoded.
                                             * RFC2965: Allows for special characters enclosed within double quotes.
responseCookieCompliance RFC6265             This sets the cookie compliance level used by Jetty when generating response
                                             ``Set-Cookie`` headers, this can be useful when needing to support Version=1 cookies
                                             defined in RFC2109 (and continued in RFC2965) which allows for special/reserved
                                             characters (control, separator, et al) to be enclosed within double quotes when used
                                             in a cookie value;
                                             Possible values are set forth in the ``org.eclipse.jetty.http.CookieCompliance``
                                             enum:

                                             * RFC6265: Special characters in cookie values must be encoded.
                                             * RFC2965: Allows for special characters enclosed within double quotes.
======================== ==================  ======================================================================================

.. _`java.net.Socket#setSoTimeout(int)`: https://docs.oracle.com/javase/8/docs/api/java/net/Socket.html#setSoTimeout-int-
.. _`ForwardedRequestCustomizer`: https://www.eclipse.org/jetty/javadoc/jetty-9/org/eclipse/jetty/server/ForwardedRequestCustomizer.html
.. _`ProxyConnectionFactory`: https://www.eclipse.org/jetty/javadoc/jetty-9/org/eclipse/jetty/server/ProxyConnectionFactory.html
.. _`HAProxy`: https://www.haproxy.org/

.. _`Server::Starter`:  https://github.com/kazuho/p5-Server-Starter

.. _man-configuration-https:

HTTPS
-----

Extends the attributes that are available to the :ref:`HTTP connector <man-configuration-http>`

.. code-block:: yaml

    # Extending from the default server configuration
    server:
      applicationConnectors:
        - type: https
          port: 8443
          ....
          keyStorePath: /path/to/file
          keyStorePassword: changeit
          keyStoreType: JKS
          keyStoreProvider:
          trustStorePath: /path/to/file
          trustStorePassword: changeit
          trustStoreType: JKS
          trustStoreProvider:
          keyManagerPassword: changeit
          needClientAuth: false
          wantClientAuth:
          certAlias: <alias>
          crlPath: /path/to/file
          enableCRLDP: false
          enableOCSP: false
          maxCertPathLength: (unlimited)
          ocspResponderUrl: (none)
          jceProvider: (none)
          validateCerts: false
          validatePeers: false
          supportedProtocols: (JVM default)
          excludedProtocols: [SSL, SSLv2, SSLv2Hello, SSLv3] # (Jetty's default)
          supportedCipherSuites: (JVM default)
          excludedCipherSuites: [.*_(MD5|SHA|SHA1)$] # (Jetty's default)
          allowRenegotiation: true
          endpointIdentificationAlgorithm: (none)
          disableSniHostCheck: false

================================ ================================ ======================================================================================
Name                             Default                          Description
================================ ================================ ======================================================================================
keyStorePath                     REQUIRED                         The path to the Java key store which contains the host certificate and private key.
keyStorePassword                 REQUIRED                         The password used to access the key store.
keyStoreType                     JKS                              The type of key store (usually ``JKS``, ``PKCS12``, ``JCEKS``,
                                                                  ``Windows-MY``}, or ``Windows-ROOT``).
keyStoreProvider                 (none)                           The JCE provider to use to access the key store.
trustStorePath                   (none)                           The path to the Java key store which contains the CA certificates used to establish
                                                                  trust.
trustStorePassword               (none)                           The password used to access the trust store.
trustStoreType                   JKS                              The type of trust store (usually ``JKS``, ``PKCS12``, ``JCEKS``,
                                                                  ``Windows-MY``, or ``Windows-ROOT``).
trustStoreProvider               (none)                           The JCE provider to use to access the trust store.
keyManagerPassword               (none)                           The password, if any, for the key manager.
needClientAuth                   (none)                           Whether or not client authentication is required.
wantClientAuth                   (none)                           Whether or not client authentication is requested.
certAlias                        (none)                           The alias of the certificate to use.
crlPath                          (none)                           The path to the file which contains the Certificate Revocation List.
enableCRLDP                      false                            Whether or not CRL Distribution Points (CRLDP) support is enabled.
enableOCSP                       false                            Whether or not On-Line Certificate Status Protocol (OCSP) support is enabled.
maxCertPathLength                (unlimited)                      The maximum certification path length.
ocspResponderUrl                 (none)                           The location of the OCSP responder.
jceProvider                      (none)                           The name of the JCE provider to use for cryptographic support. See `Oracle documentation <https://docs.oracle.com/javase/8/docs/technotes/guides/security/SunProviders.html>`_ for more information.
validateCerts                    false                            Whether or not to validate TLS certificates before starting. If enabled, Dropwizard
                                                                  will refuse to start with expired or otherwise invalid certificates. This option will
                                                                  cause unconditional failure in Dropwizard 1.x until a new validation mechanism can be
                                                                  implemented.
validatePeers                    false                            Whether or not to validate TLS peer certificates. This option will
                                                                  cause unconditional failure in Dropwizard 1.x until a new validation mechanism can be
                                                                  implemented.
supportedProtocols               (none)                           A list of protocols (e.g., ``SSLv3``, ``TLSv1``) which are supported. All
                                                                  other protocols will be refused.
excludedProtocols                ["SSL.*", "TLSv1", "TLSv1\\.1"]  A list of protocols (e.g., ``SSLv3``, ``TLSv1``) which are excluded. These
                                                                  protocols will be refused.
supportedCipherSuites            (none)                           A list of cipher suites (e.g., ``TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256``) which
                                                                  are supported. All other cipher suites will be refused.
excludedCipherSuites             (none)                           A list of cipher suites (e.g., ``TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256``) which
                                                                  are excluded. These cipher suites will be refused and exclusion takes higher
                                                                  precedence than inclusion, such that if a cipher suite is listed in
                                                                  ``supportedCipherSuites`` and ``excludedCipherSuites``, the cipher suite will be
                                                                  excluded. To verify that the proper cipher suites are being whitelisted and
                                                                  blacklisted, it is recommended to use the tool `sslyze`_.
allowRenegotiation               true                             Whether or not TLS renegotiation is allowed.
endpointIdentificationAlgorithm  (none)                           Which endpoint identification algorithm, if any, to use during the TLS handshake.
disableSniHostCheck              false                            Whether to disable server-side SNI host checking.
================================ ================================ ======================================================================================

.. _sslyze: https://github.com/nabla-c0d3/sslyze

.. _man-configuration-http2:

HTTP/2 over TLS
---------------

HTTP/2 is a new protocol, intended as a successor of HTTP/1.1. It adds several important features
like binary structure, stream multiplexing over a single connection, header compression, and server push.
At the same time it remains semantically compatible with HTTP/1.1, which should make the upgrade process more
seamless. Checkout HTTP/2 FAQ__ for the further information.

.. __: https://http2.github.io/faq/

For an encrypted connection HTTP/2 uses ALPN protocol. It's a TLS extension, that allows a client to negotiate
a protocol to use after the handshake is complete. If either side does not support ALPN, then the protocol will
be ignored, and an HTTP/1.1 connection over TLS will be used instead.

For this connector to work with ALPN protocol you need to either:

* Enable native SSL support via Google's Conscrypt as described in the :ref:`SSL section <man-core-ssl>` of the
  Core manual; or
* Provide alpn-boot library to JVM's bootpath. The correct library version depends on the JVM version.
  Consult Jetty ALPN guide__ for the reference.

.. __: http://www.eclipse.org/jetty/documentation/current/alpn-chapter.html

Note that your JVM also must provide ``TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`` cipher. The specification states__
that HTTP/2 deployments must support it to avoid handshake failures. It's the single supported cipher in HTTP/2
connector by default. In case you want to support more strong ciphers, you should specify them in the
``supportedCipherSuites`` parameter along with ``TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256``.

.. __: http://http2.github.io/http2-spec/index.html#rfc.section.9.2.2

This connector extends the attributes that are available to the :ref:`HTTPS connector <man-configuration-https>`

.. code-block:: yaml

    server:
      applicationConnectors:
        - type: h2
          port: 8445
          maxConcurrentStreams: 1024
          initialStreamRecvWindow: 65535
          keyStorePath: /path/to/file # required
          keyStorePassword: changeit
          trustStorePath: /path/to/file # required
          trustStorePassword: changeit
          supportedCipherSuites: # optional
            - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
            - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256


========================  ========  ===================================================================================
Name                      Default   Description
========================  ========  ===================================================================================
maxConcurrentStreams      1024      The maximum number of concurrently open streams allowed on a single HTTP/2
                                    connection. Larger values increase parallelism, but cost a memory commitment.
initialStreamRecvWindow   65535     The initial flow control window size for a new stream. Larger values may allow
                                    greater throughput, but also risk head of line blocking if TCP/IP flow control is
                                    triggered.
========================  ========  ===================================================================================

.. _man-configuration-http2c:

HTTP/2 Plain Text
-----------------

HTTP/2 promotes using encryption, but doesn't require it. However, most browsers stated that they will
not support HTTP/2 without encryption. Currently no browser supports HTTP/2 unencrypted.

The connector should only be used in closed secured networks or during development. It expects from clients
an HTTP/1.1 OPTIONS request with ``Upgrade : h2c`` header to indicate a wish to upgrade to HTTP/2, or a request with
the HTTP/2 connection preface. If the client doesn't support HTTP/2, a plain HTTP/1.1 connections will be used instead.

This connector extends the attributes that are available to the :ref:`HTTP connector <man-configuration-http>`

.. code-block:: yaml

    server:
      applicationConnectors:
        - type: h2c
          port: 8446
          maxConcurrentStreams: 1024
          initialStreamRecvWindow: 65535


========================  ========  ===================================================================================
Name                      Default   Description
========================  ========  ===================================================================================
maxConcurrentStreams      1024      The maximum number of concurrently open streams allowed on a single HTTP/2
                                    connection. Larger values increase parallelism, but cost a memory commitment.
initialStreamRecvWindow   65535     The initial flow control window size for a new stream. Larger values may allow
                                    greater throughput, but also risk head of line blocking if TCP/IP flow control is
                                    triggered.
========================  ========  ===================================================================================

.. _man-configuration-tasks:

Tasks
=====

.. code-block:: yaml

    admin:
      tasks:
        printStackTraceOnError: true


====================== ======= ===============================================================
Name                   Default Description
====================== ======= ===============================================================
printStackTraceOnError false   Print the full stack trace when the execution of a task failed.
====================== ======= ===============================================================

.. _man-configuration-healthchecks:

Health checks
=============

.. code-block:: yaml

    admin:
      healthChecks:
        servletEnabled: true
        minThreads: 1
        maxThreads: 4
        workQueueSize: 1


============== ======= ==========================================================
Name           Default Default Description
============== ======= ==========================================================
servletEnabled true    Whether to enable or disable the health check servlet.
minThreads     1       The minimum number of threads for executing health checks.
maxThreads     4       The maximum number of threads for executing health checks.
workQueueSize  1       The length of the work queue for health check executions.
============== ======= ==========================================================

.. _man-configuration-logging:

Logging
=======

.. code-block:: yaml

    logging:
      level: INFO
      loggers:
        "io.dropwizard": INFO
        "org.hibernate.SQL":
          level: DEBUG
          additive: false
          appenders:
            - type: file
              currentLogFilename: /var/log/myapplication-sql.log
              archivedLogFilenamePattern: /var/log/myapplication-sql-%d.log.gz
              archivedFileCount: 5
      appenders:
        - type: console


====================== ===========  ============================================================
Name                   Default      Description
====================== ===========  ============================================================
level                  Level.INFO   Logback logging level.
additive               true         Logback additive setting.
loggers                (none)       Individual logger configuration (both forms are acceptable).
appenders              (none)       One of console, file or syslog.
====================== ===========  ============================================================


.. _man-configuration-logging-console:

Console
-------

.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: console
          threshold: ALL
          queueSize: 512
          discardingThreshold: 0
          timeZone: UTC
          target: stdout
          logFormat: "%-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx"
          filterFactories:
            - type: URI


====================== =======================================  ===========
Name                   Default                                  Description
====================== =======================================  ===========
type                   REQUIRED                                 The appender type. Must be ``console``.
threshold              ALL                                      The lowest level of events to print to the console.
queueSize              256                                      The maximum capacity of the blocking queue.
discardingThreshold    -1                                       When the blocking queue has only the capacity mentioned in
                                                                discardingThreshold remaining, it will drop events of level TRACE,
                                                                DEBUG and INFO, keeping only events of level WARN and ERROR.
                                                                If no discarding threshold is specified (-1), then a default of
                                                                queueSize / 5 (logback's default ratio) is used.
                                                                To keep all events, set discardingThreshold to 0.
timeZone               UTC                                      The time zone to which event timestamps will be converted.
                                                                To use the system/default time zone, set it to ``system``.
target                 stdout                                   The name of the standard stream to which events will be written.
                                                                Can be ``stdout`` or ``stderr``.
logFormat              %-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx    The Logback pattern with which events will be formatted. See
                                                                the Logback_ documentation for details.
                                                                The default log pattern is ```%h %l %u [%t{dd/MMM/yyyy:HH:mm:ss Z,UTC}] "%r" %s %b "%i{Referer}" "%i{User-Agent}" %D```.
                                                                Use the placeholder ``%dwTimeZone`` to include the value of
                                                                ``timeZone`` in the pattern.
filterFactories        (none)                                   The list of filters to apply to the appender, in order, after
                                                                the threshold.
neverBlock             false                                    Prevent the wrapping asynchronous appender from blocking when its underlying queue is full.
                                                                Set to true to disable blocking.
====================== =======================================  ===========

.. _Logback: http://logback.qos.ch/manual/layouts.html#conversionWord


.. _man-configuration-logging-file:

File
----

.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: file
          currentLogFilename: /var/log/myapplication.log
          threshold: ALL
          queueSize: 512
          discardingThreshold: 0
          archive: true
          archivedLogFilenamePattern: /var/log/myapplication-%d.log
          archivedFileCount: 5
          timeZone: UTC
          logFormat: "%-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx"
          bufferSize: 8KiB
          immediateFlush: true
          filterFactories:
            - type: URI


============================ =========================================  ==================================================================================================
Name                         Default                                    Description
============================ =========================================  ==================================================================================================
type                         REQUIRED                                   The appender type. Must be ``file``.
currentLogFilename           REQUIRED                                   The filename where current events are logged.
threshold                    ALL                                        The lowest level of events to write to the file.
queueSize                    256                                        The maximum capacity of the blocking queue.
discardingThreshold          -1                                         When the blocking queue has only the capacity mentioned in discardingThreshold
                                                                        remaining, it will drop events of level TRACE, DEBUG and INFO, keeping only events
                                                                        of level WARN and ERROR. If no discarding threshold is specified (-1), then a default
                                                                        of queueSize / 5 (logback's default ratio) is used. To keep all events, set
                                                                        discardingThreshold to 0.
archive                      true                                       Whether or not to archive old events in separate files.
archivedLogFilenamePattern   (none)                                     Required if ``archive`` is ``true``.
                                                                        The filename pattern for archived files.
                                                                        If ``maxFileSize`` is specified, rollover is size-based, and the pattern must contain ``%i`` for
                                                                        an integer index of the archived file.
                                                                        Otherwise rollover is date-based, and the pattern must contain ``%d``, which is replaced with the
                                                                        date in ``yyyy-MM-dd`` form.
                                                                        If the pattern ends with ``.gz`` or ``.zip``, files will be compressed as they are archived.
archivedFileCount            5                                          The number of archived files to keep. Must be greater than or equal to ``0``. Zero is a
                                                                        special value signifying to keep infinite logs (use with caution)
maxFileSize                  (unlimited)                                The maximum size of the currently active file before a rollover is triggered. The value can be
                                                                        expressed in bytes, kibibytes, kilobytes, mebibytes, megabytes, gibibytes, gigabytes, tebibytes,
                                                                        terabytes, pebibytes, and petabytes by appending B, KiB, KB, MiB, MB, GiB, GB, TiB, TB, PiB, or PB
                                                                        to the numeric value.  Examples include 5KiB, 100MiB, 1GiB, 1TB.  Sizes can also be spelled out, such
                                                                        as 5 kibibytes, 100 mebibytes, 1 gibibyte, 1 terabyte.
totalSizeCap                 (unlimited)                                Controls the total size of all files.
                                                                        Oldest archives are deleted asynchronously when the total size cap is exceeded.
timeZone                     UTC                                        The time zone to which event timestamps will be converted.
logFormat                    %-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx      The Logback pattern with which events will be formatted. See
                                                                        the Logback_ documentation for details.
                                                                        The default log pattern is ```%h %l %u [%t{dd/MMM/yyyy:HH:mm:ss Z,UTC}] "%r" %s %b "%i{Referer}" "%i{User-Agent}" %D```.
                                                                        Use the placeholder ``%dwTimeZone`` to include the value of
                                                                        ``timeZone`` in the pattern.
filterFactories              (none)                                     The list of filters to apply to the appender, in order, after
                                                                        the threshold.
neverBlock                   false                                      Prevent the wrapping asynchronous appender from blocking when its underlying queue is full.
                                                                        Set to true to disable blocking.
bufferSize                   8KiB                                       The buffer size of the underlying FileAppender (setting added in logback 1.1.10). Increasing this
                                                                        from the default of 8KiB to 256KiB is reported to significantly reduce thread contention.
immediateFlush               true                                       If set to true, log events will be immediately flushed to disk. Immediate flushing is safer, but
                                                                        it degrades logging throughput.
============================ =========================================  ==================================================================================================


.. _man-configuration-logging-syslog:

Syslog
------

.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: syslog
          host: localhost
          port: 514
          facility: local0
          threshold: ALL
          stackTracePrefix: \t
          logFormat: "%-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx"
          filterFactories:
            - type: URI


============================ =====================================  ==================================================================================================
Name                         Default                                Description
============================ =====================================  ==================================================================================================
host                         localhost                              The hostname of the syslog server.
port                         514                                    The port on which the syslog server is listening.
facility                     local0                                 The syslog facility to use. Can be either ``auth``, ``authpriv``,
                                                                    ``daemon``, ``cron``, ``ftp``, ``lpr``, ``kern``, ``mail``,
                                                                    ``news``, ``syslog``, ``user``, ``uucp``, ``local0``,
                                                                    ``local1``, ``local2``, ``local3``, ``local4``, ``local5``,
                                                                    ``local6``, or ``local7``.
threshold                    ALL                                    The lowest level of events to write to the file.
logFormat                    %-5p [%d{ISO8601,UTC}] %c: %m%n%dwREx  The Logback pattern with which events will be formatted. See
                                                                    the Logback_ documentation for details.
                                                                    The default log pattern is ```%h %l %u [%t{dd/MMM/yyyy:HH:mm:ss Z,UTC}] "%r" %s %b "%i{Referer}" "%i{User-Agent}" %D```.
stackTracePrefix             \t                                     The prefix to use when writing stack trace lines (these are sent
                                                                    to the syslog server separately from the main message)
filterFactories              (none)                                 The list of filters to apply to the appender, in order, after
                                                                    the threshold.
neverBlock                   false                                  Prevent the wrapping asynchronous appender from blocking when its underlying queue is full.
                                                                    Set to ``true`` to disable blocking.
includeStackTrace            true                                   Set to ``false`` to disable sending stack traces to the syslog service.
============================ =====================================  ==================================================================================================


.. _man-configuration-logging-tcp:

TCP
------

.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: tcp
          host: localhost
          port: 4560
          connectionTimeout: 500ms
          immediateFlush: true
          sendBufferSize: 8KiB


============================ =============  ==================================================================
Name                         Default        Description
============================ =============  ==================================================================
host                         localhost      The hostname of the TCP server.
port                         4560           The port on which the TCP server is listening.
connectionTimeout            500ms          The timeout to connect to the TCP server.
immediateFlush               true           If set to true, log events will be immediately send to the server
                                            Immediate flushing is safer, but it degrades logging throughput.
sendBufferSize               8KiB           The buffer size of the underlying SocketAppender.
                                            Takes into effect if immediateFlush is disabled.
============================ =============  ==================================================================


.. _man-configuration-logging-udp:

UDP
------

.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: udp
          host: localhost
          port: 514


============================ =============  ==================================================================
Name                         Default        Description
============================ =============  ==================================================================
host                         localhost      The hostname of the UDP server.
port                         514            The port on which the UDP server is listening.
============================ =============  ==================================================================


.. _man-configuration-logging-filter-factories:

FilterFactories
---------------

A factory used for request logging appenders should implement ``io.dropwizard.logging.common.filter.FilterFactory<IAccessEvent>``
while one used for regular logging should implement ``io.dropwizard.logging.common.filter.FilterFactory<ILoggingEvent>``.
To register a factory, its fully qualified classname must be listed in
``META-INF/services/io.dropwizard.logging.common.filter.FilterFactory``. The factory then can be referenced in the configuration
either via its simple classname or via type name, if factory class annotated with ``@JsonTypeName``.


.. code-block:: yaml

    logging:
      level: INFO
      appenders:
        - type: console
          filterFactories:
            - type: URI


====================== ===========  =====================
Name                   Default      Description
====================== ===========  =====================
type                   REQUIRED     The filter type name.
====================== ===========  =====================

.. _man-configuration-json-layout:

JSON layout
-----------

.. code-block:: yaml

    layout:
      type: json
      timestampFormat: "yyyy-MM-dd'T'HH:mm:ss.SSSZ"
      prettyPrint: false
      appendLineSeparator: true
      includes: [timestamp, threadName, level, loggerName, message, mdc, exception]
      customFieldNames:
        timestamp: "@timestamp"
      additionalFields:
        service-name: "user-service"
      includesMdcKeys: [userId]
      flattenMdc: true
      exception:
        rootFirst: true
        depth: full
        evaluators: [org.apache]


=======================  =====================  ================
Name                     Default                Description
=======================  =====================  ================
timestampFormat          (none)                 By default, the timestamp is not formatted. To customize how timestamps are formatted,
                                                set the property to the corresponding DateTimeFormatter_ string or one of the
                                                predefined formats (e.g. ``ISO_LOCAL_TIME``, ``ISO_ZONED_DATE_TIME``, ``RFC_1123_DATE_TIME``).
prettyPrint              false                  Whether the JSON output should be formatted for human readability.
appendLineSeparator      true                   Whether to append a line separator at the end of the message formatted as JSON.
includes                 (timestamp, level,
                         threadName,  mdc,
                         loggerName, message,
                         exception)             Set of logging event attributes to include in the JSON map:

                                                - ``timestamp``   *true*   Whether to include the timestamp as the ``timestamp`` field.
                                                - ``level``       *true*   Whether to include the logging level as the ``level`` field.
                                                - ``threadName``  *true*   Whether to include the thread name as the ``thread`` field.
                                                - ``mdc``         *true*   Whether to include the MDC properties as the ``mdc`` field.
                                                - ``loggerName``  *true*   Whether to include the logger name as the ``logger`` field.
                                                - ``message``     *true*   Whether to include the formatted message as the ``message`` field.
                                                - ``exception``   *true*   Whether to log exceptions. If the property enabled and there is an exception, it will be formatted to a string as the ``exception`` field.
                                                - ``contextName`` *false*  Whether to include the logging context name as the ``context`` field .
customFieldNames         (empty)                Map of field name replacements . For example ``(requestTime:request_time, userAgent:user_agent)``.
additionalFields         (empty)                Map of fields to add in the JSON map.
includesMdcKeys          (empty)                Set of MDC keys which should be included in the JSON map. By default includes everything.
flattenMdc               false                  Flatten the MDC to the root of the JSON object instead of nested in the ``mdc`` field.
exception                (empty)                The :ref:`exception <man-configuration-json-layout-exception>` configuration for the ``exception`` field.
=======================  =====================  ================

.. _DateTimeFormatter:  https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html

.. _man-configuration-json-layout-exception:

Exception
.........

.. code-block:: yaml

    layout:
      type: json
      exception:
        rootFirst: false
        depth: 25
        evaluators: [org.apache]


====================== ===========  ================================
Name                   Default      Description
====================== ===========  ================================
rootFirst              true         Whether the root cause should be displayed first.
depth                  full         The stack trace depth_.
evaluators             (empty)      The packages to filter_ from the stacktrace.
====================== ===========  ================================

.. _depth:  https://logback.qos.ch/manual/layouts.html#ex
.. _filter:  https://github.com/qos-ch/logback/pull/244

.. _man-configuration-json-access-layout:

JSON access log layout
----------------------

.. code-block:: yaml

    layout:
      type: access-json
      timestampFormat: "yyyy-MM-dd'T'HH:mm:ss.SSSZ"
      prettyPrint: false
      appendLineSeparator: true
      includes: [timestamp, remoteAddress, remoteUser, protocol, method, requestUri, statusCode, requestTime, contentLength, userAgent]
      requestHeaders:
        - X-Request-Id
      responseHeaders:
        - X-Request-Id
      requestAttributes:
        - SomeAttributeName
      customFieldNames:
        timestamp: "@timestamp"
      additionalFields:
        service-name: "user-service"

=======================  ===========================  ================
Name                     Default                      Description
=======================  ===========================  ================
timestampFormat          (none)                       By default, the timestamp is not formatted. To customize how timestamps are formatted,
                                                      set the property to the corresponding DateTimeFormatter_ string or one of the predefined formats
                                                      (e.g. ``ISO_LOCAL_TIME``, ``ISO_ZONED_DATE_TIME``,``RFC_1123_DATE_TIME``).
prettyPrint              false                        Whether the JSON output should be formatted for human readability.
appendLineSeparator      true                         Whether to append a line separator at the end of the message formatted as JSON.
includes                 (timestamp, remoteAddress,
                         protocol, method,
                         requestUri, statusCode,
                         requestTime, contentLength,
                         userAgent)                   Set of logging event attributes to include in the JSON map:

                                                      - ``contentLength``     *true*     Whether to include the response content length, if it's known as the ``contentLength`` field.
                                                      - ``method``            *true*     Whether to include the request HTTP method as the ``method`` field.
                                                      - ``remoteAddress``     *true*     Whether to include the IP address of the client or last proxy that sent the request as the ``remoteAddress`` field.
                                                      - ``remoteUser``        *true*     Whether to include information about the remote user as the ``remoteUser`` field.
                                                      - ``requestTime``       *true*     Whether to include the time elapsed between receiving the request and logging it as the ``requestTime`` field. Time is in *ms*.
                                                      - ``requestUri``        *true*     Whether to include the URI of the request as the ``uri`` field.
                                                      - ``statusCode``        *true*     Whether to include the status code of the response as the ``status`` field.
                                                      - ``protocol``          *true*     Whether to include the request HTTP protocol as the ``protocol`` field.
                                                      - ``timestamp``         *true*     Whether to include the timestamp of the event the ``timestamp`` field.
                                                      - ``userAgent``         *true*     Whether to include the user agent of the request as the ``userAgent`` field.
                                                      - ``requestParameters`` *false*    Whether to include the request parameters as the ``params`` field.
                                                      - ``requestContent``    *false*    Whether to include the body of the request as the ``requestContent`` field. Must register_ the TeeFilter_ to be effective.
                                                      - ``requestUrl``        *false*    Whether to include the request URL (method, URI, query parameters, protocol) as the ``url`` field.
                                                      - ``pathQuery``         *false*    Whether to include the URI and query parameters of the request as the ``pathQuery`` field.
                                                      - ``remoteHost``        *false*    Whether to include the fully qualified name of the client or the last proxy that sent the request as the ``remoteHost`` field.
                                                      - ``responseContent``   *false*    Whether to include the response body as the ``responseContent`` field. Must register_ the TeeFilter_ to be effective.
                                                      - ``serverName``        *false*    Whether to include the name of the server to which the request was sent as the ``serverName`` field.
requestHeaders           (empty)                      Set of request headers included in the JSON map as the ``headers`` field.
responseHeaders          (empty)                      Set of response headers included in the JSON map as the ``responseHeaders`` field.
requestAttributes        (empty)                      Set of ServletRequest attributes included in the JSON map as the ``requestAttributes`` field.
customFieldNames         (empty)                      Map of field name replacements in the JSON map. For example ``requestTime:request_time, userAgent:user_agent)``.
additionalFields         (empty)                      Map of fields to add in the JSON map.
=======================  ===========================  ================

.. _DateTimeFormatter:  https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html

.. _TeeFilter: https://logback.qos.ch/access.html#teeFilter

.. _register: https://github.com/dropwizard/dropwizard/issues/2045#issuecomment-299149563

.. _man-configuration-metrics:

Metrics
=======

The metrics configuration has three fields; frequency, reporters and reportOnStop.

.. code-block:: yaml

    metrics:
      frequency: 1 minute
      reporters:
        - type: <type>
      reportOnStop: false


====================== ===========  ===========
Name                   Default      Description
====================== ===========  ===========
frequency              1 minute     The frequency to report metrics. Overridable per-reporter.
reporters              (none)       A list of reporters to report metrics.
reportOnStop           false        To report metrics one last time when stopping Dropwizard.
====================== ===========  ===========


.. _man-configuration-metrics-all:

All Reporters
-------------

The following options are available for all metrics reporters.

.. code-block:: yaml

    metrics:
      reporters:
        - type: <type>
          durationUnit: milliseconds
          rateUnit: seconds
          excludes: (none)
          includes: (all)
          excludesAttributes: (none)
          includesAttributes: (all)
          useRegexFilters: false
          frequency: 1 minute


====================== =============  ===========
Name                   Default        Description
====================== =============  ===========
durationUnit           milliseconds   The unit to report durations as. Overrides per-metric duration units.
rateUnit               seconds        The unit to report rates as. Overrides per-metric rate units.
excludes               (none)         Metrics to exclude from reports, by name. When defined, matching metrics will not be reported.
includes               (all)          Metrics to include in reports, by name. When defined, only these metrics will be reported.
excludesAttributes     (none)         Metric attributes to exclude from reports, by name (e.g. ``p98``, ``m15_rate``, ``stddev``).
                                      When defined, matching metrics attributes will not be reported.
includesAttributes     (all)          Metrics attributes to include in reports, by name (e.g. ``p98``, ``m15_rate``, ``stddev``).
                                      When defined, only these attributes will be reported.
useRegexFilters        false          Indicates whether the values of the 'includes' and 'excludes' fields should be treated as regular expressions or not.
useSubstringMatching   false          Uses a substring matching strategy to determine whether a metric should be processed.
frequency              (none)         The frequency to report metrics. Overrides the default.
====================== =============  ===========

The inclusion and exclusion rules are defined as:

* If **includes** is empty, then all metrics are included;
* If **includes** is not empty, only metrics from this list are included;
* If **excludes** is empty, no metrics are excluded;
* If **excludes** is not empty, then exclusion rules take precedence over inclusion rules. Thus if a name matches the exclusion rules it will not be included in reports even if it also matches the inclusion rules.

When neither **useRegexFilters** nor **useSubstringMatching** are enabled, a default exact matching strategy will be used to determine whether a metric should be processed.
In case both **useRegexFilters** and **useSubstringMatching** are set, **useRegexFilters** takes precedence over **useSubstringMatching**.

.. _man-configuration-metrics-formatted:

Formatted Reporters
...................

These options are available only to "formatted" reporters and extend the options available to :ref:`all reporters <man-configuration-metrics-all>`

.. code-block:: yaml

    metrics:
      reporters:
        - type: <type>
          locale: <system default>


====================== ===============  ===========
Name                   Default          Description
====================== ===============  ===========
locale                 System default   The Locale_ for formatting numbers, dates and times.
====================== ===============  ===========

.. _Locale: http://docs.oracle.com/javase/7/docs/api/java/util/Locale.html

.. _man-configuration-metrics-console:

Console Reporter
----------------

Reports metrics periodically to the console.

Extends the attributes that are available to :ref:`formatted reporters <man-configuration-metrics-formatted>`

.. code-block:: yaml

    metrics:
      reporters:
        - type: console
          timeZone: UTC
          output: stdout


====================== ===============  ===========
Name                   Default          Description
====================== ===============  ===========
timeZone               UTC              The timezone to display dates/times for.
output                 stdout           The stream to write to. One of ``stdout`` or ``stderr``.
====================== ===============  ===========


.. _man-configuration-metrics-csv:

CSV Reporter
------------

Reports metrics periodically to a CSV file.

Extends the attributes that are available to :ref:`formatted reporters <man-configuration-metrics-formatted>`

.. code-block:: yaml

    metrics:
      reporters:
        - type: csv
          file: /path/to/file


====================== ===============  ===========
Name                   Default          Description
====================== ===============  ===========
file                   No default       The CSV file to write metrics to.
====================== ===============  ===========


.. _man-configuration-metrics-graphite:

Graphite Reporter
-----------------

Reports metrics periodically to Graphite.

Extends the attributes that are available to :ref:`all reporters <man-configuration-metrics-all>`

.. note::

    You will need to add ``dropwizard-metrics-graphite`` to your POM.

.. code-block:: yaml

    metrics:
      reporters:
        - type: graphite
          host: localhost
          port: 2003
          prefix: <prefix>
          transport: tcp


====================== ===============  ====================================================================================================
Name                   Default          Description
====================== ===============  ====================================================================================================
host                   localhost        The hostname of the Graphite server to report to.
port                   2003             The port of the Graphite server to report to.
prefix                 (none)           The prefix for Metric key names to report to Graphite.
transport              tcp              The type of transport to report to Graphite with ("tcp" or "udp").
====================== ===============  ====================================================================================================


.. _man-configuration-metrics-slf4j:

SLF4J
-----

Reports metrics periodically by logging via SLF4J.

Extends the attributes that are available to :ref:`all reporters <man-configuration-metrics-all>`

See BaseReporterFactory_  and BaseFormattedReporterFactory_ for more options.

.. _BaseReporterFactory:  https://github.com/dropwizard/dropwizard/blob/master/dropwizard-metrics/src/main/java/io/dropwizard/metrics/BaseReporterFactory.java
.. _BaseFormattedReporterFactory: https://github.com/dropwizard/dropwizard/blob/master/dropwizard-metrics/src/main/java/io/dropwizard/metrics/BaseFormattedReporterFactory.java


.. code-block:: yaml

    metrics:
      reporters:
        - type: log
          logger: metrics
          markerName: <marker name>


====================== ===============  ====================================================================================================
Name                   Default          Description
====================== ===============  ====================================================================================================
logger                 metrics          The name of the logger to write metrics to.
markerName             (none)           The name of the marker to mark logged metrics with.
====================== ===============  ====================================================================================================


.. _man-configuration-health:

Health
=======

.. code-block:: yaml

    health:
      enabled: true
      delayedShutdownHandlerEnabled: true
      shutdownWaitPeriod: 5s
      healthCheckUrlPaths: ["/health-check"]
      healthChecks:
        - <some health check config>
        - <some other health check config>
      initialOverallState: false
      responseProvider:
        type: json
      responder:
        type: servlet


============================== =======================  ====================================================================================================
Name                           Default                  Description
============================== =======================  ====================================================================================================
enabled                        true                     Flag indicating whether to enable health functionality or not.
delayedShutdownHandlerEnabled  false                    Flag indicating whether to delay shutdown to allow already processing requests to complete.
shutdownWaitPeriod             15 seconds               Amount of time to delay shutdown by to allow already processing requests to complete. Only applicable if ``delayedShutdownHandlerEnabled`` is true.
healthCheckUrlPaths            \["/health-check"\]      URLs to expose the app's health check on.
healthChecks                   []                       A list of configured health checks. See the [Health Check Configuration section](#health-check-configuration) for more details.
initialOverallState            true                     Flag indicating whether the overall health state of the application should start as healthy or unhealthy. A value of ``true`` indicates an initial state of healthy while a value of ``false`` indicates an initial state of unhealthy.
responseProvider               json                     The health response provider that is used to respond to generate responses to return to health check requests. This can be implemented using Jersey, Jetty, or other technologies if desired. See the :ref:`detailed JSON health response provider section <man-configuration-health-responseprovider>` for more details.
responder                      servlet                  The health responder that is used to respond to health check requests. This can be implemented using Jersey, Jetty, or other technologies if desired. See the :ref:`servlet health responder section <man-configuration-health-responder>` for more details.
============================== =======================  ====================================================================================================


.. _man-configuration-health-checks:

Health Checks
--------------

Options around a particular health check which is registered in an Application

  .. code-block:: yaml

      health:
        healthChecks:
          - name: file-system
            type: alive
            critical: true
            initialState: true
          - name: database
            type: ready
            critical: false
            initialState: false


============================== =======================  ====================================================================================================
Name                           Default                  Description
============================== =======================  ====================================================================================================
name                           (none)                   The name of this health check. This must be unique, and match the name of the check registered in code. (On the application's ``HealthCheckRegistry``)
type                           ready                    The type of this health check. This is either ``alive`` or ``ready``. See the :ref:`application status section <man-core-health-status>` for more details.
critical                       false                    Flag indicating whether this dependency is critical to determine the health of the application. If ``true`` and this dependency is unhealthy, the application will also be marked as unhealthy.
initialState                   true                     Flag indicating the initial state to use for this health check. A value of ``true`` indicates an initial state of healthy while a value of ``false`` indicates an initial state of unhealthy.
schedule                       default schedule         The schedule that this health check will be run on. See the :ref:`schedule section <man-configuration-health-schedule>` for more details.
============================== =======================  ====================================================================================================


.. _man-configuration-health-schedule:

Schedule
-----------

The schedule on which to execute a particular :ref:`health checks <man-configuration-health-checks>`

.. code-block:: yaml

    health:
       healthChecks:
       - name: file-system
         schedule:
           checkInterval: 10s
           downtimeInterval: 2s
           initialDelay: 5s
           failureAttempts: 1
           successAttempts: 2


============================== ============================  ====================================================================================================
Name                           Default                       Description
============================== ============================  ====================================================================================================
checkInterval                  5 seconds                     The interval on which to perform a health check for this dependency while the dependency is in a healthy state.
downtimeInterval               30 seconds                    The interval on which to perform a health check for this dependency while the dependency is in an unhealthy state.
initialDelay                   the value of `checkInterval`  The initial delay to use when first scheduling the health check.
failureAttempts                3                             The threshold of consecutive failed attempts needed to mark a dependency as unhealthy (from a healthy state).
successAttempts                2                             The threshold of consecutive successful attempts needed to mark a dependency as healthy (from an unhealthy state).
============================== ============================  ====================================================================================================

.. _man-configuration-health-responseprovider:

Detailed JSON Health Response Provider
--------------------------------------

A detailed servlet used to handle health check requests, which returns a JSON response explaining the various
registered health checks, their current status, and other metadata.

.. code-block:: yaml

    health:
      responseProvider:
        type: json

============================== ============================  ====================================================================================================
Name                           Default                       Description
============================== ============================  ====================================================================================================
type                           json                 - json
============================== ============================  ====================================================================================================


.. _man-configuration-health-responder:

Servlet Health Responder
-------------------------

A servlet responder used to handle health check requests.

.. code-block:: yaml

    health:
      responder:
        type: servlet
        cacheControlEnabled: true
        cacheControlValue: "no-store"


============================== ============================  ====================================================================================================
Name                           Default                       Description
============================== ============================  ====================================================================================================
type                           servlet                       - servlet
cacheControlEnabled            true                          Flag controlling whether a ``Cache-Control`` header will be included in the health check response or not. Set header value using ``cacheControlValue``.
cacheControlValue              "no-store"                    The value to be set in the ``Cache-Control`` header in the health check response. Only used if ``cacheControlEnabled`` is set to ``true``.
============================== ============================  ====================================================================================================

.. _man-configuration-clients:

Clients
=======

.. _man-configuration-clients-http:

HttpClient
----------

See HttpClientConfiguration_  for more options.

.. _HttpClientConfiguration:  https://github.com/dropwizard/dropwizard/blob/master/dropwizard-client/src/main/java/io/dropwizard/client/HttpClientConfiguration.java

.. code-block:: yaml

    httpClient:
      timeout: 500ms
      connectionTimeout: 500ms
      timeToLive: 1h
      cookiesEnabled: false
      maxConnections: 1024
      maxConnectionsPerRoute: 1024
      keepAlive: 0ms
      retries: 0
      userAgent: <application name> (<client name>)


=============================  ======================================  =============================================================================
Name                           Default                                 Description
=============================  ======================================  =============================================================================
timeout                        500 milliseconds                        The maximum idle time for a connection, once established.
connectionTimeout              500 milliseconds                        The maximum time to wait for a connection to open.
connectionRequestTimeout       500 milliseconds                        The maximum time to wait for a connection to be returned from the connection pool.
timeToLive                     1 hour                                  The maximum time a pooled connection can stay idle (not leased to any thread)
                                                                       before it is shut down.
cookiesEnabled                 false                                   Whether or not to enable cookies.
maxConnections                 1024                                    The maximum number of concurrent open connections.
maxConnectionsPerRoute         1024                                    The maximum number of concurrent open connections per route.
keepAlive                      0 milliseconds                          The maximum time a connection will be kept alive before it is reconnected. If set
                                                                       to 0, connections will be immediately closed after every request/response.
retries                        0                                       The number of times to retry failed requests. Requests are only
                                                                       retried if they throw an exception other than ``InterruptedIOException``,
                                                                       ``UnknownHostException``, ``ConnectException``, or ``SSLException``.
userAgent                      ``applicationName`` (``clientName``)    The User-Agent to send with requests.
validateAfterInactivityPeriod  0 milliseconds                          The maximum time before a persistent connection is checked to remain active.
                                                                       If set to 0, no inactivity check will be performed.
=============================  ======================================  =============================================================================


.. _man-configuration-clients-http-proxy:

Proxy
.....

.. code-block:: yaml

    httpClient:
      proxy:
        host: 192.168.52.11
        port: 8080
        scheme : http
        auth:
          username: secret
          password: stuff
          authScheme: NTLM
          realm: realm
          hostname: host
          domain: WINDOWSDOMAIN
          credentialType: NT
        nonProxyHosts:
          - localhost
          - '192.168.52.*'
          - '*.example.com'


==============  =================  =====================================================================================
Name            Default            Description
==============  =================  =====================================================================================
host            REQUIRED           The proxy server host name or ip address.
port            (scheme default)   The proxy server port.
                                   If the port is not set then the scheme default port is used.
scheme          http               The proxy server URI scheme. HTTP and HTTPS schemas are permitted.
                                   By default HTTP scheme is used.
auth            (none)             The proxy server ``Basic`` or ``NTLM`` authentication schemes.
                                   If they are not set then no credentials will be passed to the server.
username        REQUIRED           The username used to connect to the server.
password        REQUIRED           The password used to connect to the server.
authScheme      Basic              The authentication scheme used by the. Allowed options are:
                                   ``Basic``, ``NTLM``
realm           (none)             The realm, used for NTLM authentication.
hostname        (none)             The hostname of the windows workstation, used for NTLM authentication.
domain          (none)             The Windows Domain, used for NTLM authentication.
credentialType  (none)             The Apache HTTP Client Credentials implementation used for proxy authentication.
                                   Allowed options are: ``UsernamePassword`` or ``NT``
nonProxyHosts   (none)             List of patterns of hosts that should be reached without proxy.
                                   The patterns may contain symbol '*' as a wildcard.
                                   If a host matches one of the patterns it will be reached through a direct connection.
==============  =================  =====================================================================================


.. _man-configuration-clients-http-tls:

TLS
.....

.. code-block:: yaml

    httpClient:
      tls:
        protocol: TLSv1.2
        provider: SunJSSE
        verifyHostname: true
        keyStorePath: /path/to/file
        keyStorePassword: changeit
        keyStoreType: JKS
        trustStorePath: /path/to/file
        trustStorePassword: changeit
        trustStoreType: JKS
        trustSelfSignedCertificates: false
        supportedProtocols: TLSv1.1,TLSv1.2
        supportedCipherSuites: TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
        certAlias: alias-of-specific-cert


===========================  =================  ============================================================================================================================
Name                         Default            Description
===========================  =================  ============================================================================================================================
protocol                     TLSv1.2            The default protocol the client will attempt to use during the SSL Handshake.
                                                See
                                                `here <http://docs.oracle.com/javase/8/docs/technotes/guides/security/StandardNames.html#SSLContext>`_ for more information.
provider                     (none)             The name of the JCE provider to use on client side for cryptographic support (for example, SunJCE, Conscrypt, BC, etc).
                                                See `Oracle documentation <https://docs.oracle.com/javase/8/docs/technotes/guides/security/SunProviders.html>`_ for more information.
verifyHostname               true               Whether to verify the hostname of the server against the hostname presented in the server certificate.
keyStorePath                 (none)             The path to the Java key store which contains the client certificate and private key.
keyStorePassword             (none)             The password used to access the key store.
keyStoreType                 JKS                The type of key store (usually ``JKS``, ``PKCS12``, ``JCEKS``, ``Windows-MY``, or ``Windows-ROOT``).
trustStorePath               (none)             The path to the Java key store which contains the CA certificates used to establish trust.
trustStorePassword           (none)             The password used to access the trust store.
trustStoreType               JKS                The type of trust store (usually ``JKS``, ``PKCS12``, ``JCEKS``, ``Windows-MY``, or ``Windows-ROOT``).
trustSelfSignedCertificates  false              If true, will trust all self-signed certificates regardless of trustStore settings.
                                                If false, trust decisions will be handled by the supplied trustStore.
supportedProtocols           (none)             A list of protocols (e.g., ``SSLv3``, ``TLSv1``) which are supported. All
                                                other protocols will be refused.
supportedCipherSuites        (none)             A list of cipher suites (e.g., ``TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256``) which
                                                are supported. All other cipher suites will be refused.
certAlias                    (none)             The alias of a specific client certificate to present when authenticating. Use this when
                                                the specified keystore has multiple certificates to force use of a non-default certificate.
===========================  =================  ============================================================================================================================


.. _man-configuration-clients-jersey:

JerseyClient
------------

Extends the attributes that are available to :ref:`http clients <man-configuration-clients-http>`

See JerseyClientConfiguration_ and HttpClientConfiguration_ for more options.

.. _JerseyClientConfiguration:  https://github.com/dropwizard/dropwizard/blob/master/dropwizard-client/src/main/java/io/dropwizard/client/JerseyClientConfiguration.java

.. code-block:: yaml

    jerseyClient:
      minThreads: 1
      maxThreads: 128
      workQueueSize: 8
      gzipEnabled: true
      gzipEnabledForRequests: true
      chunkedEncodingEnabled: true


======================= ==================  ===================================================================================================
Name                    Default             Description
======================= ==================  ===================================================================================================
minThreads              1                   The minimum number of threads in the pool used for asynchronous requests.
maxThreads              128                 The maximum number of threads in the pool used for asynchronous requests. If asynchronous requests made by jersey client while serving requests, the number must be set according to the `maxThread` setting of the :ref:`server <man-configuration-all>`. Otherwise some requests made to dropwizard on heavy load may fail due to congestion on the jersey client's thread pool.
workQueueSize           8                   The size of the work queue of the pool used for asynchronous requests.
                                            Additional threads will be spawn only if the queue is reached its maximum size.
gzipEnabled             true                Adds an Accept-Encoding: gzip header to all requests, and enables automatic gzip decoding of responses.
gzipEnabledForRequests  true                Adds a Content-Encoding: gzip header to all requests, and enables automatic gzip encoding of requests.
chunkedEncodingEnabled  true                Enables the use of chunked encoding for requests.
======================= ==================  ===================================================================================================


.. _man-configuration-database:

Database
========

.. code-block:: yaml

    database:
      driverClass : org.postgresql.Driver
      url: 'jdbc:postgresql://db.example.com/db-prod'
      user: pg-user
      password: iAMs00perSecrEET


============================    =====================    ===============================================================
Name                            Default                  Description
============================    =====================    ===============================================================
url                             REQUIRED                 The URL of the server.

driverClass                     none                     The fully qualified class name of the JDBC driver class.
                                                         Only required if there were no JDBC drivers registered in
                                                         ``META-INF/services/java.sql.Driver``.

user                            none                     The username used to connect to the server.

password                        none                     The password used to connect to the server.

removeAbandoned                 false                    Remove abandoned connections if they exceed
                                                         removeAbandonedTimeout. If set to true a connection is
                                                         considered abandoned and eligible for removal if it has been in
                                                         use longer than the removeAbandonedTimeout and the condition
                                                         for abandonWhenPercentageFull is met.

removeAbandonedTimeout          60 seconds               The time before a database connection can be considered
                                                         abandoned.

abandonWhenPercentageFull       0                        Connections that have been abandoned (timed out) won't get
                                                         closed and reported up unless the number of connections in use
                                                         are above the percentage defined by abandonWhenPercentageFull.
                                                         The value should be between 0-100.

alternateUsernamesAllowed       false                    Set to true if the call getConnection(username,password) is
                                                         allowed. This is used for when the pool is used by an
                                                         application  accessing multiple schemas. There is a
                                                         performance impact turning this option on, even when not used.

commitOnReturn                  false                    Set to true if you want the connection pool to commit any
                                                         pending transaction when a connection is returned.

rollbackOnReturn                false                    Set to true if you want the connection pool to rollback any
                                                         pending transaction when a connection is returned.


autoCommitByDefault             JDBC driver's default    The default auto-commit state of the connections.

readOnlyByDefault               JDBC driver's default    The default read-only state of the connections.

properties                      none                     Any additional JDBC driver parameters.

defaultCatalog                  none                     The default catalog to use for the connections.

defaultTransactionIsolation     JDBC driver's default    The default transaction isolation to use for the connections.
                                                         Can be one of none, default, read-uncommitted, read-committed,
                                                         repeatable-read, or serializable.

useFairQueue                    true                     If true, calls to getConnection are handled in a FIFO manner.

initialSize                     10                       The initial size of the connection pool.

minSize                         10                       The minimum size of the connection pool.

maxSize                         100                      The maximum size of the connection pool.

initializationQuery             none                     A custom query to be run when a connection is first created.

logAbandonedConnections         false                    If true, logs stack traces of abandoned connections.

logValidationErrors             false                    If true, logs errors when connections fail validation.

maxConnectionAge                none                     If set, connections which have been open for longer than
                                                         maxConnectionAge are closed when returned.

maxWaitForConnection            30 seconds               If a request for a connection is blocked for longer than this
                                                         period, an exception will be thrown.

minIdleTime                     1 minute                 The minimum amount of time an connection must sit idle in the
                                                         pool before it is eligible for eviction.

validationQuery                 SELECT 1                 The SQL query that will be used to validate connections from
                                                         this pool before returning them to the caller or pool.
                                                         If specified, this query does not have to return any data, it
                                                         just can't throw a SQLException.( FireBird will throw exception unless validationQuery set to **select 1 from rdb$database**)

validationQueryTimeout          none                     The timeout before a connection validation queries fail.

checkConnectionWhileIdle        true                     Set to true if query validation should take place while the
                                                         connection is idle.

checkConnectionOnBorrow         false                    Whether or not connections will be validated before being
                                                         borrowed from the pool. If the connection fails to validate,
                                                         it will be dropped from the pool, and another will be
                                                         borrowed.

checkConnectionOnConnect        false                    Whether or not connections will be validated before being
                                                         added to the pool. If the connection fails to validate,
                                                         it won't be added to the pool.

checkConnectionOnReturn         false                    Whether or not connections will be validated after being
                                                         returned to the pool. If the connection fails to validate, it
                                                         will be dropped from the pool.

autoCommentsEnabled             true                     Whether or not ORMs should automatically add comments.

evictionInterval                5 seconds                The amount of time to sleep between runs of the idle
                                                         connection validation, abandoned cleaner and idle pool
                                                         resizing.

validationInterval              30 seconds               To avoid excess validation, only run validation once every
                                                         interval.

validatorClassName              none                     Name of a class of a custom validator implementation, which
                                                         will be used for validating connections.
jdbcInterceptors                none                       A semicolon separated list of JDBC interceptor classnames.

ignoreExceptionOnPreLoad        false                    Flag whether ignore error of connection creation while
                                                         initializing the pool. Set to true if you want to ignore
                                                         error of connection creation while initializing the pool.
                                                         Set to false if you want to fail the initialization of the
                                                         pool by throwing exception.
============================    =====================    ===============================================================

.. _man-configuration-polymorphic:

Polymorphic configuration
=========================

.. rubric:: The ``dropwizard-configuration`` module provides you with a polymorphic configuration
            mechanism, meaning that a particular section of your configuration file can be implemented
            using one or more configuration classes.

To use this capability for your own configuration classes, create a top-level configuration interface or class that
implements ``Discoverable`` and add the name of that class to ``META-INF/services/io.dropwizard.jackson.Discoverable``.
Make sure to use `Jackson polymorphic deserialization`_ annotations appropriately.

.. _Jackson polymorphic deserialization: https://github.com/FasterXML/jackson-docs/wiki/JacksonPolymorphicDeserialization

.. code-block:: java

    @JsonTypeInfo(use = Id.NAME, include = As.PROPERTY, property = "type")
    interface WidgetFactory extends Discoverable {
        Widget createWidget();
    }

Then create subtypes of the top-level type corresponding to each alternative, and add their names to
``META-INF/services/WidgetFactory``.

.. code-block:: java

    @JsonTypeName("hammer")
    public class HammerFactory implements WidgetFactory {
        @JsonProperty
        private int weight = 10;

        @Override
        public Hammer createWidget() {
            return new Hammer(weight);
        }
    }

    @JsonTypeName("chisel")
    public class ChiselFactory implements WidgetFactory {
        @JsonProperty
        private float radius = 1;

        @Override
        public Chisel createWidget() {
            return new Chisel(radius);
        }
    }

Now you can use ``WidgetFactory`` objects in your application's configuration.

.. code-block:: java

    public class MyConfiguration extends Configuration {
        @JsonProperty
        @NotNull
        @Valid
        private List<WidgetFactory> widgets;
    }

.. code-block:: yaml

    widgets:
      - type: hammer
        weight: 20
      - type: chisel
        radius: 0.4

See :ref:`testing configurations <man-testing-configurations>` for details on ensuring the
configuration will be deserialized correctly.
