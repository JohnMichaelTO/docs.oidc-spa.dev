---
icon: split
---

# Multi Providers - Login with Google or Microsoft

The example setup is live here: [https://example-multi-providers.oidc-spa.dev/](https://example-multi-providers.oidc-spa.dev)

Run it locally with: &#x20;

```bash
git clone https://github.com/keycloakify/oidc-spa
mv oidc-spa/examples/multi-providers oidc-spa-multi-providers
rm -rf oidc-spa
cd oidc-spa-multi-providers
# NOTE: With this example only internat Insee Microsoft account can sign in.
# You can however signin with your personal Google Account.
cp .env.local.sample .env.local
yarn
yarn dev
```

{% embed url="https://github.com/keycloakify/oidc-spa/tree/main/examples/multi-providers" %}

