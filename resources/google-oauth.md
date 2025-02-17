---
icon: google
description: Implement "Login with Google"
---

# Google OAuth

With oidc-spa you would usualy use an OIDC Provider like Keycloak to centralize your authentication flow and have Google configured as an identity provider in your Keycloak server so that user can select "Google" as authentication option.  \
That being said, if you do not have a Keycloak instance it's perfectly possible to configure oidc-spa directly against google as shown in the following video: &#x20;

{% embed url="https://youtu.be/d0RgnM4vXbc" %}

Example configuration on the Google Cloud Console:&#x20;

* Navigate to google cloud platform console
* Api and services
* Credentials
* Create Credentials: OAuth Client ID
* Application type: Web Application&#x20;
* Authorized Redirect URIs: **https://my-app.com/** and **http://localhost:5173/**
  * The slash at the end is important
  * If your app is hosted on a sub path like /dashboard you would set **https://my-app.com/dashboard/** and **http://localhost:5173/dashboard/**
  * 5173 is the default port used by Vite dev server, adapt according to your setup.
* Authorized JavaScript Origins: The origins of your redirec uris.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Google did a big faux pas in the design of their OAuth service by making any standard auth flow + PKCE fail if no client secret is provided!  \
Public Clients can't store secrets, the mechanism that prevent from code interception by a third party is PKCE which is supported by Google and implemented by oidc-spa.  \
So having PKCE + client secret makes no sense and is thourouly missleading.\
\
That being said rest assure that providing the client secret to oidc-spa in your frontend code, in this context, has no security repercution whatshoever it's just bad API design by the Google. &#x20;
{% endhint %}



We should have the following code: &#x20;

{% tabs %}
{% tab title="Vanilla" %}
```typescript
import { createOidc } from "oidc-spa";

export const prOidc = createOidc({
    issuerUri: "https://accounts.google.com",
    clientId: "928024164279-ifjvmsffi64slkk81h3gmoh7p03ev68k.apps.googleusercontent.com",
    homeUrl: import.meta.env.BASE_URL,
    // In this context you're not in trouble, it's not your fault Google
    // forces you to provide a secret to a public client, it's on them. 
    __clientSecret_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: "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"
});

```


{% endtab %}

{% tab title="React" %}
<pre class="language-typescript" data-title="src/oidc.ts"><code class="lang-typescript">import { createReactOidc } from "oidc-spa/react";

export const { OidcProvider, useOidc, getOidc } = createReactOidc({
    issuerUri: "https://accounts.google.com",
    clientId: "928024164279-ifjvmsffi64slkk81h3gmoh7p03ev68k.apps.googleusercontent.com",
    homeUrl: import.meta.env.BASE_URL,
    // In this context you're not in trouble, it's not your fault Google
    // forces you to provide a secret to a public client, it's on them. 
<strong>    __clientSecret_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: "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"
</strong>});
</code></pre>
{% endtab %}
{% endtabs %}



