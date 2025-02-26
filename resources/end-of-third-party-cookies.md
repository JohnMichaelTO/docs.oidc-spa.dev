---
icon: cookie
---

# End of Third-Party Cookies

Google is phasing out third-party cookies for all **Chrome** users in 2024. These cookies are already blocked by default in **Safari** and **Firefox**.

### How does this affect you?

**It doesn’t.** `oidc-spa` works seamlessly even in environments where third-party cookies are blocked. 

However, if your app is **allowed** to set third-party cookies on your authentication server, you may experience a **slight improvement** in the initial load time of your app 
(because we'll be able to implement silent sign-in in an iframe).  

### Optimizing for Performance

If possible, it’s beneficial to configure your setup in a way that prevents third-party cookies from being blocked. The rule is simple: 

> To avoid third-party cookie restrictions, **your authentication server and your application should share the same parent domain**.

#### Examples:

✅ **Allowed (Same Parent Domain)**  
- App hosted at `www.my-company.com`, `dashboard.my-company.com`, or `my-company.com/dashboard`  
- `issuerUri`: `https://auth.my-company.com/realms/myrealm`  
- **Parent domain:** `my-company.com`

❌ **Blocked (Different Parent Domains)**  
- App hosted at `my-company.com`  
- `issuerUri`:  
  - `https://accounts.google.com`  
  - `https://login.microsoftonline.com/xxx/v2.0`  
  - `https://hydra.project-name.ory.cloud/`  
- **No common parent domain → Third-party cookies will be blocked.**

---

## Google reCAPTCHA

While reCAPTCHA is not directly related to `oidc-spa`, its cookies are set on the **login page**, outside of your app. Since this is a related concern, you can find more details here:

{% embed url="https://docs.keycloakify.dev/faq-and-help/google-recaptcha-and-end-of-third-party-cookies" %}