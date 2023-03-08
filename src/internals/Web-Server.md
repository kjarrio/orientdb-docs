
# Web Server

|   |   |
|---|---|
|![](../images/warning.png)|Even though OrientDB Server is a regular Web Server, it is not recommended to expose it directly on the Internet or public networks. We suggest to always hide OrientDB server in a private network.|

Global settings can be set at JVM startup (`java ... -D<setting>="<value>"`) or in `orientdb-server-config.xml` file under "properties" XML tag.

## Avoid exposing OrientDB Server to a public network

By default, OrientDB listens to all the network interfaces (`0.0.0.0`). It's strongly suggested to not open OrientDB server on public networks. To force OrientDB to bind to only one network, please edit the file `config/orientdb-server-config.xml`.  Replace `"0.0.0.0` with `127.0.0.1` if you want only local clients to have access to the server or any other valid IP you want to publish OrientDB. This is the default configuration:

```xml
<listeners>
  <listener protocol="binary" socket="default" port-range="2424-2430" ip-address="0.0.0.0"/>
  <listener protocol="http" socket="default" port-range="2480-2490" ip-address="0.0.0.0">
</listeners>
```

To bind OrientDB server only to the local server, change it into:

```xml
<listeners>
  <listener protocol="binary" socket="default" port-range="2424-2430" ip-address="127.0.0.1"/>
  <listener protocol="http" socket="default" port-range="2480-2490" ip-address="127.0.0.1">
</listeners>
```

## Maximum content length

OrientDB by default allows a request content of maximum 1 MB. To change this limitation set the global configuration `network.http.maxLength` to the needed value.

## Charset

OrientDB uses UTF-8 as the default charset. To change it set the global configuration `network.http.charset`.

## JSONP

JSONP is supported by OrientDB Web Server, but disabled by default. To enable it set the coniguration `network.http.jsonp=true`

This is a global setting, so you can set it at JVM startup (`java ... -Dnetwork.http.jsonp=true`) or by setting it as property in `orientdb-server-config.xml` file under "properties" XML tag.

## Cross Site
Cross site requests are disabled by default.

To enable it, set a couple of additional headers in `orientdb-server-config.xml` under the HTTP listener XML tag:

```xml
<listener protocol="http" ip-address="0.0.0.0" port-range="2480-2490" socket="default">
  <parameters>
    <parameter name="network.http.additionalResponseHeaders" value="Access-Control-Allow-Origin: *;Access-Control-Allow-Credentials: true" />
  </parameters>
</listener>
```

This setting is also global, so you can set it at JVM startup (`java ... -Dnetwork.http.additionalResponseHeaders="Access-Control-Allow-Origin: *;Access-Control-Allow-Credentials: true"`) or by setting it as property in `orientdb-server-config.xml` file under "properties" XML tag.


## Clickjacking

Look also: [Clickjacking on WikiPedia](https://en.wikipedia.org/wiki/Clickjacking) and  [Clickjacking on OWASP](https://www.owasp.org/index.php/Clickjacking)

You can disable clickjacking in OrientDB by setting the additional header `X-FRAME-OPTIONS` to `DENY` in all the HTTP responses. 

To enable it, set a couple of additional headers in `orientdb-server-config.xml` under the HTTP listener XML tag:

```xml
<listener protocol="http" ip-address="0.0.0.0" port-range="2480-2490" socket="default">
  <parameters>
    <parameter name="network.http.additionalResponseHeaders" value="X-FRAME-OPTIONS: DENY" />
  </parameters>
</listener>
```

This setting is also global, so you can set it at JVM startup (`java ... -Dnetwork.http.additionalResponseHeaders="X-FRAME-OPTIONS: DENY"`) or by setting it as property in `orientdb-server-config.xml` file under "properties" XML tag.
