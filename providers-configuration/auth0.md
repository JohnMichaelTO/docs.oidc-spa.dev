---
icon: shield-quartered
---

# Auth0 Integration Guide

This guide explains how to configure Auth0 to obtain the necessary parameters for setting up `oidc-spa`.

{% embed url="https://youtu.be/zPikliLzC84" %}

## Creating Your Application

1. Navigate to [Auth0 Dashboard](https://manage.auth0.com/dashboard).
2. In the left panel, go to **Applications → Applications**.
3. Click **Create Application**.
4. Select **Single Page Application** as the application type.
5. Navigate to the **Settings** tab to find the **Domain** and **Client ID**.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```typescript
const { ... } = createOidc({
    // Referred to as "Domain" in Auth0:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
});
```

## Creating an API

If you need Auth0 to issue a JWT access token for your API, follow these steps:

1. Navigate to [Auth0 Dashboard](https://manage.auth0.com/dashboard).
2. In the left panel, go to **Applications → APIs**.
3. Click **Create API**.
4. Configure the API:
   - **Identifier**: Ideally, use your API's root URL (e.g., `https://myapp.my-company.com/api`). However, this is just an identifier, so any unique string works.
   - Click **Save**.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```typescript
const { ... } = createOidc({
    // Referred to as "Domain" in Auth0:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
    extraQueryParams: {
       // Custom API Identifier
       audience: "https://app.my-company.com/api"
    }
});
```

## (Optional) Configuring a Custom Domain

It is **highly recommended** to [set up a custom domain in Auth0](#user-content-fn-1)[^1] to ensure Auth0 is not treated as a third-party service by browsers.

### Why Is a Custom Domain Important?

By default, Auth0 does not issue a refresh token. If your access token expires and you haven't configured a custom domain, `oidc-spa` will **force reload your app** to refresh the token, instead of doing it silently in the background.

Auth0 access tokens have a default validity of **24 hours**, so if you don’t modify this setting, you won’t notice page reloads. However, if your app requires shorter expiration times for security reasons, a custom domain is necessary.

### Configuring a Custom Domain in Auth0

1. Navigate to the [Auth0 Dashboard](https://manage.auth0.com/dashboard).
2. Click **Settings** in the left panel.
3. Open the **Custom Domain** tab.
4. Configure a custom domain (e.g., `auth.my-company.com`).
   - See [the end of third-party cookie page](../resources/end-of-third-party-cookies.md) for more details.

Once configured, use your custom domain as the `issuerUri`:

```diff
 const { ... } = createOidc({
-    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
+    issuerUri: "auth.my-company.com",
     clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
     extraQueryParams: {
         audience: "https://app.my-company.com/api"
     }
 });
```

## (Optional) Configuring Auto Logout

If you want users to be **automatically logged out** after a period of inactivity, follow these steps.

### Why Enable Auto Logout?

For **security-critical applications**, users should:

- Log in **on every visit**.
- Be **logged out after inactivity**[^2].

This prevents unauthorized access if a user steps away from their device.

### Configuring Session Expiration in Auth0

1. Navigate to [Auth0 Dashboard](https://manage.auth0.com/dashboard).
2. Click **Settings** in the left panel.
3. Open the **Advanced** tab.
4. Configure **Session Expiration**:
   - **Idle Session Lifetime**: `5 minutes` (300 seconds) – logs out inactive users.
   - **Maximum Session Lifetime**: `14 days` (20160 minutes) – ensures active users stay logged in.
5. Configure **Access Token Lifetime**:
   1. Go to **Applications → APIs**.
   2. Select your API (`My App - API` or the name used earlier).
   3. Open the **Settings** tab.
   4. Under **Access Token Settings**:
      - **Maximum Access Token Lifetime**: `4 minutes` (240 seconds) – should be **shorter** than the Idle Session Lifetime.
      - **Implicit/Hybrid Flow Access Token Lifetime**: `4 minutes` – required to save settings, even if unused.
   5. Click **Save**.

### Configuring `oidc-spa` for Auto Logout

Since Auth0 **does not issue refresh tokens** (or issues non-JWT ones), inform `oidc-spa` of your settings:

```typescript
const { ... } = createOidc({
    issuerUri: "auth.my-company.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
    extraQueryParams: {
       audience: "https://app.my-company.com/api"
    },
    idleSessionLifetimeInSeconds: 300
});
```

### (Optional) Implementing an Auto Logout Countdown

You can enhance user experience by displaying a countdown warning before logout:

{% content-ref url="../auto-logout.md" %}
[auto-logout.md](../auto-logout.md)
{% endcontent-ref %}

---

[^1]: Custom domains are available even under the free plan, but you must enter a credit card.

[^2]: `oidc-spa` considers a user inactive if they **do not** interact with the app (mouse movement, keyboard, or touch input) in **any tab** using the same SSO session.