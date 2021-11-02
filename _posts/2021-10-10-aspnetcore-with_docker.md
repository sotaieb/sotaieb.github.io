---
title: "ASP.NET with Docker/Docker Compose/Docker Swarm"
categories:
  - Docker
tags:
  - Docker
  - Docker Compose
  - Docker Swarm
---

The goal is to define and run multi-container Docker for Asp.net core applications.

## Configure Application to use Https

```sh
dotnet dev-certs https --clean
dotnet dev-certs https -ep c:\path\to\userprofile\.aspnet\https\cert.pfx -p admin
dotnet dev-certs https --trust

cd path\to\project

dotnet user-secrets -p app.csproj set "Kestrel:Certificates:Development:Password" "admin"
```

Import certificate:
MMC. File --> Add/Remove Snap-In>Click Add>Choose Certificates>Add
Check the "Computer Account" radio button>Next.

Choose the client computer in the next screen>Finish.
install the certificate "PFX" into the "Trusted Root Certification Authorities certificate store".

Select the cert-> Right click-> Go to All tasks-> Manage Private Keys-> Add permissions to user executing the server.

## Manage docker image

```sh
docker build --no-cache -f "path\to\dockerfile\Dockerfile" --force-rm -t ascode-sts "path\to\dockerontext"

Windows container
# The --rm flag here just removes the container after it has finished executing.

Windows host, Windowds container
docker run --rm -it ascode-sts
-p 6000:80
-p 6001:443
-e ASPNETCORE_URLS="https://+;http://+"
-e ASPNETCORE_HTTPS_PORT=6001
-e ASPNETCORE_ENVIRONMENT=Development
-e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\my.pfx
-e ASPNETCORE_Kestrel__Certificates__Default__Password="admin"
-v $env:APPDATA\microsoft\UserSecrets\:C:\UserSecrets\
-v $env:USERPROFILE\.aspnet\https:C:\https\
--name ascode-sts-release

Windows host, Linux container

-v $env:APPDATA\microsoft\UserSecrets\:/root/.microsoft/usersecrets
-v $env:USERPROFILE\.aspnet\https:/root/.aspnet/https/

Using dockerfile:
ENV ASPNETCORE_ENVIRONMENT Staging
ENV ASPNETCORE_URLS https://+;http://+
ENV ASPNETCORE_HTTPS_PORT 443
ENV ASPNETCORE_Kestrel__Certificates__Default__Path /root/.aspnet/https/AsCode.Sts.pfx
ENV ASPNETCORE_Kestrel__Certificates__Default__Password "admin"

docker run --rm -it ascode-sts
-p 6000:80
-p 6001:443
--name ascode-sts-release


```

## Configure MSSQL

Windows Firewall ->Advanced Settings->Inbound Rules
Add a rule to enable TCP port 1433
Add a rule to enable UDP port 1434

Server Properties - > Connections -> Allow Remote Connections
Server Properties -> Security -> SQL Server and Windows Authentication mode

Enable SQL Service to listen on TCP/IP
SQL Configuration Manager ( SQLServerManager13.msc) -> SqlServer Network -> Enable TCP/IP

## Automate certificate generation for nginx as Reverse proxy

```sh

docker-compose up --scale api=4 --build

FROM alpine:3.8 AS generate
WORKDIR /certificates
RUN apk update && \
 apk add --no-cache openssl && \
 rm -rf /var/cache/apk/\*
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout example.key -out example.crt -subj "/C=GB"

FROM nginx:1.15.8-alpine
EXPOSE 443
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=generate /certificates/example.key /certificates/example.crt /etc/nginx/ssl/
```

## Links

<https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md>

<https://github.com/dotnet/dotnet-docker/blob/main/samples/host-aspnetcore-https.md>
