![Logo](websocket-sharp_logo.png)

## Welcome to websocket-sharp! ##

**websocket-sharp** supports:

- **[RFC 6455](#supported-websocket-specifications)**
- **[WebSocket Client](#websocket-client)** and **[Server](#websocket-server)**
- **[Per-message Compression](#per-message-compression)** extension
- **[Secure Connection](#secure-connection)**
- **[HTTP Authentication](#http-authentication)**
- **[Query String, Origin header and Cookies](#query-string-origin-header-and-cookies)**
- **[Connecting through the HTTP Proxy server](#connecting-through-the-http-proxy-server)**
- .NET **3.5** or later (includes compatible)

## Branches ##

- **[master]** for production releases.
- **[hybi-00]** for older [draft-ietf-hybi-thewebsocketprotocol-00]. No longer maintained.
- **[draft75]** for even more old [draft-hixie-thewebsocketprotocol-75]. No longer maintained.

## Build ##

websocket-sharp is built as a single assembly, **websocket-sharp.dll**.

websocket-sharp is developed with **[MonoDevelop]**. So the simple way to build is to open **websocket-sharp.sln** and run build for the **websocket-sharp project** with any of the build configurations (e.g. `Debug`) in the MonoDevelop.

## Install ##

### Self Build ###

You should add **websocket-sharp.dll** (e.g. `/path/to/websocket-sharp/bin/Debug/websocket-sharp.dll`) built yourself to the library references of your project.

If you would like to use that websocket-sharp.dll in your **[Unity]** project, you should add that dll to any folder of your project (e.g. `Assets/Plugins`) in the **Unity Editor**.

### NuGet Gallery ###

websocket-sharp is available on the **[NuGet Gallery]**, as still a **prerelease** version.

- **[NuGet Gallery: websocket-sharp]**

You can add websocket-sharp to your project using the **NuGet Package Manager**, the following command in the **Package Manager Console**.

    PM> Install-Package WebSocketSharp -Pre

### Unity Asset Store ###

websocket-sharp is available on the **Unity Asset Store**.

- **[WebSocket-Sharp for Unity]**

It works with **Unity Free**, but there are some limitations:

- **[Security Sandbox of the Webplayer]** (server doesn't work in the webplayer)
- **[.NET Socket Support for iOS/Android][Unity Licenses Comparison]** (requires iOS/Android Pro)
- **.NET API 2.0 compatibility level for iOS/Android**

Using **.NET API 2.0 compatibility level for iOS/Android** requires to fix lack of some features for later than .NET 2.0, such as the `System.Func<...>` delegates (so i've fixed it in the asset package).

And it's priced at **US$15**. I think your $15 makes this project more better and accelerated, **Thank you!**

## Usage ##

### WebSocket Client ###

```cs
using System;
using WebSocketSharp;

namespace Example
{
  public class Program
  {
    public static void Main (string[] args)
    {
      using (var ws = new WebSocket ("ws://dragonsnest.far/Laputa")) {
        ws.OnMessage += (sender, e) =>
          Console.WriteLine ("Laputa says: " + e.Data);

        ws.Connect ();
        ws.Send ("BALUS");
        Console.ReadKey (true);
      }
    }
  }
}
```

#### Step 1 ####

Required namespace.

```cs
using WebSocketSharp;
```

The `WebSocket` class exists in the `WebSocketSharp` namespace.

#### Step 2 ####

Creating an instance of the `WebSocket` class with the WebSocket URL to connect.

```cs
using (var ws = new WebSocket ("ws://example.com")) {
  ...
}
```

The `WebSocket` class inherits the `System.IDisposable` interface, so you can use the `using` statement.

#### Step 3 ####

Setting the `WebSocket` events.

##### WebSocket.OnOpen Event #####

A `WebSocket.OnOpen` event occurs when the WebSocket connection has been established.

```cs
ws.OnOpen += (sender, e) => {
  ...
};
```

`e` has passed as the `System.EventArgs.Empty`, so you don't use `e`.

##### WebSocket.OnMessage Event #####

A `WebSocket.OnMessage` event occurs when the `WebSocket` receives a message.

```cs
ws.OnMessage += (sender, e) => {
  ...
};
```

`e` has passed as a `WebSocketSharp.MessageEventArgs`.

`e.Type` property returns either `WebSocketSharp.Opcode.Text` or `WebSocketSharp.Opcode.Binary` that represents the type of the message. So by checking it, you can determine which item you should use.

If it returns `Opcode.Text`, you should use `e.Data` property that returns a `string` (represents the **Text** message).

Or if it returns `Opcode.Binary`, you should use `e.RawData` property that returns a `byte[]` (represents the **Binary** message).

```cs
if (e.Type == Opcode.Text) {
  // Do something with e.Data
  ...

  return;
}

if (e.Type == Opcode.Binary) {
  // Do something with e.RawData
  ...

  return;
}
```

##### WebSocket.OnError Event #####

A `WebSocket.OnError` event occurs when the `WebSocket` gets an error.

```cs
ws.OnError += (sender, e) => {
  ...
};
```

`e` has passed as a `WebSocketSharp.ErrorEventArgs`.

`e.Message` property returns a `string` that represents the error message. So you should use it to get the error message.

And if the error is due to an exception, you can get the `System.Exception` instance that caused the error, by using `e.Exception` property.

##### WebSocket.OnClose Event #####

A `WebSocket.OnClose` event occurs when the WebSocket connection has been closed.

```cs
ws.OnClose += (sender, e) => {
  ...
};
```

`e` has passed as a `WebSocketSharp.CloseEventArgs`.

`e.Code` property returns a `ushort` that represents the status code indicating the reason for the close, and `e.Reason` property returns a `string` that represents the reason for the close. So you should use them to get the reason for the close.

#### Step 4 ####

Connecting to the WebSocket server.

```cs
ws.Connect ();
```

If you would like to connect to the server asynchronously, you should use the `WebSocket.ConnectAsync ()` method.

#### Step 5 ####

Sending a data to the WebSocket server.

```cs
ws.Send (data);
```

The `WebSocket.Send` method is overloaded.

You can use the `WebSocket.Send (string)`, `WebSocket.Send (byte[])`, or `WebSocket.Send (System.IO.FileInfo)` method to send a data.

If you would like to send a data asynchronously, you should use the `WebSocket.SendAsync` method.

```cs
ws.SendAsync (data, completed);
```

And also if you would like to do something when the send is complete, you should set `completed` to any `Action<bool>` delegate.

#### Step 6 ####

Closing the WebSocket connection.

```cs
ws.Close (code, reason);
```

If you would like to close the connection explicitly, you should use the `WebSocket.Close` method.

The `WebSocket.Close` method is overloaded.

You can use the `WebSocket.Close ()`, `WebSocket.Close (ushort)`, `WebSocket.Close (WebSocketSharp.CloseStatusCode)`, `WebSocket.Close (ushort, string)`, or `WebSocket.Close (WebSocketSharp.CloseStatusCode, string)` method to close the connection.

If you would like to close the connection asynchronously, you should use the `WebSocket.CloseAsync` method.

### WebSocket Server ###

```cs
using System;
using WebSocketSharp;
using WebSocketSharp.Server;

namespace Example
{
  public class Laputa : WebSocketService
  {
    protected override void OnMessage (MessageEventArgs e)
    {
      var msg = e.Data == "BALUS"
                ? "I've been balused already..."
                : "I'm not available now.";

      Send (msg);
    }
  }

  public class Program
  {
    public static void Main (string[] args)
    {
      var wssv = new WebSocketServer ("ws://dragonsnest.far");
      wssv.AddWebSocketService<Laputa> ("/Laputa");
      wssv.Start ();
      Console.ReadKey (true);
      wssv.Stop ();
    }
  }
}
```

#### Step 1 ####

Required namespace.

```cs
using WebSocketSharp.Server;
```

The `WebSocketServer` and `WebSocketService` classes exist in the `WebSocketSharp.Server` namespace.

#### Step 2 ####

Creating the class that inherits the `WebSocketService` class.

For example, if you would like to provide an echo service,

```cs
using System;
using WebSocketSharp;
using WebSocketSharp.Server;

public class Echo : WebSocketService
{
  protected override void OnMessage (MessageEventArgs e)
  {
    Send (e.Data);
  }
}
```

And if you would like to provide a chat service,

```cs
using System;
using WebSocketSharp;
using WebSocketSharp.Server;

public class Chat : WebSocketService
{
  private string _suffix;

  public Chat ()
    : this (null)
  {
  }

  public Chat (string suffix)
  {
    _suffix = suffix ?? String.Empty;
  }

  protected override void OnMessage (MessageEventArgs e)
  {
    Sessions.Broadcast (e.Data + _suffix);
  }
}
```

If you override the `WebSocketService.OnMessage (MessageEventArgs)` method, it's called when the `OnMessage` event of the `WebSocket` used in the current session in the WebSocket service occurs.

And if you override the `WebSocketService.OnOpen ()`, `WebSocketService.OnError (ErrorEventArgs)`, and `WebSocketService.OnClose (CloseEventArgs)` methods, each of them is called when each event of the `WebSocket` (the `OnOpen`, `OnError`, and `OnClose` events) occurs.

The `WebSocketService.Send` method sends a data to the client on the current session in the WebSocket service.

If you would like to access the sessions in the WebSocket service, you should use the `WebSocketService.Sessions` property (returns a `WebSocketSharp.Server.WebSocketSessionManager`).

The `WebSocketService.Sessions.Broadcast` method broadcasts a data to every client in the WebSocket service.

#### Step 3 ####

Creating an instance of the `WebSocketServer` class.

```cs
var wssv = new WebSocketServer (4649);
wssv.AddWebSocketService<Echo> ("/Echo");
wssv.AddWebSocketService<Chat> ("/Chat");
wssv.AddWebSocketService<Chat> ("/ChatWithNyan", () => new Chat (" Nyan!"));
```

You can add any WebSocket service to your `WebSocketServer` with the specified path to the service, using the `WebSocketServer.AddWebSocketService<TWithNew> (string)` or `WebSocketServer.AddWebSocketService<T> (string, Func<T>)` method.

The type of `TWithNew` must inherit the `WebSocketService` class and must have a public parameterless constructor.

And also the type of `T` must inherit the `WebSocketService` class.

So you can use the classes created in **Step 2** to add the WebSocket service.

If you create an instance of the `WebSocketServer` class without a port number, the `WebSocketServer` set the port number to **80** automatically. So it's necessary to run with root permission.

    $ sudo mono example2.exe

#### Step 4 ####

Starting the WebSocket server.

```cs
wssv.Start ();
```

#### Step 5 ####

Stopping the WebSocket server.

```cs
wssv.Stop (code, reason);
```

The `WebSocketServer.Stop` method is overloaded.

You can use the `WebSocketServer.Stop ()`, `WebSocketServer.Stop (ushort, string)`, or `WebSocketServer.Stop (WebSocketSharp.CloseStatusCode, string)` method to stop the server.

### HTTP Server with the WebSocket ###

I modified the `System.Net.HttpListener`, `System.Net.HttpListenerContext`, and some other classes of **[Mono]** to create the HTTP server that allows to accept the WebSocket connection requests.

So websocket-sharp provides the `WebSocketSharp.Server.HttpServer` class.

You can add any WebSocket service to your `HttpServer` with the specified path to the service, using the `HttpServer.AddWebSocketService<TWithNew> (string)` or `HttpServer.AddWebSocketService<T> (string, Func<T>)` method.

```cs
var httpsv = new HttpServer (4649);
httpsv.AddWebSocketService<Echo> ("/Echo");
httpsv.AddWebSocketService<Chat> ("/Chat");
httpsv.AddWebSocketService<Chat> ("/ChatWithNyan", () => new Chat (" Nyan!"));
```

For more information, would you see **[Example3]**?

### WebSocket Extensions ###

#### Per-message Compression ####

websocket-sharp supports the **[Per-message Compression][compression]** extension. (But it doesn't support with the [extension parameters].)

If you would like to enable this extension as a WebSocket client, you should set like the following.

```cs
ws.Compression = CompressionMethod.Deflate;
```

And then your client sends the following header with the connection request to the server.

    Sec-WebSocket-Extensions: permessage-deflate

If the server supports this extension, it returns the same header. And when your client receives that header, it enables this extension.

### Secure Connection ###

websocket-sharp supports the **Secure Connection (SSL)**.

As a **WebSocket Client**, you should create an instance of the `WebSocket` class with the **wss** scheme WebSocket URL.

```cs
using (var ws = new WebSocket ("wss://example.com")) {
  ...
}
```

And if you would like to set the custom validation for the server certificate, you should set the `WebSocket.ServerCertificateValidationCallback` property.

```cs
ws.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => {
  // Do something to validate the server certificate.
  ...

  return true; // If the server certificate is valid.
};
```

If you set this property to nothing, the validation does nothing with the server certificate and returns `true`.

As a **WebSocket Server**, you should create an instance of the `WebSocketServer` or `HttpServer` class with some settings for the secure connection, like the following.

```cs
var wssv = new WebSocketServer (4649, true);
wssv.Certificate = new X509Certificate2 ("/path/to/cert.pfx", "password for cert.pfx");
```

### HTTP Authentication ###

websocket-sharp supports the **[HTTP Authentication (Basic/Digest)][rfc2617]**.

As a **WebSocket Client**, you should set a pair of user name and password for the HTTP authentication, using the `WebSocket.SetCredentials (string, string, bool)` method before connecting.

```cs
ws.SetCredentials ("nobita", "password", preAuth);
```

If `preAuth` is `true`, the `WebSocket` sends the Basic authentication credentials with the first connection request to the server.

Or if `preAuth` is `false`, the `WebSocket` sends either the Basic or Digest authentication (determined by the unauthorized response to the first connection request) credentials with the second connection request to the server.

As a **WebSocket Server**, you should set an HTTP authentication scheme, a realm, and any function to find the user credentials before starting, like the following.

```cs
wssv.AuthenticationSchemes = AuthenticationSchemes.Basic;
wssv.Realm = "WebSocket Test";
wssv.UserCredentialsFinder = id => {
  var expected = "nobita";
  return id.Name == expected
         ? new NetworkCredential (expected, "password", "gunfighter") // User name, password, and roles
         : null; // If the user credentials not found.
};
```

If you would like to provide the Digest authentication, you should set like the following.

```cs
wssv.AuthenticationSchemes = AuthenticationSchemes.Digest;
```

### Query String, Origin header and Cookies ###

As a **WebSocket Client**, if you would like to send the **Query String** with the WebSocket connection request to the server, you should create an instance of the `WebSocket` class with the WebSocket URL that includes the [Query] string parameters.

```cs
using (var ws = new WebSocket ("ws://example.com/?name=nobita")) {
  ...
}
```

And if you would like to send the **Origin header** with the WebSocket connection request to the server, you should set the `WebSocket.Origin` property to an allowable value as the [Origin header] before connecting, like the following.

```cs
ws.Origin = "http://example.com";
```

And if you would like to send the **Cookies** with the WebSocket connection request to the server, you should set any cookie using the `WebSocket.SetCookie (WebSocketSharp.Net.Cookie)` method before connecting, like the following.

```cs
ws.SetCookie (new Cookie ("name", "nobita"));
```

As a **WebSocket Server**, if you would like to get the **Query String** included in each WebSocket connection request, you should access the `WebSocketService.Context.QueryString` property, like the following.

```cs
public class Chat : WebSocketService
{
  private string _name;
  ...

  protected override void OnOpen ()
  {
    _name = Context.QueryString["name"];
  }

  ...
}
```

And if you would like to check the **Origin header and Cookies** included in each WebSocket connection request, you should set each validation for the Origin header and Cookies in your `WebSocketService`, for example, using the `AddWebSocketService<T> (string, Func<T>)` method with initializing, like the following.

```cs
wssv.AddWebSocketService<Chat> (
  "/Chat",
  () => new Chat () {
    OriginValidator = val => {
      // Check the value of the Origin header, and return true if valid
      Uri origin;
      return !val.IsNullOrEmpty () &&
             Uri.TryCreate (val, UriKind.Absolute, out origin) &&
             origin.Host == "example.com";
    },
    CookiesValidator = (req, res) => {
      // Check the Cookies in 'req', and set the Cookies to send to the client with 'res' if necessary
      foreach (Cookie cookie in req) {
        cookie.Expired = true;
        res.Add (cookie);
      }

      return true; // If valid
    }
  });
```

Also, if you would like to get each value of the Origin header and cookies, you should access each of the `WebSocketService.Context.Origin` and `WebSocketService.Context.CookieCollection` properties.

### Connecting through the HTTP Proxy server ###

websocket-sharp supports to connect through the **HTTP Proxy** server.

If you would like to connect to a WebSocket server through the HTTP Proxy server, you should set the proxy server URL, and if necessary, a pair of user name and password for the proxy server authentication (Basic/Digest), using the `WebSocket.SetProxy (string, string, string)` method before connecting.

```cs
var ws = new WebSocket ("ws://example.com");
ws.SetProxy ("http://localhost:3128", "nobita", "password");
```

I tested this with the [Squid]. And it's necessary to disable the following configuration option in **squid.conf** (e.g. `/etc/squid/squid.conf`).

```
# Deny CONNECT to other than SSL ports
#http_access deny CONNECT !SSL_ports
```

### Logging ###

The `WebSocket` class includes the own logging function.

You can access it with the `WebSocket.Log` property (returns a `WebSocketSharp.Logger`).

So if you would like to change the current logging level (`WebSocketSharp.LogLevel.Error` as the default), you should set the `WebSocket.Log.Level` property to any of the `LogLevel` enum values.

```cs
ws.Log.Level = LogLevel.Debug;
```

The above means a log with lower than `LogLevel.Debug` cannot be outputted.

And if you would like to output a log, you should use any of the output methods. The following outputs a log with `LogLevel.Debug`.

```cs
ws.Log.Debug ("This is a debug message.");
```

The `WebSocketServer` and `HttpServer` classes include the same logging function.

## Examples ##

Examples using websocket-sharp.

### Example ###

**[Example]** connects to the **[Echo server]** with the WebSocket.

### Example1 ###

**[Example1]** connects to the **[Audio Data delivery server]** with the WebSocket. (But it's only implemented the chat feature, still unfinished.)

And Example1 uses **[Json.NET]**.

### Example2 ###

**[Example2]** starts a WebSocket server.

### Example3 ###

**[Example3]** starts an HTTP server that allows to accept the WebSocket connection requests.

Would you access to [http://localhost:4649](http://localhost:4649) to do **WebSocket Echo Test** with your web browser after Example3 running?

## Supported WebSocket Specifications ##

websocket-sharp supports **[RFC 6455][rfc6455]**, and it's based on the following WebSocket references:

- **[The WebSocket Protocol][rfc6455]**
- **[The WebSocket API][api]**
- **[Compression Extensions for WebSocket][compression]**

Thanks for translating to japanese.

- **[The WebSocket Protocol 日本語訳][rfc6455_ja]**
- **[The WebSocket API 日本語訳][api_ja]**

## License ##

websocket-sharp is provided under **[The MIT License]**.


[Audio Data delivery server]: http://agektmr.node-ninja.com:3000
[Echo server]: http://www.websocket.org/echo.html
[Example]: https://github.com/sta/websocket-sharp/tree/master/Example
[Example1]: https://github.com/sta/websocket-sharp/tree/master/Example1
[Example2]: https://github.com/sta/websocket-sharp/tree/master/Example2
[Example3]: https://github.com/sta/websocket-sharp/tree/master/Example3
[Json.NET]: http://james.newtonking.com/projects/json-net.aspx
[Mono]: http://www.mono-project.com
[MonoDevelop]: http://monodevelop.com
[NuGet Gallery]: http://www.nuget.org
[NuGet Gallery: websocket-sharp]: http://www.nuget.org/packages/WebSocketSharp
[Origin header]: http://tools.ietf.org/html/rfc6454#section-7
[Query]: http://tools.ietf.org/html/rfc3986#section-3.4
[Security Sandbox of the Webplayer]: http://docs.unity3d.com/Manual/SecuritySandbox.html
[Squid]: http://www.squid-cache.org
[The MIT License]: https://raw.github.com/sta/websocket-sharp/master/LICENSE.txt
[Unity]: http://unity3d.com
[Unity Licenses Comparison]: http://unity3d.com/unity/licenses
[WebSocket-Sharp for Unity]: http://u3d.as/content/sta-blockhead/websocket-sharp-for-unity
[api]: http://www.w3.org/TR/websockets
[api_ja]: http://www.hcn.zaq.ne.jp/___/WEB/WebSocket-ja.html
[compression]: http://tools.ietf.org/html/draft-ietf-hybi-permessage-compression-09
[draft-hixie-thewebsocketprotocol-75]: http://tools.ietf.org/html/draft-hixie-thewebsocketprotocol-75
[draft-ietf-hybi-thewebsocketprotocol-00]: http://tools.ietf.org/html/draft-ietf-hybi-thewebsocketprotocol-00
[draft75]: https://github.com/sta/websocket-sharp/tree/draft75
[extension parameters]: http://tools.ietf.org/html/draft-ietf-hybi-permessage-compression-09#section-8.1
[hybi-00]: https://github.com/sta/websocket-sharp/tree/hybi-00
[master]: https://github.com/sta/websocket-sharp/tree/master
[rfc2617]: http://tools.ietf.org/html/rfc2617
[rfc6455]: http://tools.ietf.org/html/rfc6455
[rfc6455_ja]: http://www.hcn.zaq.ne.jp/___/WEB/RFC6455-ja.html
