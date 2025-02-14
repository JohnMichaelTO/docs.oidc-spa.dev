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

**https://**<mark style="color:blue;">**\<HYDRA\_DOMAIN>**</mark><mark style="color:purple;">**\<HYDRA\_RELATIVE\_PATH>**</mark>

* <mark style="color:blue;">**\<HYDRA\_DOMAIN>**</mark>: The domain where your Ory Hydra instance is hosted (e.g., **hydra.my-company.com**).
* <mark style="color:purple;">**\<HYDRA\_RELATIVE\_PATH>**</mark>: The subpath (if any) under which Hydra is accessible. In most deployments, this is an empty string (`""`), but if you’re using a reverse proxy or a custom path, it might be something like `"/oidc"`.\
  Hydra exposes its OpenID Connect configuration at:\
  `https://<HYDRA_DOMAIN><HYDRA_RELATIVE_PATH>/.well-known/openid-configuration`

### `clientId`

Unlike Keycloak, Ory Hydra is a headless OAuth2 provider and does not offer a built-in admin console for client registration. You must register your SPA as a client via the Hydra CLI or its REST API.

For example, using the Hydra CLI you can run:

```bash
hydra clients create \
  --id myapp \
  --redirect-uris "https://<APP_DOMAIN><BASE_URL>,http://localhost:<DEV_PORT><BASE_URL>" \
  --grant-types authorization_code,refresh_token \
  --response-types code
```

**Parameters explained:**

* <mark style="color:yellow;">**\<APP\_DOMAIN>**</mark>: Your application’s domain, e.g., **my-company.com** or **app.my-company.com**.\
  🔹 To avoid issues with [third-party cookie deprecation](end-of-third-party-cookies.md), ensure that your app’s domain and Hydra’s domain share the same root domain when necessary.
* <mark style="color:red;">**\<BASE\_URL>**</mark>: The base URL of your SPA, for example, **"/"** or **"/dashboard/"**.
* **\<DEV\_PORT>**: The development port (e.g., **5173** for Vite).

Once registered, the client’s unique identifier (the `clientId`) will be used in your `oidc-spa` configuration.

***

## Token Lifespan & Session Considerations

Infer from Keycloak documentation:

{% content-ref url="keycloak-configuration.md" %}
[keycloak-configuration.md](keycloak-configuration.md)
{% endcontent-ref %}

