---
title: "Getting Started With Azure"
categories:
  - azure
tags:
  - azure
  - cloud
---

## Azure Function APP (Serverless Computing, FAAS)

Azure Functions is a cloud service. It provides serverless compute for Azure.

Triggers:
A function has a "trigger", triggers are what cause a function to run:

- Http Trigger: Azure Functions may be invoked via HTTP requests to build serverless APIs.

Bindings:

Binding to a function is a way of declaratively connecting another resource
to the function.

- Output binding: Return an HTTP response from a function
  Ex1. A new queue message arrives which runs a function to write to another queue.
  =>
  Trigger: Queue;
  Input binding: None;
  Output binding: Queue

Ex2. Run code when a file is uploaded or changed in blob storage.
an Azure blob storage account is a publisher
=>
All triggers and bindings have a direction property in the function.json file:
For triggers, the direction is always "in".
Input and output bindings use "in" and "out"

HTTP trigger: A function that will be run whenever it receives an HTTP request, responding based on data in the body or query string
Timer trigger: A function that will be run on a specified schedule
Azure Queue Storage trigger: A function that will be run whenever a message is added to a specified Azure Storage queue
Azure Service Bus Queue trigger: A function that will be run whenever a message is added to a specified Service Bus queue
Azure Service Bus Topic trigger: A function that will be run whenever a message is added to the specified Service Bus topic
Azure Blob Storage trigger: A function that will be run whenever a blob is added to a specified container
Azure Event Hub trigger: A function that will be run whenever an event hub receives a new event
Azure Cosmos DB trigger: A function that will be run whenever documents change in a document collection
IoT Hub (Event Hub): A function that will be run whenever an IoT Hub receives a new event from IoT Hub (Event Hub)
SendGrid: A function that sends a confirmation e-mail when a new item is added to a particular queue
Azure Event Grid trigger: A function that will be run whenever an event grid receives a new event
RabbitMQ trigger
A function that will be run whenever a message is added to a specified RabbitMQ queue
SignalR negotiate HTTP trigger
An HTTP triggered function that SignalR clients will call to begin connection negotiation

<https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?tabs=csharp>

### Compiled C# functions

Type of compiled C# functions:

- In-process: Your function code runs in the same process as the Functions host process.
- Isolated process: Your function code runs in a separate .NET worker process.

Dependencies:
Microsoft.NET.Sdk.Functions

The functions runtime only includes HTTP and timer triggers by default. Other triggers and bindings are available as separate packages.

Install Extension package:
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>

Tools:
Azure Functions Core Tools: The Azure Functions
Core Tools provide a local development experience for creating,
developing, testing, running, and debugging Azure Functions.

npm i -g azure-functions-core-tools@4
npm i -g azure-functions-core-tools@3
func host start
func host start --port <port> --useHttps --cert <yourCertificate>.pfx --password <theCertificatePrivateKey>

{
"profiles": {
"MyFunc": {
"commandName": "Project",
"commandLineArgs": "host start --useHttps --cert \"<cert>.pfx\" --password \"<pass>\""
}
}
}

### Configuration Files

- hosts.json:
  The host.json metadata file contains configuration options that affect all functions in a function app instance.
  https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json
  "extensions": {
  "http": {
  "routePrefix": "api",
  "maxOutstandingRequests": 200,
  "maxConcurrentRequests": 100,
  "dynamicThrottlesEnabled": true,
  "hsts": {
  "isEnabled": true,
  "maxAge": "10"
  },
  "customHeaders": {
  "X-Content-Type-Options": "nosniff"
  }
  }
  }

The Functions runtime uses an Azure Storage account internally.
For all trigger types other than HTTP and webhooks, set the Values.AzureWebJobsStorage key to a valid Azure Storage account connection string.

Your function app can also use the Azure Storage Emulator for the AzureWebJobsStorage connection setting that's required by the project.
To use the emulator, set the value of AzureWebJobsStorage to UseDevelopmentStorage=true.
Change this setting to an actual storage account connection string before deployment.

Cloud Explorer->Storage Accounts->Storage account->Properties->Primary Connection String.

### HTTP Trigred functions

Passing test data to a function:
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'

The default return value for an HTTP-triggered function is:
HTTP 204 No Content with an empty body

https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#example

### Non-HTTP triggered functions

For all functions other than HTTP and Event Grid triggers, you can test your functions locally
using REST by calling a special endpoint called an administration endpoint.

Calling this endpoint with an HTTP POST request on the local server triggers the function.

curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger

## Storage account

An Azure storage account contains all of your Azure Storage data objects:
blobs, files, queues, and tables. The storage account provides a unique namespace
for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS.

Azure portal->Sorage Account -> Create Storage account

## Storage Blob

## Event Grid/ Event Hubs

Azure Event Grid and Azure Event Hubs are services that help us build applications that respect the event-based architecture.
Event-based architecture gives us the ability to build applications that communicate with different systems by sharing information through events.

Event Grid can distribute events from different sources like Azure Blob Storage to different handlers like Azure Function (Event Grid trigger function) or Webhook.

Azure Event Grid allows you to easily build applications with event-based architectures.

## What is Webhooks ?

Webhooks provide a way to send a JSON representation of an event to any service.
All that is required is a public endpoint (HTTP or HTTPS).

Webhooks allow integration with other systems, including third-party systems.
Essentially, the external system can call an Azure Function when an event happens;
in this way, there’s no need to periodically poll an external system to look for changes.
If an external system supports Webhooks, it can be configured to point to an Azure Functions Webhook (via HTTP)
and call the endpoint with relevant data. Code inside the Azure Function can take this incoming data and perform processing.

A webhook in web development is a method of augmenting or altering the behavior
of a web page or web application with custom callbacks.
These callbacks may be maintained, modified, and managed by third-party users
and developers who may not necessarily be affiliated with the originating
website or application.

Download ngrok:
https://ngrok.com/download
Sign up then configure token:
ngrok authtoken xxxxxx

Register Microsoft.EventGrid Resource:
Azure Subscription->Ressource providers->Microsoft.EventGridGrid->Register

Create Event Subscription:
AccountStorage->Events->Create Event Subscription
Select an Endpoint of type Webhook:
https://1de5-2a01-cb00-424-2000-205a-ba42-c46c-8737.ngrok.io//runtime/webhooks/EventGrid?functionName=CreatePackage

Get storage connection strings:
Storage Account->Access Keys->Show Keys

Download Azure Cli
az login
az account list
// set the subscription that your Function is hosted in
az account set --subscription 'my-subscription-name'
// Create a new resource group
az group create --location westus --name MyRG
// fetch our app settings from our Function App in Azure.
func azure functionapp fetch-app-settings '<function-name>' --output-file local.settings.json

// Decrypting app settings
func settings decrypt

// publish function
// create an api management api
// Get the function access key
Azure->Functions >myfunction->Keys
// set this key in API Management so that it can access the function endpoint.
Azure->Api Management-> select Azure Functions OpenAPI Extension > Test > POST Run, then under Inbound policy select Add policy.

Download Azure Storage Emulator

Downlod Azure Tools
https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local?WT.mc_id=dotnet-39275-juyoo&tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash%2Ckeda
npm install -g azure-functions-core-tools@4 --unsafe-perm true
func start

## PowerShell

Start-Process -NoNewWindow func start

choco install mkcert
mkcert -install
mkcert localhost 127.0.0.1 ::1
openssl s_client -showcerts -connect 127.0.0.1:10000

# PowerShell

$env:NODE_TLS_REJECT_UNAUTHORIZED = '0'

azurite -s -l C:\Users\staieb\azurite -d C:\Users\staieb\azurite\debug.log --cert C:\Users\staieb\myproject\tests\Application.Tests\certs\public.pem --key C:\Users\staieb\myproject\tests\Application.Tests\certs\private-key.pem

Azure Resource Group
A resource group is a logical container into which Azure resources
are deployed and managed.
az account list
az account set --subscription 'my-subscription-name'
az group create --name "myResourceGroup" -l "EastUS"
az group delete --name "myResourceGroup"

Azure App Service Plan
An App Service plan defines a set of compute resources for a web app/function... to run.

Azure->App Service Plan->Create

az appservice plan create -g MyResourceGroup -n MyPlan --sku F1
az appservice plan delete --name MyAppServicePlan --resource-group MyResourceGroup
az appservice plan show --name MyAppServicePlan --resource-group MyResourceGroup
az appservice plan list --query "[?sku.tier=='Free']"

Azure Key Vault

Azure Key Vault is a cloud service for securely
storing and accessing secrets (passwords, certificates...).
The Azure Key Vault service can store three types of items:

- secrets:Secrets are any sequence of bytes under 10 KB like connection strings,
  passwords

- keys: Keys involve cryptographic material imported into Key Vault or generated
- certificates: a managed X.509 certificate

Azure-> Key Vaults->Create
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"

private static string secret = System.Environment.GetEnvironmentVariable("mysecret");

Create an App Configuration store

Azure->App Configuration->Create
class Startup : FunctionsStartup
{
public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
{
string cs = Environment.GetEnvironmentVariable("ConnectionString");
builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
}

        public override void Configure(IFunctionsHostBuilder builder)
        {
        }
    }

public Function1(IConfiguration configuration)
{
\_configuration = configuration;
string message = \_configuration[keyName];
}

Create App Function

az functionapp create --name ehellodev --storage-account ehelloclouddevstorage --plan ehello-plan --resource-group RS_HelloFunc --functions-version 4

az functionapp update --name ehello --resource-group RS_HelloFunc --plan ehello-plan
az functionapp plan update -g RS_HelloFunc -n ASP-RSHelloFunc-a25a --sku F1

Azure ->Function App->Identity -> Enable
Azure -> Key vaults->Access policies->Add

Function App Settings
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
