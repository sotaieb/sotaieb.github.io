---
title: "ASP.NET CORE-Web Application"
categories:
  - ASP.NET
tags:
  - ASP.NET
---

## Strict-Transport-Security

The HTTP Strict-Transport-Security response header lets a web site tell browsers that it should only be accessed using HTTPS, instead of using HTTP.

```sh
Strict-Transport-Security: max-age=<expire-time>
```

```csharp
app.UseHsts();
```

## HTTP referer

In HTTP, "Referer" is the name of an optional HTTP header field that identifies the address of the web page (i.e., the URI or IRI), which is linked to the resource being requested. By checking the referrer, the server providing the new web page can see where the request originated.

```csharp
 var header = httpContext.Request.GetTypedHeaders();
 Uri uriReferer = header.Referer;
```

## dotnet-cli

```sh
dotnet new webapp -o aspnetcoreapp
dotnet dev-certs https --trust
dotnet watch run
```
