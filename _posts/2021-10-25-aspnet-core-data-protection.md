---
title: "ASP.NET CORE-Data Protection/Secret Manager"
categories:
  - ASP.NET
tags:
  - ASP.NET
  - DataProtection
---

## Data Protection

The ASP.NET Core data protection stack is a replacement for the "machineKey".
Goal: I need to persist trusted information for later retrieval.

Microsoft.AspNetCore.DataProtection contains the core implementation of the data protection system, including core cryptographic operations, key management, configuration, and extensibility.

Basically, protecting data consists of the following steps:

Create a data protector from a data protection provider.
Call the Protect method with the data you want to protect.
Call the Unprotect method with the data you want to turn back into plain text.

```csharp
var serviceCollection = new ServiceCollection();
        serviceCollection.AddDataProtection();
var services = serviceCollection.BuildServiceProvider();

public MyService(IDataProtectionProvider provider)
{
    _protector = provider.CreateProtector("prtectorname");
}

string protectedPayload = _protector.Protect(input);
string unprotectedPayload = _protector.Unprotect(protectedPayload);

```

## Secret Manager

The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.

```sh
%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json
~/.microsoft/usersecrets/<user_secrets_id>/secrets.json
```

In the preceding file paths, replace <user_secrets_id> with the UserSecretsId value specified in the project file.

Enable secret storage:
dotnet user-secrets init

The preceding command adds a UserSecretsId element within a PropertyGroup of the project file.

```xml
<PropertyGroup>
  <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
</PropertyGroup>
```

Set a secret

```sh
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

Access a secret

```csharp
\_moviesApiKey = Configuration["Movies:ServiceApiKey"];
```

User secrets with connection strings:

```csharp
{
"ConnectionStrings": {
"Movies": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;User Id=johndoe;MultipleActiveResultSets=true"
}
}

var builder = new SqlConnectionStringBuilder(
Configuration.GetConnectionString("Movies"));
builder.Password = Configuration["DbPassword"];
\_connection = builder.ConnectionString;
```
