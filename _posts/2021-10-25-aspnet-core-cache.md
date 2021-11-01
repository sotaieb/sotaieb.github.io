---
title: "ASP.NET CORE Cache"
categories:
  - ASP.NET
tags:
  - ASP.NET
---

Install Package
Microsoft.Extensions.Caching.SqlServer

```sh
CREATE TABLE [dbo].[Cache](
[Id] [nvarchar](449) NOT NULL,
[Value] [varbinary](max) NOT NULL,
[ExpiresAtTime] [datetimeoffset](7) NOT NULL,
[SlidingExpirationInSeconds] [bigint] NULL,
[AbsoluteExpiration] [datetimeoffset](7) NULL,
PRIMARY KEY CLUSTERED
(
[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
```

```sh
services.AddDistributedSqlServerCache(options =>
{
options.ConnectionString =
\_config["DistCache_ConnectionString"];
options.SchemaName = "dbo";
options.TableName = "TestCache";
});

nuget StackExchange.Redis
Microsoft.Extensions.Caching.StackExchangeRedis

services.AddStackExchangeRedisCache(options =>
{
options.Configuration = \_config["MyRedisConStr"];
options.InstanceName = "SampleInstance";
});
```
