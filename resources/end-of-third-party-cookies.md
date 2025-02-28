---
icon: cookie
---

# End of third-party cookies

Google is phasing out third-party cookies for all **Chrome** users in 2024. These cookies are already blocked by default in **Safari** and **Firefox**.

### How does this affect you?

**In most sirconsentences, it doesn't.** `oidc-spa` works seamlessly even in environments where third-party cookies are blocked.

However, if your app is **allowed** to set third-party cookies on your authentication server, you'll experience a **slight improvement** in the initial load time of your app (because we'll be able to implement silent sign-in in an iframe instead of redirecting which causes a page reload).

The only situation where the the user experience will be significantly degraded if third pary cookies are blocked is if your OIDC provider does not issue refresh tokens **and** the lifespawn of the access token is short. If this situation, if the access token lifespawn is for example 20 seconds, your app will auto reload every 18 seconds which is not great.  &#x20;

So it's best, if possible to make sure cookies are not blocked. &#x20;

### When are cookies blocked

> Third party cookies restriction applies when your OIDC provider and your application do not share a parent domain.

#### Examples:

✅ No issue related to thid pary cookies **(Same Parent Domain)**

* App hosted at `www.my-company.com`, `dashboard.my-company.com`, or `my-company.com/dashboard`
* `issuerUri`: `https://auth.my-company.com/realms/myrealm`
* **Parent domain:** `my-company.com`

❌ Cookies blocked **(Different Parent Domains)**

* App hosted at `my-company.com`
* `issuerUri`:
  * `https://accounts.google.com`
  * `https://xxxx.us.auth0.com`
  * `https://login.microsoftonline.com/xxx/v2.0`
  * `https://hydra.project-name.ory.cloud/`
* **No common parent domain → Third-party cookies will be blocked.**

***

## Google reCAPTCHA

While reCAPTCHA is not directly related to `oidc-spa`, its cookies are set on the **login page**, outside of your app. Since this is a related concern, you can find more details here:

{% embed url="https://docs.keycloakify.dev/faq/google-recaptcha-and-end-of-third-party-cookies" %}
