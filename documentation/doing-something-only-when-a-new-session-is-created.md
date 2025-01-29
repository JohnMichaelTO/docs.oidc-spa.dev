---
icon: square-caret-up
---

# Doing Something Only When a New Session is Created

In some cases, you might want to perform some operation to initialize the user's session. This could involve calling a special API endpoint or clearing some cached values in the local storage.\
What you don't want, however, is to run this every time the user refreshes the page or when their session is restored.\
To help you determine if the session should be initialized, you can leverage the `isNewBrowserSession` property that is available when the user is logged in.

There are three possible values for the `authMethod` property:

{% tabs %}
{% tab title="Vanilla API" %}
```typescript
import { createOidc } from "oidc-spa";

const oidc = await createOidc({ /* ... */ });

if (oidc.isUserLoggedIn && oidc.isNewBrowserSession) {
  // Do something related to initialization or clearing cache
}
```


{% endtab %}

{% tab title="React API" %}
{% code title="src/oidc.ts" %}
```typescript
import { createReactOidc } from "oidc-spa/react";

export const {
    /* ... */
    getOidc
} = createReactOidc({ /* ... */ });

getOidc().then(oidc => {

    if( !oidc.isUserLoggedIn ){
        return;
    }

    if( oidc.isNewBrowserSession ){
        // Do something related to initialization or clearing cache
    }

});
```
{% endcode %}

You can also do this in your React component (although it's maybe not the best approach)

```tsx
import { useOidc } from "./oidc";
import { useEffect } from "react";

function MyComponent(){

    const { isUserLoggedIn, authMethod } = useOidc();
    
    useEffect(()=> {
    
        // Warning! In dev mode, when React Strict Mode is enabled
        // this will be called twice!
        
        if( !isUserLoggedIn ){
            return;
        }
        
        if( authMethod === "back from auth server" ){
           // Do something related to initialization or clearing cache
        }
    
    }, []);

}
```
{% endtab %}
{% endtabs %}

Note that this is referring to the browser session and not the OIDC session on the server side.

If you want to perform an action only when a new OIDC session is created you can test `oidc.isNewBrowserSession && oidc.backFromAuthServer !== undefined`
