---
icon: flag-checkered
description: Let's get your App authenticated!
---

# Basic Usage

Before getting started, you need to get a hold on the two parameters required to connect to your OIDC server, the **issuerUri** and the **clientId**.  \
Find instruction on how to configure your OIDC server on the following documentation page:

{% content-ref url="resources/oidc-server-configuration.md" %}
[oidc-server-configuration.md](resources/oidc-server-configuration.md)
{% endcontent-ref %}

{% tabs %}
{% tab title="Vanilla API" %}
```typescript
import { createOidc } from "oidc-spa";

const oidc = await createOidc({
    issuerUri: "https://auth.your-domain.net/realms/myrealm",
    clientId: "myclient",
    /**
     * Vite:  `homeUrl: import.meta.env.BASE_URL`
     * CRA:   `homeUrl: process.env.PUBLIC_URL`
     * Other: `homeUrl: "/"` (Usually)
     */
    homeUrl: import.meta.env.BASE_URL
});

if (!oidc.isUserLoggedIn) {
    // The user is not logged in.

    // We can call login() to redirect the user to the login/register page.
    // This return a promise that never resolve. 
    oidc.login({
         /** 
          * If you are calling login() in the callback of a click event
          * set this to false.  
          */
         doesCurrentHrefRequiresAuth: false
         /** 
          * Optionally, you can add some extra parameter 
          * to be added on the login url.  
          * (Can also be a parameter of createOidc `extraQueryParams: ()=> ({ ui_locales: "fr" })`)
          */
         //extraQueryParams: { kc_idp_hint: "google", ui_locales: "fr" }
         /**
          * You can allso set where to redirect the user after 
          * successful login
          */
          // redirectUrl: "/dashboard"
          
          /**
           * Keycloak: You can also send the users directly to the register page
           * see: https://github.com/keycloakify/oidc-spa/blob/14a3777601c50fa69d1221495d77668e97443119/examples/tanstack-router-file-based/src/components/Header.tsx#L54-L66
           */ 
    });

} else {
    // The user is logged in.

    const {
        // The accessToken is what you'll use as a Bearer token to 
        // authenticate to your APIs
        accessToken
    } = await oidc.getTokens_next();

    fetch("https://api.your-domain.net/orders", {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    })
     .then(response => response.json())
     .then(orders => console.log(orders));

    // To call when the user click on logout.
    // You can also redirect to a custom url with 
    // { redirectTo: "specific url", url: "/bye" }
    oidc.logout({ redirectTo: "home" });
    
    const decodedIdToken = oidc.getDecodedIdToken();

    // If you are wondering why ther's a decodedIdToken and no
    // decodedAccessToken read this: https://docs.oidc-spa.dev/resources/jwt-of-the-access-token
    console.log(`Hello ${decodedIdToken.preferred_username}`);

    // Note that in this example the decodedIdToken is not typed.  
    // What is inside the idToken is defined by the OIDC server you are using
    // and the scopes you are requesting if you want to specify the type of the 
    // decodedIdToken you can do:
    //
    // import { z } from "zod";
    // export const { useOidc } = createUseOidc({
    //    ...
    //    decodedIdTokenSchema: z.object({
    //        sub: z.string(),
    //        preferred_username: z.string(),
    //        // ... other properties
    //    })
    // })

}
```
{% endtab %}

{% tab title="React API" %}
This piece of code should give you the necessary information to understand how oidc-spa can be used inside your react components.  \
To go further you can refer to the examples setup to see how to integrate oidc-spa with your routing library: &#x20;

* [react-router-dom example setup](example-setups/react-router.md)
* [@tanstack/react-router example setup](example-setups/tanstack-router.md)

```tsx
import { createReactOidc } from "oidc-spa/react";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    // NOTE: If you don't have the params right away see note below.
    issuerUri: "https://auth.your-domain.net/realms/myrealm",
    clientId: "myclient",
    /**
     * Vite:  `homeUrl: import.meta.env.BASE_URL`
     * CRA:   `homeUrl: process.env.PUBLIC_URL`
     * Other: `homeUrl: "/"` (Usually)
     */
    homeUrl: import.meta.env.BASE_URL
});

ReactDOM.createRoot(document.getElementById("root")!).render(
    <OidcProvider
        // Optional, it's usually so fast that a fallback is really not required.
        fallback={<>Checking authentication ⌛️</>}
    >
        <App />
    </OidcProvider>
);

function App() {
    const { isUserLoggedIn } = useOidc();

    return (
        <div>
            {isUserLoggedIn ? <HeaderLoggedIn /> : <HeaderNotLoggedIn />}
            {isUserLoggedIn && <OrderHistory />}
        </div>
    );
}

function HeaderLoggedIn() {
    const { logout, decodedIdToken } = useOidc({ assert: "user logged in" });

    return (
        <div>
            {/* Note: The decodedIdToken can be typed and validated with zod See: https://github.com/keycloakify/oidc-spa/blob/e0914379e2f831161d52c858dccbfcdc1ed4f1e9/examples/tanstack-router-file-based/src/oidc.tsx#L33-L65 */}
            <span>{`Hello ${decodedIdToken.preferred_username}`}</span>
            <button onClick={() => logout({ redirectTo: "home" })}>Logout</button>
        </div>
    );
}

function HeaderNotLoggedIn() {
    const { login } = useOidc({ assert: "user not logged in" });

    return (
        <div>
            <button
                onClick={() =>
                    login({
                        /**
                         * If you are calling login() in the callback of a button click
                         * (like here) set this to false.
                         */
                        doesCurrentHrefRequiresAuth: false
                        /**
                         * Optionally, you can add some extra parameter
                         * to be added on the login url.
                         * (Can also be a parameter of createReactOidc `extraQueryParams: ()=> ({ ui_locales: "fr" })`)
                         */
                        //extraQueryParams: { kc_idp_hint: "google", ui_locales: "fr" }
                        /**
                         * You can also set where to redirect the user after
                         * successful login, by default it will be on the current location.
                         */
                        // redirectUrl: "/dashboard"
                    })
                }
            >
                Login
            </button>
            {/* If you are using Keycloak you can also implement a register button. See: https://github.com/keycloakify/oidc-spa/blob/efc0380e775d29de76cf412f0a1369353396f621/examples/tanstack-router-file-based/src/components/Header.tsx#L70-L85 */}
        </div>
    );
}

import { useEffect, useState } from "react";

type Order = {
    id: number;
    name: string;
};

export function OrderHistory() {
    const [orders, setOrders] = useState<Order[] | undefined>(undefined);

    useEffect(() => {
        fetchWithAuth("https://api.your-domain.net/orders", {
            headers: {
                "Content-Type": "application/json"
            }
        })
            .then(response => response.json())
            .then(orders => setOrders(orders));
    }, []);

    if (orders === undefined) {
        return <>Loading orders ⌛️</>;
    }

    return (
        <ul>
            {orders.map(order => (
                <li key={order.id}>{order.name}</li>
            ))}
        </ul>
    );
}

const fetchWithAuth: typeof fetch = async (input, init) => {
    const oidc = await getOidc();

    if (!oidc.isUserLoggedIn) {
        throw new Error("Should not be called in this context");
    }

    const { accessToken } = await oidc.getTokens();

    return fetch(input, {
        ...init,
        headers: {
            ...init?.headers,
            Authorization: `Bearer ${accessToken}`
        }
    });
};
```

If you get your OIDC parameters from an API you can pass an assync function that returns the oidc parameters. This function gets called when `<OidcProvider />` is first mounted or when `getOidc()` is first called.

```typescript
export const { 
  OidcProvide, 
  useOidc, 
  getOidc 
} = createReactOidc(async ()=> {

    const { 
        issuerUri, 
        clientId 
    } = await fetch("/api/oidc-params").then(r => r.json());

    return {
        issuerUri,
        clientId,
        homeUrl: import.meta.env.BASE_URL
    };
    
});
```
{% endtab %}
{% endtabs %}
