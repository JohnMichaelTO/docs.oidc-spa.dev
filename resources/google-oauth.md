---
icon: google
description: Implement "Login with Google"
---

# Google OAuth

With `oidc-spa`, you would typically use an OIDC Provider like Keycloak to centralize authentication and configure Google as an identity provider within Keycloak. This allows users to select "Google" as a login option.  

That being said, if you don't have a Keycloak instance, you can configure `oidc-spa` directly with Google, as demonstrated in the following video:  

{% embed url="https://youtu.be/d0RgnM4vXbc" %}

## Google Cloud Console Configuration

To set up authentication via Google, follow these steps in the **Google Cloud Console**:

1. Navigate to **Google Cloud Platform Console**.
2. Go to **API & Services** → **Credentials**.
3. Click **Create Credentials** → **OAuth Client ID**.
4. Choose **Application Type: Web Application**.
5. Set the **Authorized Redirect URIs**:
   - **https://my-app.com/** and **http://localhost:5173/** (Ensure the trailing slash is included).
   - If your app is hosted under a subpath (e.g., `/dashboard`), set:
     - **https://my-app.com/dashboard/**  
     - **http://localhost:5173/dashboard/**
   - `5173` is Vite's default development server port—adjust as needed.
6. Set the **Authorized JavaScript Origins** to match the origins of your redirect URIs.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
### Google's OAuth Design Flaw

Google's OAuth implementation has a significant flaw: **PKCE-based authentication fails unless a client secret is provided**.  

For public clients, storing secrets is inherently insecure. **PKCE (Proof Key for Code Exchange)** exists precisely to prevent code interception, and Google supports PKCE. **Requiring a client secret in addition to PKCE is unnecessary and misleading**.  

That said, **providing the client secret in your frontend code for this specific case has no security implications**. This is purely a poor API design decision on Google's part.  
{% endhint %}

---

## Implementation

Here’s how to configure `oidc-spa` to work with Google:

{% tabs %}
{% tab title="Vanilla" %}
```typescript
import { createOidc } from "oidc-spa";

export const prOidc = createOidc({
    issuerUri: "https://accounts.google.com",
    clientId: "928024164279-ifjvmsffi64slkk81h3gmoh7p03ev68k.apps.googleusercontent.com",
    homeUrl: import.meta.env.BASE_URL,
    // You are not at fault—Google forces public clients to provide a secret.
    // This is on them.
    __clientSecret_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: "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"
});
```
{% endtab %}

{% tab title="React" %}
```typescript
import { createReactOidc } from "oidc-spa/react";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    issuerUri: "https://accounts.google.com",
    clientId: "928024164279-ifjvmsffi64slkk81h3gmoh7p03ev68k.apps.googleusercontent.com",
    homeUrl: import.meta.env.BASE_URL,
    // You are not at fault—Google forces public clients to provide a secret.
    // This is on them.
    __clientSecret_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: "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"
});
```
{% endtab %}
{% endtabs %}

