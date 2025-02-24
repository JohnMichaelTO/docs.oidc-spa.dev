---
icon: microsoft
description: >-
  Formerly Azure Active Directory, enable "Sign In with Microsoft Entreprise
  Account, Xbox, Skype, Office 360,..."
---

# Microsoft (Entra ID)

## Making Entra ID issue a JWT Access Token

This step aims at configuring Entra ID so that it issues a JWT Access Token. &#x20;

By default Entra ID issues opaque Access Tokens that can only be validated by your backend calling the Microsoft Graph API. &#x20;

If you want to be able read and validate the access token on the backen in a non vendor locked in way as shown in [the Web API section of this website](../web-api.md), you have to configure a custom scope.

* Navigate to [https://portal.azure.com/](https://portal.azure.com/)
* In the left pannel select "Microsoft Entra ID"
* In the left bar "Manage > App Registrations"
* Click on "New Registration"
* Name "My App - API", Click Register.
* Supported Account Type: It's up to you.
* In the left menu navigate to "Manage > Expose API"
* Click "Add a scope"
* Application ID URI: "api://my-app-api", Save and continue
* Configure the scope as follow then click "Add Scope"
  * Scope name: "access\_as\_user"
  * Who can consent: Admins and Users
  * Admin Consent Display Name: "JWT Access Token"
  * Admin consent description: "Ensure issuance of a JWT Access Token"
  * User Consent Display Name: "View your basic profile"
  * User consent description: "Allows the app to see your basic profile (e.g., name, picture, user name, email address)"
  * State: Enabled

On the backend to validate the token you should check that the aud claim of the JWT of the access token is "api://my-app-api"

## Registering your Application

* Navigate to [https://portal.azure.com/](https://portal.azure.com/)
* In the left pannel select "Microsoft Entra ID"
* In the left bar "Manage > App Registrations"
* Click on "New Registration" again
* Display Name "My App" (Put the actuall name of your app)
* Supported Account Type: It's up to you to decide
* Click on Register
* Click on "Add a Redirect URI"
* Click on "Add Platform"
* Click on "Single-Page application"
* Redirect URIs: **https://my-app.com/** (Ensure the trailing slash is included. If your app is hosted under a subpath like /dasboard enter https://my-app.com/dashboard/)
* Front-channel logout URL: Leave empty
* Check both "Access Token" and "ID Token"
* Click Save
* Click on "Add URI" and enter **http://localhost:5173/** (Ensure the trailing slash is included, 5173 is the default port that the Vite dev server uses, adapt to your setup)
* Cick Save
* In the Left Pannel, click on "API Permission"
* Click "Add a permission"
* Click "APIs My Organization uses"
* In the list click on "My App - API"
* Check "access\_as\_user"
* Click "Add permission
* In the left pannel click on "Overview" and copy the **Application (client) ID** and **Directory (tenant) ID** thoses are the two parameters you will need to configure oidc-spa.

## Configuring oidc-spa

{% tabs %}
{% tab title="Vanilla" %}
```typescript
import { createOidc } from "oidc-spa";

// Directory (tenant) ID:
const directoryId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
// Application (client) ID:
const clientId= "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

export const prOidc = createOidc({
    issuerUri: `https://login.microsoftonline.com/${directoryId}/v2.0`,
    clientId,
    scopes: ["profile", "api://my-app-api/access_as_user"],
    homeUrl: import.meta.env.BASE_URL
});
```
{% endtab %}

{% tab title="React" %}
```typescript
import { createReactOidc } from "oidc-spa/react";

// Directory (tenant) ID:
const directoryId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
// Application (client) ID:
const clientId= "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    issuerUri: `https://login.microsoftonline.com/${directoryId}/v2.0`,
    clientId,
    scopes: ["profile", "api://my-app-api/access_as_user"],
    homeUrl: import.meta.env.BASE_URL
});
```
{% endtab %}
{% endtabs %}

## Testing

```bash
git clone https://github.com/keycloakify/oidc-spa
mv oidc-spa/examples/tanstack-router-file-based oidc-spa-tanstack-router
rm -rf oidc-spa
cd oidc-spa-tanstack-router
cp .env.local.sample .env.local
# Here, uncomment the Microsoft Entra ID section and comment the Keycloak section
# in the .env.local file.
yarn
yarn dev
```
