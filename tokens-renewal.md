---
icon: arrows-rotate-reverse
---

# Tokens Renewal

You don't need to do it. The token refresh is handled automatically for you, however you can manually trigger a token refresh. &#x20;

{% tabs %}
{% tab title="Vanilla API" %}
```typescript
import { createOidc } from "oidc-spa";

const prOidc = await createOidc({ ... });

// Function to call when we want to renew the token
export function renewTokens(){

   const oidc = await prOidc;
   
   if( !oidc.isUserLoggedIn ){
      throw new Error("Logical error");
   }
   
   oidc.renewToken(
      // Optionally you can pass extra params that will be added 
      // to the body of the POST request to the openid-connect/token endpoint.
      // { extraTokenParams: { electedCustomer: "customer123" } }
      // This parameter can also be provided as parameter to the createOidc
      // function. See: https://github.com/keycloakify/oidc-spa/blob/59b8db7db0b47c84e8f383a86677e88e884887cb/src/oidc.ts#L153-L163
   );

}

// Subscribing to token renewal

prOidc.then(oidc => {
    if( !oidc.isUserLoggedIn ){
        return;
    }
    
    const { unsubscribe } = oidc.subscribeToTokensChange(tokens => {
       console.log("Token Renewed", tokens);
    });
    
    setTimeout(() => {
        // Call unsubscribe when you want to stop watching tokens change
        unsubscribe();
    }, 10_000);
});
```
{% endtab %}

{% tab title="React API" %}
```tsx
import { useOidc } from "./oidc";
import { decodeJwt } from "oidc-spa/tools/decodeJwt";

function DisplayAccessToken() {
    const { tokens, renewTokens } = useOidc({ assert: "user logged in" });

    if (tokens === undefined) {
        // NOTE: The tokens object is always initially undefined on first
        // render, the hook will imediately re-render automatically.
        return null;
    }

    return (
        <div>
            <h3>Access Token</h3>
            <pre>{JSON.stringify(decodeJwt(tokens.accessToken), null, 2)}</pre>
            <button onClick={() => renewTokens(
              // Optionally you can pass extra params that will be added 
              // to the body of the POST request to the openid-connect/token endpoint.
              // { extraTokenParams: { electedCustomer: "customer123" } }
              // This parameter can also be provided as parameter to the createOidc
              // function. See: https://github.com/keycloakify/oidc-spa/blob/59b8db7db0b47c84e8f383a86677e88e884887cb/src/oidc.ts#L153-L163      
            )}>Renew Tokens</button>
        </div>
    );
}
```

Outside of a React Component:&#x20;

```typescript
import { createReactOidc } from "oidc-spa/react";

const prOidc = await createReactOidc({ ... });

// Function to call when we want to renew the token
export function renewTokens(){

   const oidc = await prOidc;
   
   if( !oidc.isUserLoggedIn ){
      throw new Error("Logical error");
   }
   
   oidc.renewToken(
      // Optionally you can pass extra params that will be added 
      // to the body of the POST request to the openid-connect/token endpoint.
      // { extraTokenParams: { electedCustomer: "customer123" } }
      // This parameter can also be provided as parameter to the createOidc
      // function. See: https://github.com/keycloakify/oidc-spa/blob/59b8db7db0b47c84e8f383a86677e88e884887cb/src/oidc.ts#L153-L163
   );

}

// Subscribing to token renewal

prOidc.then(oidc => {
    if( !oidc.isUserLoggedIn ){
        return;
    }
    
    const { unsubscribe } = oidc.subscribeToTokensChange(tokens => {
       console.log("Token Renewed", tokens);
    });
    
    setTimeout(() => {
        // Call unsubscribe when you want to stop watching tokens change
        unsubscribe();
    }, 10_000);
});
```
{% endtab %}
{% endtabs %}
