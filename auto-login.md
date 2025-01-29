---
icon: shield
description: Enforce authentiaction everywhere on your app
---

# Auto Login

If there is no part of your app that can be browsed without being logged which is typically the case for application like dashboard or administration pannel, you can make oidc-spa automatically redirect  users to the login pages when they are not authenticated. &#x20;

In this mode you **don't** have to:

* Check `isUserLoggedIn`, it will always be true.&#x20;
* (React) Use `useOidc({ assert: "user logged in" })` you know that's the case.

{% tabs %}
{% tab title="Vanilla API" %}
```typescript
import { createOidc, type OidcInitializationError } from "oidc-spa";

const oidc = await createOidc({
    // ...
    autoLogin: true,
    // Optional, the default value is: location.href (here)
    // postLoginRedirectUrl: "/dashboard"
})
.catch((error: OidcInitializationError) => {
    // Here you need to handle the potential initialization error
    // because in this mode we cannot fallback to the user being
    // not authenticated if we ran into an error.

    if( !error.isAuthServerLikelyDown ){
        // This mean that there is an issue in the configuration
        // of your OIDC server.
        throw error;
    }

    alert("Sorry our authentication server is down, try again later");

    return new Promise<never>(() => {});

});

```
{% endtab %}

{% tab title="React API" %}
{% code title="src/oidc.ts" %}
```typescript
import { createReactOidc } from "oidc-spa/react";

export const {
    OidcProvider,
    useOidc,
    prOidc
} = createReactOidc({
   // ...
   autoLogin: true,
   // Optional, the default value is: location.href (here)
   // postLoginRedirectUrl: "/dashboard"
});
```
{% endcode %}

{% code title="src/main.tsx" %}
```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { OidcProvider } from "oidc";

const router = createRouter({ routeTree });

ReactDOM.createRoot(document.getElementById("root")!).render(
    <React.StrictMode>
        <OidcProvider
            ErrorFallback={({ initializationError }) => (
                <h1 style={{ color: "red" }}>
                    {initializationError.isAuthServerLikelyDown ? (
                        <>Sorry our authentication server is currently down, please try again later</>
                    ) : (
                        // NOTE: Check initializationError.message for debug information.
                        // It's an error on your end no need to show it to the user.
                        <>Unexpected authentication error </>
                    )}
                </h1>
            )}
        >
            <RouterProvider router={router} />
        </OidcProvider>
    </React.StrictMode>
);

```
{% endcode %}
{% endtab %}
{% endtabs %}
