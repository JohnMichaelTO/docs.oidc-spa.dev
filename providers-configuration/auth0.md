---
icon: shield-quartered
---

# Auth0

Let's see how to configure Auth0 to get the required parameters to configure oidc-spa!

{% embed url="https://youtu.be/zPikliLzC84" %}

## Creating your application

* Navigate to [https://manage.auth0.com/dashboard](https://manage.auth0.com/dashboard)
* In the left panel navigate to Applications -> Applications
* Click "Create Application"
* Select Application Type: Single Page Application
* Navigate to the "Settings" tab, you'll see the domain and client ID

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```typescript
const { ... } = createOidc({
    // Referred to as "Domain" in Auth0 terminology:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
});
```

### Create an API

If you want Auth0 to issue a JWT access token that you can use to consume your api you have to: &#x20;

* Navigate to [https://manage.auth0.com/dashboard](https://manage.auth0.com/dashboard)
* In the left panel navigate to Applications -> APIs
* Click "Create API"
* Select Application Type: Single Page Application
  * Identifier: Ideally you would put the root url of your API like `https://myapp.my-company.com/api` but really, it's just an identifier, you can put anythin you like.
  * Click Save

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<pre class="language-tsx"><code class="lang-tsx">const { ... } = createOidc({
    // Referred to as "Domain" in Auth0 terminology:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
<strong>    extraQueryParams: {
</strong><strong>       // Custom API Identifier
</strong><strong>       audience: "https://app.my-company.com/api"
</strong><strong>    }
</strong>});
</code></pre>

## (Optional) Configuring a custom domain

It's higly advised that you [configure a custom domain in your Auth0 organization](#user-content-fn-1)[^1] to avoid Auth0 beeing seen as a third party relative to your app by the browsers.  \
See end of third party cookies for more details. &#x20;

Indeed, Auth0 is one of those providers that do not by defaut issue a refresh token, so when the acess\_token is about to expire, if you havent cofigured a custom domain, oidc-spa will have to force reload your app to refresh the token, it wont be able to do it silently in the background. &#x20;

By default, Auth0 issue access\_token with a 24h validity duraction period. If you leave this default setting you won't experience page refresh of course because oidc-spa will never need to refresh the tokens, that being said, depending of how sensible your app is you might want to change this default as discussed in the next section.

To configure a custom domain:

* Navigate to the Auth0 dashboard: [https://manage.auth0.com/dashboard](https://manage.auth0.com/dashboard)
* Click on Settings in the left bar
* Click on the Custom Domain tab
* Configure a custom domain, see [the end of third pary cookie page ](../resources/end-of-third-party-cookies.md)to know what domain will do usually you would pick something like auth.my-company.com

Once you've done that you can use your configured custom domain as issuerUri:

```diff
 const { ... } = createOidc({
-    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
+    issuerUri: "auth.my-company.com",
     clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
     extraQueryParams: {
         audience: "https://app.my-company.com/api"
     }
 });
```

## (Optional) Auto Logout configuration

This section is applicable if you want your user to be automatically disconnected after a set period of inactivity on your app.&#x20;

For security-critical apps, users should log in **each visit** and be **logged out** [**after inactivity**](#user-content-fn-2)[^2].

**Why?** Users accessing sensitive applications should not remain authenticated indefinitely, especially if they step away from their device. The session idle timeout ensures automatic logout after inactivity.

**Steps to enforce this policy:**

* Configuring the session expiration:
  * Go to  [https://manage.auth0.com/dashboard](https://manage.auth0.com/dashboard)
  * Click on Settings in the left bar
  * Click on the "Advanced" tab
  * In the "Session Expiration":
    * Idle Session Lifetime:  5 minutes (300 seconds): ensures users are logged out after 5 minutes of inactivity.
    * Maximum Session Lifetime: 20160 minutes (14 days): ensures users who actively use the app don’t get logged out unnecessarily
* Configuring the Lifespawn of the access token:
  * In the left pannel navigate to Applications -> APIs
  * Select "My App - API" (or whatever you put earlyer)
  * Click on the "Settings" tab
  * Unce "Access Token Settings":&#x20;
    * Maximum Access Token Lifetime: 240 seconds (4 minutes): Should be something shorter than the Idle Session Lifetime.&#x20;
    * Implicit / Hybrid Flow Access Token Lifetime: 240 seconds: You don't use it but it has to be set so that you can save.
  * At the bottom of the page click "Save"

One last important thing, since Auth0 does not issue Refresh Token, and even when it does, it's not a JWT,  you have to let oidc-spa know about how you have configured your server:

<pre class="language-typescript"><code class="lang-typescript"> const { ... } = createOidc({
    issuerUri: "auth.my-company.com",
    clientId: "DzXSmwQS7oSTQGLbafhrPXYLT0mOMyZD",
    extraQueryParams: {
       audience: "https://app.my-company.com/api"
    },
<strong>    idleSessionLifetimeInSeconds: 300
</strong>});
</code></pre>

Now, if you want, you can implement an auto logout countdown in your app that let your users know when they are about to be logged out:

{% content-ref url="../auto-logout.md" %}
[auto-logout.md](../auto-logout.md)
{% endcontent-ref %}

[^1]: You can do it even under the free plan, you just have to enter a credit card.

[^2]: The user is considered inactive by oidc-spa when it's not actively moving the mouse, touching the screen or typing on the keyboard in any tab of your app. (More precisely, on any tab of any app that use the same SSO session)
