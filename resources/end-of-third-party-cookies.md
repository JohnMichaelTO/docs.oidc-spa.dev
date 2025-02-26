---
icon: cookie
---

# End of third-party cookies

Google is ending third-party cookies for all **Chrome** users in 2024 and are already disabled by default in **Safari** and **Firefox**. &#x20;

How does it affec you? **It doesn't** really. oidc-spa works perfectly fine even in situation where third party cookies are blocked.  \
\
When your app is allowed to set third party cookies on your auth server however, you'll get a slight performance improvement of initial load time of your app.  \


So, if it's possible, it's a nice to set up thing in a way that ensure they are not blocked.  \
\
The rule is quite easy if you don't want cookies to be blocked you should make sure that your auth server and your application share the same parent domain. &#x20;

Examples:

* Your app is hosted at www.my-company.com or dashboard.my-company.com or my-company.com/dashboard and your issuerUri is auth.my-company.com/realms/myrealm: :white\_check\_mark: Your auth server and your app have the same parent domain: my-company.com
* You app is hosted at my-company.com and your issuerUri is https://accounts.google.com or https://login.microsoftonline.com/xxx/v2.0 or https://hydra.project-name.ory.cloud/: :x: Third party cookies will be blocked because your app and the auth server do not have a parent domain in common. &#x20;

## Google reCaptcha

reCaptcha is not directly related to oidc-spa since the cookie it sets is on the thegister page (so outside of your app). Anyway, since it's a connex concern: &#x20;

{% embed url="https://docs.keycloakify.dev/faq-and-help/google-recaptcha-and-end-of-third-party-cookies" %}
