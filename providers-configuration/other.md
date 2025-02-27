---
icon: sliders
---

# Other OIDC Provider

If you are using an OIDC Provider other than the ones for wich we have [a specific guide for](broken-reference), here are the general instruction on how to configure your OIDC Provider:

* Create a OpenID Connect client with **Standard Flow** also refered to as **Authorization code flow**. It's usualy the default setting.
* Disable client authentication (public client). We don't want to rely on client secret, it would be instantaneously leaked by your SPA.  This means enforcing PKCE. &#x20;
* Valid Redirect URIs: **https://my-app.com/** and **http://localhost:5173/**
  * The slash at the end is important
  * If your app is hosted on a sub path like /dashboard you would set **https://my-app.com/dashboard/** and **http://localhost:5173/dashboard/**
  * 5173 is the default port used by Vite dev server, adapt according to your setup.
* Valid post logout redirect: Same as the Valid Redirec URIs
* Web Origins: **https://my-app.com**, **http://localhost:5173**
