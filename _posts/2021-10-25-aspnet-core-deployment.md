---
title: "ASP.NET CORE Deployment"
categories:
  - ASP.NET
tags:
  - ASP.NET
  - deployment
---

## IIS deployment

An Asp.net core Applications can be hosted into

- InProcess (default): ASP.NET Core Module (AspNetCoreModuleV2) forwards the requests to IIS HTTP Server.
  IIS HTTP Server is a server that runs in-process with IIS.
- OutOfProcess: IIS HTTP Server won’t be used, Kestrel web server is used to process your requests.

The general flow of a request is as follows:

- A request arrives from the web to the kernel-mode HTTP.sys driver.
- The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).
- The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (IISHttpServer).

```xml
<system.webServer>
  <handlers>
    <add name="aspNetCore" path="*" verb="*"
          modules="AspNetCoreModuleV2" />
  </handlers>
  <aspNetCore processPath="dotnet"
              arguments=".\app.dll"
              stdoutLogEnabled="true"
              stdoutLogFile=".\logs\stdout"
              hostingModel="inprocess" />
</system.webServer>
```

```xml
<PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
    <AspNetCoreHostingModel>-InProcess|OutOfProcess-</AspNetCoreHostingModel>
</PropertyGroup>
```

### Deployment steps

- Install ASP.NET Core Runtime (ASP.NET Core Module v2) on the server.

- Create an iis site and an application.

- Create an iis application pool and an identity.
  (.Net CLR version: No managed Code)

- Enable file logs and prepare your logging environment to file.
- You can set the execution environment in publish profile file IISProfile.pubxml (or in Project file csproj):

```xml
<?xml version="1.0" encoding="utf-8"?>

<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>MSDeploy</WebPublishMethod>
    <TargetFramework>net5.0</TargetFramework>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
     <EnvironmentName>Development</EnvironmentName>
    <MSDeployServiceURL>localhost</MSDeployServiceURL>
    <SiteUrlToLaunchAfterPublish>http://localhost/app</SiteUrlToLaunchAfterPublish>
    <DeployIisAppPath>Default Web Site\app</DeployIisAppPath>
    <MSDeployPublishMethod>InProc</MSDeployPublishMethod>
    <ProjectGuid>911a0e75-1163-4585-bb4b-ff045c5b58a6</ProjectGuid>
    <SelfContained>false</SelfContained>
    <SkipExtraFilesOnServer>True</SkipExtraFilesOnServer>
  </PropertyGroup>
</Project>
```

- Publish the profile using publxml file or by command:

```csharp
dotnet publish app.csproj  -c Release -o C:\app
dotnet publish app.csproj -c Release /p:PublishDir=//app

// Or using publish profile
// create a folder iisprofile in properties/PublishProfiles/IISProfile.pubxml
//<Project>
//   <PropertyGroup>
//     <PublishProtocol>Kudu</PublishProtocol>
//     <PublishSiteName>nodewebapp</PublishSiteName>
//     <UserName>username</UserName>
//     <Password>password</Password>
//   </PropertyGroup>
// </Project>


dotnet publish app.csproj -c Release /p:DeployOnBuild=true -p:PublishProfile=IISProfile.pubxml
```

- Enable Health middleware

- Enable Response cache:
  <https://docs.microsoft.com/en-us/aspnet/core/performance/caching/middleware?view=aspnetcore-5.0>

- Enable compression on server:
  <https://docs.microsoft.com/en-us/aspnet/core/performance/response-compression?view=aspnetcore-5.0>
  <https://docs.nginx.com/nginx/admin-guide/web-server/compression/>

- Check windows event logs and file logs (stdoutLogFile) for errors.
