---
icon: turtle
---

# Performance issues?

If you're experiencing performance issues—meaning that the `createOidc()` function takes too long to resolve or that your application remains stuck on the fallback of `<OidcProvider />` (e.g., `<>Checking authentication ⌛️</>`) for several hundred milliseconds—it may be due to your application's architecture. Specifically, if your application performs asynchronous operations before `oidc-spa` is imported, the silent sign-in process (executed in an iframe) can be delayed.

## Solution

You don’t need to restructure your application. Instead, simply add the following two lines to your entry file:

<pre class="language-tsx" data-title="src/main.tsx"><code class="lang-tsx">import React from "react";
import ReactDOM from "react-dom/client";
<strong>import { handleOidcCallback } from "oidc-spa/oidc/handleOidcCallback";</strong>
<strong>handleOidcCallback();</strong>

ReactDOM.createRoot(document.getElementById("root")!).render(
  //...
);
</code></pre>

This ensures that the authentication server's response is processed as early as possible, minimizing delays while keeping the impact on your bundle size minimal. :thumbsup:
