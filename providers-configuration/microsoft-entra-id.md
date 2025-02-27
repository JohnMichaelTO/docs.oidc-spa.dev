---
icon: microsoft
description: Formerly Azure Active Directory
---

# Microsoft Entra ID

{% embed url="https://youtu.be/upcAmYq4JLY" %}

## Configuring Entra ID to Issue a JWT Access Token

By default, Entra ID issues opaque Access Tokens, which can only be validated by your backend via the Microsoft Graph API. 

To enable validation of access tokens in a non-vendor-locked way—such as demonstrated in [the Web API section](../web-api.md)—you need to configure a custom scope.

### Steps to Configure a Custom Scope

1. Go to [Microsoft Azure Portal](https://portal.azure.com/).
2. In the left panel, select **"Microsoft Entra ID"**.
3. Navigate to **"Manage > App Registrations"**.
4. Click **"New Registration"**.
5. Enter **"My App - API"** as the name, then click **Register**.
6. Set **Supported Account Type** to **Accounts in this organization**.
7. In the left menu, go to **"Manage > Expose API"**.
8. Click **"Add a scope"**.
9. Configure as follows, then click **"Add Scope"**:
   - **Application ID URI**: `api://my-app-api` (then save and continue)
   - **Scope name**: `access_as_user`
   - **Who can consent**: Admins and Users
   - **Admin Consent Display Name**: "JWT Access Token"
   - **Admin Consent Description**: "Ensure issuance of a JWT Access Token"
   - **User Consent Display Name**: "View your basic profile"
   - **User Consent Description**: "Allows the app to see your basic profile (e.g., name, picture, user name, email address)"
   - **State**: Enabled

### Validating the Token on the Backend

To validate the token on the backend, ensure that the `aud` claim in the JWT access token matches `api://my-app-api`. For more details, refer to the [Web API documentation](../web-api.md).

---

## Registering Your Application

1. Go to [Microsoft Azure Portal](https://portal.azure.com/).
2. In the left panel, select **"Microsoft Entra ID"**.
3. Navigate to **"Manage > App Registrations"**.
4. Click **"New Registration"**.
5. Enter **"My App"** as the display name (replace with your actual app name).
6. Set **Supported Account Type** to **Accounts in this organization**.
7. Click **Register**.
8. Click **"Add a Redirect URI"**.
9. Click **"Add Platform"** > **"Single-Page Application"**.
10. Set **Redirect URIs**:
    - **Production**: `https://my-app.com/` (include trailing slash; adjust if hosted under a subpath, e.g., `https://my-app.com/dashboard/`)
    - **Local Development**: `http://localhost:5173/` (include trailing slash; adjust based on your dev server)
11. Ensure **"Access Token"** and **"ID Token"** are checked.
12. Click **Save**.
13. In the left panel, go to **"API Permissions"**.
14. Click **"Add a permission"**.
15. Click **"APIs My Organization Uses"**.
16. Select **"My App - API"**.
17. Check **"access_as_user"**, then click **"Add permission"**.
18. In the left panel, click **"Overview"** and copy:
    - **Application (client) ID**
    - **Directory (tenant) ID**

These are required to configure `oidc-spa`.

---

## Configuring `oidc-spa`

{% tabs %}
{% tab title="Vanilla" %}
```typescript
import { createOidc } from "oidc-spa";

// Replace with your actual values
const directoryId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
const clientId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

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

// Replace with your actual values
const directoryId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
const clientId = "XXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    issuerUri: `https://login.microsoftonline.com/${directoryId}/v2.0`,
    clientId,
    scopes: ["profile", "api://my-app-api/access_as_user"],
    homeUrl: import.meta.env.BASE_URL
});
```
{% endtab %}
{% endtabs %}

---

## Testing the Setup

To test your configuration using `oidc-spa`:

```bash
git clone https://github.com/keycloakify/oidc-spa
mv oidc-spa/examples/tanstack-router-file-based oidc-spa-tanstack-router
rm -rf oidc-spa
cd oidc-spa-tanstack-router
cp .env.local.sample .env.local

# Uncomment the Microsoft Entra ID section and comment out the Keycloak section.
# Note: You cannot log in with a personal Microsoft account, so you may need 
# to adjust the .env.local file with your organization's configuration.

yarn
yarn dev
```
