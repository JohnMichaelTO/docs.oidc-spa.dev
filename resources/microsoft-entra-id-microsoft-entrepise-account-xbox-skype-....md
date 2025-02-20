---
icon: microsoft
description: >-
  Formerly Azure Active Directory, enable "Sign In with Microsoft Entreprise
  Account, Xbox, Skype, Office 360,..."
---

# Microsoft Entra ID: Microsoft Entrepise Account, Xbox, Skype, ...

## Making Entra ID issue an OIDC Compliant Access Token

This step aims at configuring Entra ID so that it issues oidc compliant JWT Access Token. &#x20;

By default Entra ID issues opaque Access Tokens that can only be validated by your backend calling the Microsoft Graph API. &#x20;

If you are using this library instead of MSAL.js, chances are you see the value in building software that is not vendor locked-in to any specific solution and you probably want to be able to build an API as described in this guide that only&#x20;

This first step is not strictly nessesary for the authentication process to succees, however, if you want your backend to be able to validate the Access Token that you will use as Authorization Bearer in your request to your backend API whithout having to call the Microsoft Graph API [as described in this section](../web-api.md), it is mandatory. &#x20;

Chances are, if you are using this library instead of MSAL.js that you are seeing the value of not having your code vendor locked in to any specific OIDC Provider so this is probably what you want to do. Let's procceed:

* Navigate to [https://portal.azure.com/](https://portal.azure.com/)
* In the left pannel select "Microsoft Entra ID"
* In the left bar "Manage > App Registrations"
* Click on "New Registration"
* Name "OpenID Connect Standard APIs", Click Register.
* Supported Account Type: "Accounts in any organizational directory (Any Microsoft Entra ID tenant - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)"
* In the left menu navigate to "Manage > Expose API"
* Click "Add a scope"
* Application ID URI: "api://oidc", Save and continue
* Configure the scope as follow then click "Add Scope"
  * Scope name: "jwt-access-token"
  * Who can consent: Admins and Users
  * Admin Consent Display Name: "OIDC-compliant Access Token"
  * Admin consent description: "Enables the issuance of an OIDC-compliant access token. By default, Entra ID issues opaque access tokens that require validation via the Graph API. With this scope, the access token will be a standard JWT containing user information, signed by Microsoft’s public keys, and verifiable independently of Microsoft’s APIs."
  * User Consent Display Name: "Read your identity"
  * User consent description: "Allow the app to verify your identity in order to sign you in."
  * State: Enabled
* Go Back to "App Registrations"
* Click on "New Registration" again
* Display Name "My App" (Put the actuall name of your app)
* Supported Account Type: It's up to you to decide
* Click on Register
* Click on "Add a Redirect URI"
* Click on "Add Platform"
* Click on "Single-Page application"
* Redirect URIs: **https://my-app.com/** (Ensure the trailing slash is included. If your app is hosted under a subpath like /dasboard enter https://my-app.com/dashboard/)
* Front-channel logout URL: Same as Redirect URI
* Check both "Access Token" and "ID Token"
* Click Save
* Click on "Add URI" and enter **http://localhost:5173/** (Ensure the trailing slash is included, 5173 is the default port that the Vite dev server uses, adapt to your setup)
* Cick Save
* In the Left Pannel, click on "API Permission"
* Click "Add a permission"
* Click "APIs My Organization uses"
* In the list click on "OpenID Connect Standard APIs"
* Check "jwt-access-token"
* Click "Add permission
* In the left pannel click on "Overview" and copy the Application (client) ID and Directory (tenant) ID thoses are the two parameters you will need to configure oidc-spa.\


d

## Registering your Application

* Navigate to [https://portal.azure.com/](https://portal.azure.com/)
* In the left pannel select "Microsoft Entra ID"
* In the left bar "Manage > App Registrations"
* Click on "New Registration"
* Name "OpenID Connect Standard APIs", Click Register.
* Supported Account Type: "Accounts in any organizational directory (Any Microsoft Entra ID tenant - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)"
* In the left menu navigate to "Manage > Expose API"
* Click "Add a scope"
* Application ID URI: "api://oidc", Save and continue
* Configure the scope as follow then click "Add Scope"
  * Scope name: "jwt-access-token"
  * Who can consent: Admins and Users
  * Admin Consent Display Name: "OIDC-compliant Access Token"
  * Admin consent description: "Enables the issuance of an OIDC-compliant access token. By default, Entra ID issues opaque access tokens that require validation via the Graph API. With this scope, the access token will be a standard JWT containing user information, signed by Microsoft’s public keys, and verifiable independently of Microsoft’s APIs."
  * User Consent Display Name: "Read your identity"
  * User consent description: "Allow the app to verify your identity in order to sign you in."
  * State: Enabled
* Go Back to "App Registrations"
* Click on "New Registration" again
* Display Name "My App" (Put the actuall name of your app)
* Supported Account Type: It's up to you to decide
* Click on Register
* Click on "Add a Redirect URI"
* Click on "Add Platform"
* Click on "Single-Page application"
* Redirect URIs: **https://my-app.com/** (Ensure the trailing slash is included. If your app is hosted under a subpath like /dasboard enter https://my-app.com/dashboard/)
* Front-channel logout URL: Same as Redirect URI
* Check both "Access Token" and "ID Token"
* Click Save
* Click on "Add URI" and enter **http://localhost:5173/** (Ensure the trailing slash is included, 5173 is the default port that the Vite dev server uses, adapt to your setup)
* Cick Save
* In the Left Pannel, click on "API Permission"
* Click "Add a permission"
* Click "APIs My Organization uses"
* In the list click on "OpenID Connect Standard APIs"
* Check "jwt-access-token"
* Click "Add permission
* In the left pannel click on "Overview" and copy the Application (client) ID and Directory (tenant) ID thoses are the two parameters you will need to configure oidc-spa.

## Configuring oidc-spa

{% tabs %}
{% tab title="Vanilla" %}
```typescript
import { createOidc } from "oidc-spa";

// Directory (tenant) ID:
const directoryId = "71a0a621-363a-4182-8209-86364aa6de03";
// Application (client) ID:
const clientId= "2bf08137-c88d-4e92-9e1d-fcfa528294e6";

export const prOidc = createOidc({
    issuerUri: `https://login.microsoftonline.com/${directoryId}/v2.0`,
    clientId,
    scopes: ["profile", "api://oidc/jwt-access-token"],
    homeUrl: import.meta.env.BASE_URL
});
```
{% endtab %}

{% tab title="React" %}
```typescript
import { createReactOidc } from "oidc-spa/react";

// Directory (tenant) ID:
const directoryId = "71a0a621-363a-4182-8209-86364aa6de03";
// Application (client) ID:
const clientId= "2bf08137-c88d-4e92-9e1d-fcfa528294e6";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    issuerUri: `https://login.microsoftonline.com/${directoryId}/v2.0`,
    clientId,
    scopes: ["profile", "api://oidc/jwt-access-token"],
    homeUrl: import.meta.env.BASE_URL
});
```
{% endtab %}
{% endtabs %}
