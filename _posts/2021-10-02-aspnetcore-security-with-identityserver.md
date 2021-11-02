---
title: "Aspnet Core security with IdentityServer"
categories:
  - security
tags:
  - dotnet
  - oauth
  - jwt
  - identityserver
---

The goal is to create an Aspnet core application with a security layer using IdentityServer, an OpenID Connect and OAuth 2.0 framework.

## Identity and Access Control

Identity, means some set of attributes that a computer system can use to represent a person, organization, application or device.
Access control, on the other hand, refers to a security technique used to regulate who or what can access and use resources (data and operations) in a computing environment.

## Authentication and Authorization

Authentication means the process used to determine whether a user is who they claim to be.
Once authenticated, authorization determines which resources a given user should be able to access, and what they're allowed to do with those resources.

## OpenID Connect and OAuth

Protocols for obtaining and using tokens

- Allows for authentication to client application (With id_token)
- Allows for securing server APIs (With access_token)

OpenID Connect (OIDC) is a simple identity and authentication protocol layer built on top of the OAuth protocol that allows applications (typically referred to as clients) to verify the identity of end-users.
OAuth is an open standard for authorization that provides secure, delegated access, meaning that an application (or client) can take actions or access resources on a resource server on behalf of a user,
without the user ever sharing their credentials with the application, the key being delegated access.

- OAuth 2.0 is an authorization framework. It is a set of defined process flows for "delegated authorization".
- OpenID Connect is a set of defined process flows for "federated authentication", it defines a workflow for authentication.
  It is a "profile" of OAuth 2.0 specifically designed for attribute release and authentication.
- There is no id_token defined in OAuth2 (contains a hash of the access token)

OpenID Connect has become the leading standard for single sign-on and identity provision on the Internet.

Federated Authentication allows you to login to a site using your facebook or google account.
Delegated Authorization is the ability of an external app to access resources.

## Claims/Roles authorization

Roles-based authorization :

- Identify a user.
- Get user roles.
- Compare user roles to roles that are authorized to access a resource.

Claims-based authorization :

- Assign a claim to user.
- User present a claim for authorization rather than username and password.

A role is a specific kind of claims :

Based on my identity (username/password), i'm in this role, because i'm a member of this role, i have access to this resource.

## Terminology

User
A user is a human that is using a registered client to access resources.

Client
A client is a piece of software that requests tokens from IdentityServer.

Api Resources
Resources are something you want to protect with IdentityServer

API Scopes (Oath2.0)
An API is a resource in your system that you want to protect.

Identity Scopes/Identity Resources (openId)
identity data like user id, name or email address.
openid (subject id) scope
profile (first name, last name etc..) scope

Identity Token
An identity token represents the outcome of an authentication process. It contains at a bare minimum an identifier for the user (called the sub aka subject claim) and information about how and when the user authenticated. It can contain additional identity data.

Access Token
An access token allows access to an API resource. Clients request access tokens and forward them to the API. Access tokens contain information about the client and the user (if present). APIs use that information to authorize access to their data.

## Implementation

We'll implement the Implicit Grant OAuth flow:

- IdentityServer4 as our OpenId Connect Provider.
- Secure the access to an ASP.NET5 Web API.

## Getting started IdentityServer4

dotnet new -i IdentityServer4.Templates
IdentityServer4 with AdminUI is4admin
IdentityServer4 with ASP.NET Core Identity is4aspid
IdentityServer4 Empty is4empty
IdentityServer4 with Entity Framework Stores is4ef  
IdentityServer4 with In-Memory Stores and Test Users is4inmem
IdentityServer4 Quickstart UI (UI assets only) is4ui

Add Nuget
IdentityServer4
Microsoft.AspNetCore.Identity
Microsoft.AspNetCore.Authentication.JwtBearer

## Configure IdentityServer4

In development:

```javascript
"IdentityServer": {
  "Key": {
  "Type": "Development"
  }
}
```

Or by using code:

```csharp
services.AddIdentityServer()
        .AddDeveloperSigningCredential()
```

In production:
Creating the Self-Signed Cert (using OpenSSL)
Download Open SSL
<https://slproweb.com/products/Win32OpenSSL.html>

Add bin folder to the system path
C:\Program Files\OpenSSL-Win64\bin

Create the certificate and private key

openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout idsdev.key -out idsdev.crt -subj "/CN=ascode.sotaieb.com" -days 3650

Convert certificate and key to pfx format

openssl pkcs12 -export -out idsdev.pfx -inkey idsdev.key -in idsdev.crt -certfile idsdev.crt

Then add to certificate store (required to identity server because of permissions to access private key),
mmc-> Add/Remove Snap-in->certificates (Add)->Next->Personal->Import pfx

pem to pfx:

```sh
openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt
```

```javascript
"IdentityServer": {
  "Key": {
      "Type": "File",
      "FilePath": "pathto/cert.pfx",
      "Password": "strongpassword"
    }
}
```

Or by using code:

```csharp
services.AddIdentityServer()
        .AddSigningCredentials()
```

Or

```csharp
var path = Path.Combine(configuration["cert"], "idsdev.pfx");
            var cert = new X509Certificate2(path, "admin");
services.AddIdentityServer()
        .AddSigningCredential(cert)
```

Use in memory configuration:

```csharp
internal class Config
    {
        internal static class Clients
        {
            public static IEnumerable<Client> Get()
            {
                return new List<Client>
            {
                new Client
                {
                    ClientId = "sts",
                    ClientName = "Sts",
                    AllowedGrantTypes = GrantTypes.ClientCredentials,
                    ClientSecrets = new List<Secret> {new Secret("password".Sha256())}, // change me!
                    AllowedScopes = new List<string> { "AsCode.Sts.All" } // from api scopes in code below
                }
            };
            }
        }

        internal static class ApiScopes
        {
            public static IEnumerable<ApiScope> Get()
            {
                return new[]
                {
                    new ApiScope("AsCode.Sts.All", "Access to all AsCode.Sts"),
                    new ApiScope("AsCode.Sts.Admin", "Admin Access to AsCode.Sts"),
                };
            }
        }
        internal static class ApiResources // your api resources
        {
            public static IEnumerable<ApiResource> Get()
            {
                return new[]
                {
                    new ApiResource
                    {
                        Name = "AsCode.Sts", // this is the audience
                        DisplayName = "AsCode.Sts",
                        Scopes = new List<string> {"AsCode.Sts.All"}, // from api scopes in code above
                        ApiSecrets = new List<Secret> {new Secret("ScopeSecret".Sha256())}, // change me!
                        UserClaims = new List<string> {"role"}
                    }
                };
            }
        }
    }
```

- Register IdentityServer

```csharp
services.AddIdentityServer()
         .AddDeveloperSigningCredential()
         .AddInMemoryApiScopes(Config.ApiScopes.Get())
         .AddInMemoryClients(Config.Clients.Get())
         .AddInMemoryApiResources(Config.ApiResources.Get());

```

- Register Authentication/Authorization moddleware

```csharp
services.AddAuthentication("Bearer")
 //.AddIdentityServerJwt()
 .AddJwtBearer("Bearer", options =>
 {
     options.Authority = "https://localhost:6001";
     options.Audience = "AsCode.Sts"; // the api resource (configured in ApiResources)
      // options.TokenValidationParameters = new TokenValidationParameters
      // {
        //ValidateAudience = false
      //};
  });
```

- You need to add the IdentityServer middleware to the pipeline.

```csharp
app.UseIdentityServer() // UseIdentityServer includes a call to UseAuthentication,
    .UseAuthorization();
```

## Provider Metadata

Openid-configuration is a URI defined within OpenID Connect which provides configuration information about the Identity Provider (IDP).
<https://localhost:5001/.well-known/openid-configuration>

```javascript
{
  "issuer": "https://localhost:6001",
  "jwks_uri": "https://localhost:6001/.well-known/openid-configuration/jwks",
  "authorization_endpoint": "https://localhost:6001/connect/authorize",
  "token_endpoint": "https://localhost:6001/connect/token",
  "userinfo_endpoint": "https://localhost:6001/connect/userinfo",
  "end_session_endpoint": "https://localhost:6001/connect/endsession",
  "check_session_iframe": "https://localhost:6001/connect/checksession",
  "revocation_endpoint": "https://localhost:6001/connect/revocation",
  "introspection_endpoint": "https://localhost:6001/connect/introspect",
  "device_authorization_endpoint": "https://localhost:6001/connect/deviceauthorization",
  "frontchannel_logout_supported": true,
  "frontchannel_logout_session_supported": true,
  "backchannel_logout_supported": true,
  "backchannel_logout_session_supported": true,
  "scopes_supported": [
  "AsCode.Sts.All",
  "AsCode.Sts.Admin",
  "offline_access"
  ],
  "claims_supported": [
  "role"
  ],
  "grant_types_supported": [
  "authorization_code",
  "client_credentials",
  "refresh_token",
  "implicit",
  "urn:ietf:params:oauth:grant-type:device_code"
  ],
  "response_types_supported": [
  "code",
  "token",
  "id_token",
  "id_token token",
  "code id_token",
  "code token",
  "code id_token token"
  ],
  "response_modes_supported": [
  "form_post",
  "query",
  "fragment"
  ],
  "token_endpoint_auth_methods_supported": [
  "client_secret_basic",
  "client_secret_post"
  ],
  "id_token_signing_alg_values_supported": [
  "RS256"
  ],
  "subject_types_supported": [
  "public"
  ],
  "code_challenge_methods_supported": [
  "plain",
  "S256"
  ],
  "request_parameter_supported": true
}
```

## ClientCredentials flow

Ask the server for an access_token (client_credentials grant type)

```sh
curl --location --request POST 'https://localhost:6001/connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'client_id=sts' \
--data-urlencode 'client_secret=password'
```

```javascript
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkREOThGMjg4NzRDNzQ5RDIzMUI4NjRGNUM3QzI0NkYxIiwidHlwIjoiYXQrand0In0.eyJuYmYiOjE2MzQzMTkwMjUsImV4cCI6MTYzNDMyMjYyNSwiaXNzIjoiaHR0cHM6Ly9sb2NhbGhvc3Q6NjAwMSIsImF1ZCI6IkFzQ29kZS5TdHMiLCJjbGllbnRfaWQiOiJzdHMiLCJqdGkiOiJCRjNDNTREQUVGNUVGM0U2RDg3OThGNzJGQThDNzc2MSIsImlhdCI6MTYzNDMxOTAyNSwic2NvcGUiOlsiQXNDb2RlLlN0cy5BbGwiXX0.ApECt1ECjiQ1HrPzXAYG1M4e4AQTOMuYk6Y_Kdwxl7-P0P8D4Usj1W6IFX_G4PnB15k0H2dp5JrPCCxELb9olin1ZKY_WwMChTmX0HRNvYHFHEbGyltoAQ7G7N4hzLre-Gc6fXtL54cgEcWVpmiSEJ0RzS5cuYSTj7yoS9ER6Dvub6Azl1Of98400l0GesHp0ump1m4Qy2VxMowcHm7VZksz0JgcuaoPRFyF9yF5x4Mtc-zSaTlErHRJlhLBs7uG8nhCUXgAW-b2V8oIBxyHpoh_EWhytx1flm6pWHMgzTl5qOA2N2z9AfsNdo8aBYu2bt8Q-mKanwkTFXgubGwj6w",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "AsCode.Sts.All"
}
```

Request the resource api:

```sh
curl --location --request GET 'https://localhost:6001/api/check' \
--header 'Authorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IkREOThGMjg4NzRDNzQ5RDIzMUI4NjRGNUM3QzI0NkYxIiwidHlwIjoiYXQrand0In0.eyJuYmYiOjE2MzQzMTkwMjUsImV4cCI6MTYzNDMyMjYyNSwiaXNzIjoiaHR0cHM6Ly9sb2NhbGhvc3Q6NjAwMSIsImF1ZCI6IkFzQ29kZS5TdHMiLCJjbGllbnRfaWQiOiJzdHMiLCJqdGkiOiJCRjNDNTREQUVGNUVGM0U2RDg3OThGNzJGQThDNzc2MSIsImlhdCI6MTYzNDMxOTAyNSwic2NvcGUiOlsiQXNDb2RlLlN0cy5BbGwiXX0.ApECt1ECjiQ1HrPzXAYG1M4e4AQTOMuYk6Y_Kdwxl7-P0P8D4Usj1W6IFX_G4PnB15k0H2dp5JrPCCxELb9olin1ZKY_WwMChTmX0HRNvYHFHEbGyltoAQ7G7N4hzLre-Gc6fXtL54cgEcWVpmiSEJ0RzS5cuYSTj7yoS9ER6Dvub6Azl1Of98400l0GesHp0ump1m4Qy2VxMowcHm7VZksz0JgcuaoPRFyF9yF5x4Mtc-zSaTlErHRJlhLBs7uG8nhCUXgAW-b2V8oIBxyHpoh_EWhytx1flm6pWHMgzTl5qOA2N2z9AfsNdo8aBYu2bt8Q-mKanwkTFXgubGwj6w'

```

## Authorization Code Flow

The authorization code flow allows you to request an authorization code from the authorization endpoint, which you can then exchange at the token endpoint.

PKCE (Proof Key for Code Exchange) is a new, more secure authorization flow (based on the OAuth 2.0 spec)
Building on top of the authorization code flow, there are three new parameters used by PKCE: code_verifier, code_challenge, and code_challenge_method.
The code_verifier is a cryptographically random string generated by your app.
The code_challenge is derived from the code_verifier using one of the two possible transformations: plain and S256.
The code_challenge_method tells the server which function was used to transform the code_verifier (plain or S256).

- Client (your app) creates the code_verifier. (RFC 7636, Section 4.1)
- Client creates the code_challenge by transforming the code_verifier using S256 encryption. (RFC 7636, Section 4.2)
- Client sends the code_challenge and code_challenge_method with the initial authorization request. (RFC 7636, Section 4.3)
- Server responds with an authorization_code. (RFC 7636, Section 4.4)
- Client sends authorization_code and code_verifier to the token endpoint. (RFC 7636, Section 4.5)
- Server transforms the code_verifier using the code_challenge_method from the initial authorization request and checks the result against the code_challenge. If the value of both strings match, then the server has verified that the requests came from the same client and will issue an access_token.

Example:

```javascript
const base64Encode = (str) => {
  return str
    .toString("base64")
    .replace(/\+/g, "-")
    .replace(/\//g, "_")
    .replace(/=/g, "");
};
const codeVerifier = base64Encode(crypto.randomBytes(32));
console.log(`Client generated code_verifier: ${codeVerifier}`);

const sha256 = (buffer) => {
  return crypto.createHash("sha256").update(buffer).digest();
};
const codeChallenge = base64Encode(sha256(codeVerifier));
```

OR
<https://www.davidbritch.com/2017/08/using-pkce-with-identityserver-from_9.html>

### Without back channel

First, let's disable IdentityServerPKCE and configure a client:

```csharp
new Client
 {
     ClientId = "js",
     ClientName = "JavaScript Client",
     AllowedGrantTypes = GrantTypes.Code,
     RequireClientSecret = false,
     AllowAccessTokensViaBrowser = true,
     RedirectUris =           { "https://localhost:3001/home" },
     PostLogoutRedirectUris = { "https://localhost:3001/home" },
     AllowedCorsOrigins =     { "https://localhost:3001" },
     RequirePkce= false,
     AllowedScopes =
     {
         "api1",
     }
 }

```

Second, redirect user to login page (authorize endpoint):

```javascript
fetch("https://localhost:6001/connect/authorize?
  client_id=js
  &redirect_uri=https%3A%2F%2Flocalhost%3A3001%2Fhome
  &response_type=code
  &scope=api1
  // &code_challenge=pckxUwRhkTcOwwoNTVX8V5spNiDnE_IFjcLK374SfnI
  // &code_challenge_method=S256
  &response_mode=query"
  , {
    "method": "GET",
});

```

If authentication succeeded, user will be redirected to client application:

```sh
https://localhost:6001/connect/authorize/callback?
  client_id=js
  &redirect_uri=https%3A%2F%2Flocalhost%3A3001%2Fhome
  &response_type=code
  &scope=api1
 // &code_challenge=oB6o0Tqt21jPKCSyxi9MjxPQuTqxNheJxEVU8nWPiQY
 // &code_challenge_method=S256
  &response_mode=query
```

Next, we exchange the code by an access token:

```sh
fetch("https://localhost:6001/connect/token", {
  "headers": {
    "content-type": "application/x-www-form-urlencoded"
  },
  "body": "
    client_id=js
    &code=5C20271B65EFF8A526903AD7D92AC920E90A77C9899427353C010D7E186A8030
    &redirect_uri=https%3A%2F%2Flocalhost%3A3001%2Fhome
   // &code_verifier=b53f70bf441c4b9f97bc69a7d45602a389d9438eead945f38180b11b2d04abd84015ec09ec4c4127a10dd2ff2512d8e3
    &grant_type=authorization_code"
    , "method": "POST"

});
```

Now, we are ready to call our resource:

```sh
fetch("https://localhost:3001/api/check", {
  "headers": {
    "authorization": "Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IkREOThGMjg4NzRDNzQ5RDIzMUI4NjRGNUM3QzI0NkYxIiwidHlwIjoiYXQrand0In0.eyJuYmYiOjE2MzQ1NjAxODIsImV4cCI6MTYzNDU2Mzc4MiwiaXNzIjoiaHR0cHM6Ly9sb2NhbGhvc3Q6NjAwMSIsImF1ZCI6WyJBc0NvZGUuU3RzIiwiYXBpMSJdLCJjbGllbnRfaWQiOiJqcyIsInN1YiI6IjgxODcyNyIsImF1dGhfdGltZSI6MTYzNDU2MDE4MCwiaWRwIjoibG9jYWwiLCJqdGkiOiIwRUNDMUY4MzJGRkIzRERGODIxRUJCNTYyN0NFNTY5NCIsInNpZCI6Ijg5ODU5RjRCMTU0NkJDRUYwRjBFMUMzOTM0RUEwOTRGIiwiaWF0IjoxNjM0NTYwMTgyLCJzY29wZSI6WyJhcGkxIiwiQXNDb2RlLlN0cy5BbGwiXSwiYW1yIjpbInB3ZCJdfQ.C7LG5iDPcbspvoH_qPMZjk_Lp9Poc2C42VTNhwh8xuxlBOoXDUce4Egfq_iz5OdillpvMzToEM4AKoFBgHoWR_09Mwv1gTFGYGp53-_lxFQIAIx1CSZRsxyBBn7RPPMxmRPyuWiLW83-Q8feIdLLOAkNjyExrGTMlZ7CkRIMPR7XBIVJlUSadsXl3XwtoS_6dXaqr-12w5n3D6-ur7ljjsRA32te_T2cNIvzcfFGLLs3evdyYBn1m-56KFi7RyMSF3xGjVtZ20ONNje4Eepb1Qm2IeNEggz9a1fySEvFTLjXyY_cb6iGNaYUiLASRdBTY02t6Ik0aWnICkw-0m-jhg",

  },
  "method": "GET",

});
```

The process can be tested from a desktop/mobile application:
<https://github.com/IdentityModel/IdentityModel>
<https://github.com/IdentityModel/IdentityModel.OidcClient>

### With back channel

Microsoft.AspNetCore.Authentication.OpenIdConnect

## Implicit Flow

The defining characteristic of the implicit grant is that tokens are returned directly from the /authorize endpoint instead of the /token endpoint.

Configure client

```sh
new Client
 {
     ClientId = "js",
     ClientName = "JavaScript Client",
     AllowedGrantTypes = GrantTypes.Implicit,
     RequireClientSecret = false,
     AllowAccessTokensViaBrowser = true,
     RedirectUris =           { "https://localhost:3001/home" },
     PostLogoutRedirectUris = { "https://localhost:3001/home" },
     AllowedCorsOrigins =     { "https://localhost:3001" },
     RequirePkce= false,
     AllowedScopes =
     {
         "api1",
     }
 }

```

Redirect to login:

```sh
fetch("https://localhost:6001/connect/authorize?
client_id=js
&redirect_uri=https%3A%2F%2Flocalhost%3A3001%2Fhome
&response_type=token
&scope=api1"
, {
  "method": "GET",
});

```

The identity server redirect the user to client application with an access token

```sh
https://localhost:3001/home#access_token=eyJhbGciOiJSUzI1NiIsImtpZCI6IkREOThGMjg4NzRDNzQ5RDIzMUI4NjRGNUM3QzI0NkYxIiwidHlwIjoiYXQrand0In0.eyJuYmYiOjE2MzQ1NjQxNDEsImV4cCI6MTYzNDU2Nzc0MSwiaXNzIjoiaHR0cHM6Ly9sb2NhbGhvc3Q6NjAwMSIsImF1ZCI6WyJBc0NvZGUuU3RzIiwiYXBpMSJdLCJjbGllbnRfaWQiOiJqcyIsInN1YiI6IjgxODcyNyIsImF1dGhfdGltZSI6MTYzNDU2Mzc2MywiaWRwIjoibG9jYWwiLCJqdGkiOiI2Rjk3MzRENTVFQ0FCQ0QyNEE3ODg1OEVDRjhBMjVEMCIsInNpZCI6IkQ5ODg1NzJGQzgwNzQyQzNGRTkzQkE0MUE3N0QxMUQyIiwiaWF0IjoxNjM0NTY0MTQxLCJzY29wZSI6WyJhcGkxIiwiQXNDb2RlLlN0cy5BbGwiXSwiYW1yIjpbInB3ZCJdfQ.mFoaW0V3x14qDz59A2_lbUxVM3MDiAWIB8T8Lk_5tki-rAdx1BVwPdnuEXzgvNOiYA1eguWhRiiIqsGtTtXf3dDcmLHHgdpQetPDWLwVWTEHCTxHk2DdDx3BwLee89qgpauURUKbb4MqCQYt8mHidr_BSWauXJzRRlI19iV_JLDmLCLTNK1RrXAGs0Ebz03l6rwJKk9S11IDr9qyQdNGsgqg9tgcd8sT1rKyODsoHQMtk6KmLn67xXet9FXDFFb33jONx0WqlSpkRsaXKyPAXOjW-TVN0kY5HPTh6HsGA8i72BKLGlGOW0KRuURuORqYOc3cZEQLQhrBR_BQWvkjVw
&token_type=Bearer
&expires_in=3600
&scope=api

```

## Hybrid Flow

Access tokens are a bit more sensitive than identity tokens, and we don’t want to expose them to the “outside” world if not needed. OpenID Connect includes a flow called “Hybrid Flow” which gives us the best of both worlds, the identity token is transmitted via the browser channel, so the client can validate it before doing any more work. And if validation is successful, the client opens a back-channel to the token service to retrieve the access token.

In hybrid flow, we can request combinations of code, id_token and token. There are three combinations for hybrid flow defined in OIDC specification.
response type = code token
response type = code id_token
response type = code id_token token

```csharp
new Client
{
    ClientId = "mvc",
    ClientName = "MVC Client",
    AllowedGrantTypes = GrantTypes.HybridAndClientCredentials,

    ClientSecrets =
    {
        new Secret("secret".Sha256())
    },

    RedirectUris           = { "http://localhost:5002/signin-oidc" },
    PostLogoutRedirectUris = { "http://localhost:5002/signout-callback-oidc" },

    AllowedScopes =
    {
        IdentityServerConstants.StandardScopes.OpenId,
        IdentityServerConstants.StandardScopes.Profile,
        "api1"
    },
    AllowOfflineAccess = true
};
```

```csharp
.AddOpenIdConnect("oidc", options =>
{
    options.SignInScheme = "Cookies";

    options.Authority = "http://localhost:5000";
    options.RequireHttpsMetadata = false;

    options.ClientId = "mvc";
    options.ClientSecret = "secret";
    options.ResponseType = "code id_token";

    options.SaveTokens = true;
    options.GetClaimsFromUserInfoEndpoint = true;

    options.Scope.Add("api1");
    options.Scope.Add("offline_access");
    options.ClaimActions.MapJsonKey("website", "website");
});
```

```csharp
<dt>access token</dt>
<dd>@await ViewContext.HttpContext.GetTokenAsync("access_token")</dd>

<dt>refresh token</dt>
<dd>@await ViewContext.HttpContext.GetTokenAsync("refresh_token")</dd>

public async Task<IActionResult> CallApiUsingUserAccessToken()
{
    var accessToken = await HttpContext.GetTokenAsync("access_token");

    var client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    var content = await client.GetStringAsync("http://localhost:5001/identity");

    ViewBag.Json = JArray.Parse(content).ToString();
    return View("json");
}
```

## Links

<https://github.com/IdentityServer/IdentityServer4.AspNetIdentity/blob/master/host/Quickstart/Account/AccountController.cs>
