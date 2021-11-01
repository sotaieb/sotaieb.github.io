---
title: "ASP.NET CORE Fundamentals"
categories:
  - ASP.NET
tags:
  - ASP.NET
---

## Middleware

Middleware is software that's assembled into an app pipeline to handle requests and responses.

Request delegates are used to build the request pipeline. The request delegates handle each HTTP request.

Request delegates are configured using Run, Map, and Use extension methods.

Add a terminal middleware delegate to the application's request pipeline.

```csharp
 app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello, World!");
        });
```

Chain multiple request delegates together with Use/UseWhen.

```csharp
app.Use(async (context, next) =>
        {
            // Do work that doesn't write to the Response.
            await next.Invoke();
            // Do logging or other work that doesn't write to the Response.
        });
```

The primary difference between UseWhen and MapWhen is how later (i.e. registered below) middleware is executed. Unlike MapWhen, UseWhen continues to execute later middleware regardless of whether the UseWhen predicate was true or false.

```csharp
app.UseWhen(context => context.Request.Path.StartsWithSegments("/api"), appBuilder =>
{
    appBuilder.UseStatusCodePagesWithReExecute("/apierror/{0}");

    appBuilder.UseExceptionHandler("/apierror/500");
});
```

Map extensions are used as a convention for branching the pipeline.
Map branches the request pipeline based on matches of the given request path. If the request path starts with the given path, the branch is executed.

```csharp
private static void HandleMapTest1(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Map Test 1");
        });
    }

    private static void HandleMapTest2(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            await context.Response.WriteAsync("Map Test 2");
        });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.Map("/map1", HandleMapTest1);

        app.Map("/map2", HandleMapTest2);

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from non-Map delegate. <p>");
        });
    }
```

MapWhen branches the request pipeline based on the result of the given predicate.

```csharp
private static void HandleBranch(IApplicationBuilder app)
    {
        app.Run(async context =>
        {
            var branchVer = context.Request.Query["branch"];
            await context.Response.WriteAsync($"Branch used = {branchVer}");
        });
    }

    public void Configure(IApplicationBuilder app)
    {
        app.MapWhen(context => context.Request.Query.ContainsKey("branch"),
                               HandleBranch);

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from non-Map delegate. <p>");
        });
    }



```

The middleware class must include:

A public constructor with a parameter of type RequestDelegate.
A public method named Invoke or InvokeAsync. This method must:
Return a Task.
Accept a first parameter of type HttpContext.

```csharp
public class RequestCultureMiddleware
    {
        private readonly RequestDelegate _next;

        public RequestCultureMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            var cultureQuery = context.Request.Query["culture"];
            if (!string.IsNullOrWhiteSpace(cultureQuery))
            {
                var culture = new CultureInfo(cultureQuery);

                CultureInfo.CurrentCulture = culture;
                CultureInfo.CurrentUICulture = culture;

            }

            // Call the next delegate/middleware in the pipeline
            await _next(context);
        }
    }

//***********

public static class RequestCultureMiddlewareExtensions
    {
        public static IApplicationBuilder UseRequestCulture(
            this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<RequestCultureMiddleware>();
        }
    }
//***********
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.UseRequestCulture();

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync(
                $"Hello {CultureInfo.CurrentCulture.DisplayName}");
        });
    }
}

```
