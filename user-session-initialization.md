---
icon: square-caret-up
---

# User Session Initialization

In some cases, you might want to perform some operation to initialize the user's session. This could involve calling a special API endpoint or clearing some cached values in the local storage.\
What you don't want, however, is to run this every time the user refreshes the page.  \
To help you determine if the session should be initialized, you can leverage the `isNewBrowserSession` property that is available when the user is logged in.

{% tabs %}
{% tab title="Vanilla API" %}
```typescript
import { createOidc } from "oidc-spa";

const oidc = await createOidc({ /* ... */ });

if (oidc.isUserLoggedIn && oidc.isNewBrowserSession) {
  // This is a new visit of this user.
}

if(oidc.isUserLoggedIn && oidc.isNewBrowserSession && oidc.backFromAuthServer ){
  // This is a new visit AND a new OIDC session has just beeing created.
  // on the OIDC server.
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
  
    if (oidc.isUserLoggedIn && oidc.isNewBrowserSession) {
      // This is a new visit of this user.
    }
    
    if(oidc.isUserLoggedIn && oidc.isNewBrowserSession && oidc.backFromAuthServer ){
      // This is a new visit AND a new OIDC session has just beeing created.
      // on the OIDC server.
    }

});
```
{% endcode %}

You can also do this in your React component (although it's maybe not the best approach)

```tsx
import { useOidc } from "./oidc";
import { useEffect } from "react";

function MyComponent(){

    const { isUserLoggedIn, isNewBrowserSession, backFromAuthServer } = useOidc();
    
    useEffect(()=> {
    
        // Warning! In dev mode, when React Strict Mode is enabled
        // this will be called twice!
        
        if (isUserLoggedIn && isNewBrowserSession) {
          // This is a new visit of this user.
        }
        
        if(isUserLoggedIn && isNewBrowserSession && backFromAuthServer ){
          // This is a new visit AND a new OIDC session has just beeing created.
          // on the OIDC server.
        }
    
    }, []);


```
{% endtab %}
{% endtabs %}
