---
title: "ASP.NET Core-WebApi with Swagger"
categories:
  - WebApi
tags:
  - webapi
  - swagger
---

OpenAPI Specification (formerly Swagger Specification) is an API description format for REST APIs. An OpenAPI file allows you to describe your entire API. API specifications can be written in YAML or JSON. The format is easy to learn and readable to both humans and machines.

Swagger is a set of open-source tools built around the OpenAPI Specification that can help you design, build, document and consume REST APIs.

Swagger Document is defined with proper Title and Version details.
"/swagger/{documentName}/swagger.json".

## Swashbuckle

Swashbuckle provides auto generation of Swagger 2.0 and related UI.

- Add package Swashbuckle.AspNetCore

- Register service:

```csharp
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "API", Version = "v1"});
});

```

- Add service to pipeline:

{yourBaseUrl}/swagger/v1/swagger.json

```csharp
app.UseSwagger();

```

{yourBaseUrl}/swagger

```csharp
app.UseSwaggerUI(c =>
{
     c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
    c.SwaggerEndpoint("/swagger/v2/swagger.json", "My API V2");
});

```

Edit csproj to generate document:

```xml
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
</PropertyGroup>
```

When returning IActionResult, you should attribute your action with all the ProducesResponseType you need to cover the results of your action:

```csharp
[HttpPost]
[ProducesResponseType(typeof(MyRequest), 200)]
[ProducesResponseType(typeof(IDictionary<string, string>), 400)]
[ProducesResponseType(typeof(void), 400)]
[ProducesResponseType(typeof(void), 404)]
[ProducesResponseType(typeof(void), 409)]
public async Task<IActionResult> Post([FromBody] MyRequest request)
```

## NSwag

NSwag is a Swagger/OpenAPI 2.0 and 3.0
NSwag combines the functionality of Swashbuckle (OpenAPI/Swagger generation) and AutoRest (client generation) in one toolchain.

- Register service:

```csharp
services.AddOpenApiDocument();
```

- Add service to pipeline:
  {yourBaseUrl}/swagger

```csharp
app.UseOpenApi();
app.UseSwaggerUi3();
```

Ways to use the toolchain:

- NSwag.MSBuild

Install nuget package:
NSwag.MSBuild

Download a sample config:
<https://github.com/RicoSuter/NSwag/blob/master/src/NSwag.Sample.NET50/nswag.json>

Update config:

```javascript
{
  "runtime": "Net50",

  "documentGenerator": {
    "aspNetCoreToOpenApi": {
      "project": "Api.csproj", // your project

      "noBuild": true,
      "defaultPropertyNameHandling": "CamelCase",
      "documentName": "v3",
      "output": "wwwroot/api/openapi.json",  // pth to specification
      "outputType": "OpenApi3",  // OpenApi3 OR Swagger2
    }
  },
  "codeGenerators": {}
}
```

Add target to csproj:

```xml
<Target Name="NSwag" AfterTargets="PostBuildEvent" Condition=" '$(Configuration)' == 'Debug' ">
    <Message Importance="High" Text="$(NSwagExe_Net50) run nswag.json /variables:Configuration=$(Configuration)" />

    <Exec WorkingDirectory="$(ProjectDir)" EnvironmentVariables="ASPNETCORE_ENVIRONMENT=Development" Command="$(NSwagExe_Net50) run nswag.json /variables:Configuration=$(Configuration)" />

    <Delete Files="$(ProjectDir)\obj\$(MSBuildProjectFile).NSwag.targets" />

  </Target>
```

- Nswag npm

Install npm package:
npm install nswag -g
nswag help
nswag version /runtime:Net50

```javascript
nswag aspnetcore2openapi /file:pathto/nswag.json /assembly:pathto/api.dll" /runtime:Net50 /output:pathto/openapi.json
```

- NSwagStudio

Download the MSI : NSwagStudio
Then generate the specification.

- In your C# code (Not recommanded)

```csharp
services.AddOpenApiDocument(document => document.DocumentName = "v1");
services.AddSwaggerDocument(document => document.DocumentName = "v2");

app.UseOpenApi(); // serve documents (same as app.UseSwagger())
app.UseSwaggerUi3(); // serve Swagger UI
app.UseReDoc(); // serve ReDoc UI
```

Serve multiple documents (chain):

```csharp
app.UseOpenApi(options =>
            {
                options.DocumentName = "openapiv3";
                options.Path = "/api/openapi.json";

            }
                );// serve OpenAPI/Swagger documents

app.UseSwaggerUi3(settings =>
{
    settings.Path = "/api";
    settings.DocumentPath = "/api/openapi.json";
});  // serve Swagger UI

app.UseOpenApi(p => p.Path = "/swagger/{documentName}/swagger.json");
app.UseSwaggerUi3(p => p.DocumentPath = "/swagger/{documentName}/swagger.json");

```

Use multiple routes:

```csharp
app.UseSwaggerUi3(options =>
{
  // Add multiple OpenAPI/Swagger documents to the Swagger UI 3 web frontend
  options.SwaggerRoutes.Add(new SwaggerUi3Route("Swagger", "/swagger/v1/swagger.json"));
  options.SwaggerRoutes.Add(new SwaggerUi3Route("Openapi", "/openapi/v1/openapi.json"));

  // Define web UI route
  options.Path = "/swagger_all";
});
```

### Generate specification from WebApi controller

Install nuget:

NSwag.Generation.WebApi

```csharp
var settings = new WebApiOpenApiDocumentGeneratorSettings
{
    DefaultUrlTemplate = "api/{controller}/{action}/{id}"
};
var generator = new WebApiOpenApiDocumentGenerator(settings);
var document = await generator.GenerateForControllerAsync<PersonsController>();
var swaggerSpecification = document.ToJson();
```
