---
icon: shield-quartered
---

# Auth0

{% code title="Example config matching the screenshots" %}
```typescript
const { ... } = createOidc({
    // Referred to as "Domain" in Auth0 terminology:
    issuerUri: "dev-r2h8076n6dns3d4y.us.auth0.com",
    clientId: "DkoDtyQkYj4UPk3gKk4MjtnD9nskUhpt",
    extraQueryParams: {
       // Custom API Identifier
       audience: "https://app.my-company.com/api"
    }
});
```
{% endcode %}

## Avoding page refresh

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

<pre class="language-typescript" data-title="Example config matching the screenshots"><code class="lang-typescript">const { ... } = createOidc({
<strong>    issuerUri: "auth.my-company.com",
</strong>    // ...
});
</code></pre>

## Auto Logout configuration

This is applicable if you want your user to be automatically disconnected after a set period of inactivity on your app.&#x20;

For security-critical apps, users should log in **each visit** and be **logged out** [**after inactivity**](#user-content-fn-2)[^2].

**Why?** Users accessing sensitive applications should not remain authenticated indefinitely, especially if they step away from their device. The session idle timeout ensures automatic logout after inactivity.

**Steps to enforce this policy:**

* Configuring the session expiration:
  * Go to  [https://manage.auth0.com/dashboard](https://manage.auth0.com/dashboard)
  * Click on Settings in the left bar
  * Click on the "Advanced" tab
  * In the "Session Expiration":
    * Idle Session Lifetime: 300 seconds (5 minutes): ensures users are logged out after 5 minutes of inactivity.
    * Maximum Session Lifetime: 20160 minutes (14 days): ensures users who actively use the app don’t get logged out unnecessarily
* Configuring the Lifespawn of the access token:
  * In the left pannel navigate to Applications -> APIs
  * Select your application
  * Click on the "Settings" tab
  * Unce "Access Token Settings":&#x20;
    * Maximum Access Token Lifetime: 240 seconds (4 minutes): Should be something shorter than the Idle Session Lifetime.&#x20;
    * Implicit / Hybrid Flow Access Token Lifetime: 240 seconds: You don't use it but it has to be set so that you can save.
  * At the bottom of the page click "Save"

One last important thing, since Auth0 does not issue Refresh Token, and even when it does, it's not a JWT,  you have to let oidc-spa know about how you have configured your server:

{% code title="Example config matching the screenshots" %}
```typescript
const { ... } = createOidc({
    // ...
    idleSessionLifetimeInSeconds: 300
});
```
{% endcode %}

Now, if you want, you can implement an auto logout countdown in your app that let your users know when they are about to be logged out:

{% content-ref url="../auto-logout.md" %}
[auto-logout.md](../auto-logout.md)
{% endcontent-ref %}

[^1]: You can do it even under the free plan, you just have to enter a credit card.

[^2]: The user is considered inactive by oidc-spa when it's not actively moving the mouse, touching the screen or typing on the keyboard in any tab of your app. (More precisely, on any tab of any app that use the same SSO session)
