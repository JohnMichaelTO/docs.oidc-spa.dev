---
icon: sliders
---

# Ory Hydra Configuration

## Getting the `issuerUri` and `clientId`

`oidc-spa` requires two parameters to connect to your Ory Hydra instance: `issuerUri` and `clientId`.

{% code title="Example configuration" %}
```typescript
const { ... } = createOidc({
    issuerUri: "...",
    clientId: "...",
    // ...
});
```
{% endcode %}

### `issuerUri`

In Ory Hydra, the OIDC issuer URI is the public URL where Hydra’s OpenID Connect configuration is available. It typically follows this format:

**https://**<mark style="color:blue;">**\<HYDRA_DOMAIN>**</mark><mark style="color:purple;">**\<HYDRA_RELATIVE_PATH>**</mark>

- <mark style="color:blue;">**\<HYDRA_DOMAIN>**</mark>: The domain where your Ory Hydra instance is hosted (e.g., **hydra.my-company.com**).
- <mark style="color:purple;">**\<HYDRA_RELATIVE_PATH>**</mark>: The subpath (if any) under which Hydra is accessible. In most deployments, this is an empty string (`""`), but if you’re using a reverse proxy or a custom path, it might be something like `"/oidc"`.  
  Hydra exposes its OpenID Connect configuration at:  
  `https://<HYDRA_DOMAIN><HYDRA_RELATIVE_PATH>/.well-known/openid-configuration`

### `clientId`

Unlike Keycloak, Ory Hydra is a headless OAuth2 provider and does not offer a built-in admin console for client registration. You must register your SPA as a client via the Hydra CLI or its REST API.

For example, using the Hydra CLI you can run:

```bash
hydra clients create \
  --id <mark style="color:yellow;">myapp</mark> \
  --secret <mark style="color:gray;">(optional for public clients)</mark> \
  --redirect-uris "https://<mark style="color:orange;">\<APP_DOMAIN>**</mark><mark style="color:red;">**\<BASE_URL>**</mark>,http://localhost:<DEV_PORT>/" \
  --grant-types authorization_code,refresh_token \
  --response-types code
```

**Parameters explained:**

- <mark style="color:yellow;">**\<APP_DOMAIN>**</mark>: Your application’s domain, e.g., **my-company.com** or **app.my-company.com**.  
  🔹 To avoid issues with [third-party cookie deprecation](end-of-third-party-cookies.md), ensure that your app’s domain and Hydra’s domain share the same root domain when necessary.
- <mark style="color:red;">**\<BASE_URL>**</mark>: The base URL of your SPA, for example, **"/"** or **"/dashboard/"**.
- **\<DEV_PORT>**: The development port (e.g., **5173** for Vite).

Once registered, the client’s unique identifier (the `clientId`) will be used in your `oidc-spa` configuration.

---

## Token Lifespan & Session Considerations

Ory Hydra is a pure OAuth2/OpenID Connect server and does **not** manage user sessions or login pages directly. Instead, it issues access tokens and refresh tokens with configurable lifespans. These tokens are used by your SPA to access protected resources.

{% hint style="info" %}
**Note:**  
The **access token lifetime** is typically short (e.g., 5 minutes) to minimize security risks if a token is compromised. The **refresh token lifetime** can be longer (e.g., 14 days or more) to allow seamless re-authentication while still enforcing periodic logins.
{% endhint %}

### For Security-Sensitive Apps

For applications such as banking or admin panels, you might want to enforce strict token lifespans:

- **Access Token:** Keep it very short (e.g., **5 minutes**) to limit exposure.
- **Refresh Token:** Limit its lifetime (e.g., **14 days**) so that users must re-authenticate if inactive for an extended period.

These values are typically set in Hydra’s configuration (or via environment variables like `OAUTH2_ACCESS_TOKEN_LIFETIME` and `OAUTH2_REFRESH_TOKEN_LIFETIME`). Refer to the [Ory Hydra documentation](https://www.ory.sh/hydra/docs) for details on how to adjust these settings.

### For Less Sensitive Apps

For applications where user convenience is paramount (e.g., e-commerce or social media):

- **Access Token:** Can remain short-lived (e.g., **5 minutes**) to maintain security.
- **Refresh Token:** May be extended (e.g., up to **365 days**) to allow users to remain logged in longer without frequent re-authentication.

Remember, because Hydra is stateless regarding user sessions, **any session management (like “Remember Me” functionality) must be implemented in your frontend or via an external identity provider.**

---

## User Account Management

Ory Hydra focuses solely on OAuth2 and OpenID Connect protocols. It does **not** handle user accounts, authentication screens, or account deletion. These aspects must be managed by an external identity provider (such as [Ory Kratos](https://www.ory.sh/kratos/)) or your own user management system.

If you plan to implement features like a "delete account" button, ensure that your chosen identity system supports it.

---

## 📌 Additional Notes

- **Token Lifetimes:**  
  [^1] The **access token lifetime** is usually short (e.g., 5 minutes) to reduce the window of vulnerability if a token is leaked.  
  [^2] The **refresh token lifetime** should balance convenience and security (e.g., 14 days for sensitive apps, up to 365 days for less critical applications).

- **Hydra’s Role:**  
  Ory Hydra is designed to be integrated into a broader identity and access management system. It handles OAuth2 flows but delegates user authentication and account management to external systems.

---

This configuration guide should help you set up your SPA with Ory Hydra using `oidc-spa`, ensuring you have a secure and well-integrated OAuth2/OpenID Connect flow.

For further details, refer to the official [Ory Hydra documentation](https://www.ory.sh/hydra/docs). Enjoy configuring your secure applications! 🚀
