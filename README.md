---
icon: sign-posts-wrench
---

# Installation

{% hint style="danger" %}
I just released oidc-spa v6. If you are new, wait a few hours so that I can complete the new documentation :)
{% endhint %}

{% hint style="info" %}
Before starting be aware that oidc-spa is not suited for Next.js or any other framwork that involves server side rendering.

If you are using Next the closer alternative is to use [NextAuth.js](https://next-auth.js.org/) (with [the Keycloak adapter](https://next-auth.js.org/providers/keycloak) if you are using Keycloak). See [this guide](https://phasetwo.io/docs/securing-applications/next/).
{% endhint %}



If you're having issues don't hesitate to [reach out on Discord](https://discord.gg/mJdYJSdcm4)!

Let's install [oidc-spa](https://github.com/keycloakify/oidc-spa) in your project:

{% tabs %}
{% tab title="npm" %}
```bash
npm install oidc-spa
```
{% endtab %}

{% tab title="yarn" %}
```bash
yarn add oidc-spa
```
{% endtab %}

{% tab title="pnpm" %}
```bash
pnpm add oidc-spa
```
{% endtab %}

{% tab title="bun" %}
```bash
bun add oidc-spa
```
{% endtab %}
{% endtabs %}

Create the **oidc-callback.**[**htm**](#user-content-fn-1)[^1] file in your **public/** directory:

{% code title="public/oidc-callback.htm" %}
```html
<!doctype html>
<html>
<body>
    <!-- oidc-spa file. Do not remove, do not edit -->
    <script>
        if (localStorage.getItem("oidc-spa.callback-file-version") !== "2") { alert("Your oidc-callback.htm file is outdated. Please update it. https://docs.oidc-spa.dev/v/v6"); } const authResponse = {}; for (const [key, value] of new URL(location.href).searchParams) { authResponse[key] = value; } const reloadOnRestore = ()=> { const listener = ()=> { if (document.visibilityState === "visible") { document.removeEventListener("visibilitychange", listener); location.reload(); } }; document.addEventListener("visibilitychange", listener); }; const stateJson = localStorage.getItem(`oidc.${authResponse.state}`); if (!stateJson || stateJson === "null") { reloadOnRestore(); const KEY = "oidc-spa.has-navigated-back"; if (sessionStorage.getItem(KEY) === "true") { sessionStorage.removeItem(KEY); history.forward(); } else { sessionStorage.setItem(KEY, "true"); history.back(); } } const { data } = JSON.parse(stateJson); if (data.isSilentSso) { parent.postMessage(authResponse, location.origin); } else { reloadOnRestore(); const redirectUrl = new URL(data.redirectUrl); for (const [key, value] of Object.entries(authResponse)) { redirectUrl.searchParams.set(`oidc-spa.${key}`, value); } location.href = redirectUrl.href; }
    </script>
</body>
</html>
```
{% endcode %}

On your OIDC Server, in your client configuration, define Valid Redirect URIs: **https://**[**my-app.com/**](#user-content-fn-2)[^2]**oidc-callback.htm**, **http://localhost:**[**5173**](#user-content-fn-3)[^3]**/oidc-callback.htm**. [More infos](resources/oidc-server-configuration.md).

<details>

<summary>Doing without the oidc-callback.htm file</summary>

If for some reasons it's not fesable or practical for you to rely on the `oidc-callback.htm` it's fine, this file is only here for optimisation purposes but the lib works without it.

To let oidc-spa know that you won't be using the oidc-callback.htm file provide those parameter when initializing the adapter:

```diff
 export const { ... } = createOidc({
     // ...
-    BASE_URL: import.meta.env.BASE_URL,
+    BASE_URL: undefined
     // Provide the path of your homepage.
     // It's usually "/" but it can be for example "/dashboard"
     // If your app is accessible at https://your-domain.com/dashboard
+    homeUrl: "/"
 });
```

As Valid Redirec URIs use the home of your app without trailing shlash at the end.\
Example: **https://my-app.com** or https//my-app.com/dashboard and **http://localhost:5173** or http://localhost:5173/dashboard

</details>

[^1]: Yes **.htm** and not **.html**.\
    Some web server such as [serve](https://github.com/vercel/serve#readme) will rewrite `/silent-sso.html` to `/silent-sso` and serve the `index.html` of your distribution.\
    Using the `.htm` extention prevend any potential issue.

[^2]: Here we assume that your app is hosted at the root of the my-app.com domain.\
    If your app is hosted under a subpath like my-app.com/dashboard you would use https://my-app.com/dashboard/oidc-spa.htm

[^3]: This is the default port used by the Vite dev server, adapt this to your setup.
