---
icon: turtle
---

# Performance issues?

If your are experiencing performance issue, meaning by that, the createOidc() function takes too long to resolve or you're getting stuck on the fallback of the \<OidcProvider /> (<>Checking authentication ⌛️\</>) for sevral hundreds of milliseconds, it might be to the fact that your application is achitectured in a way that involves some async operations before oidc-spa beeing imported, which delay the silent signin process that is performed in an iframe.  \
\
To remedy that, you do not need to re-achitect your web application, you can simply add this two lines to your index: &#x20;

<pre class="language-tsx" data-title="src/main.tsx"><code class="lang-tsx">import React from "react";
import ReactDOM from "react-dom/client";
<strong>import { handleOidcCallback } from "oidc-spa/oidc/handleOidcCallback";
</strong><strong>handleOidcCallback();
</strong>
ReactDOM.createRoot(document.getElementById("root")!).render(
  //...
);
</code></pre>

This will ensure the auth server response is handled as soon as possible with minimal impact on your bundle size :thumbsup:.
