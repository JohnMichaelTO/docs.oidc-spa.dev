---
icon: shield-quartered
---

# Auth0

{% code title="Example config matching the screenshots" %}
```typescript
const { ... } = createOidc({
    // Referred to as "Domain" in Auth0 terminology:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DkoDtyQkYj4UPk3gKk4MjtnD9nskUhpt",
    extraQueryParams: {
       // Custom API Identifier
       audience: "https://oidc-spa-example"
    }
});
```
{% endcode %}
