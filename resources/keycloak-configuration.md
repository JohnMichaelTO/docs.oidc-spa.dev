---
icon: sliders
---

# Keycloak Configuration

oidc-spa need to two parameter to connect with your Keycloak instance, the issuerUri and the clientId.

### issuerUri

In Keycloak, the OIDC issuer uri is formatted as such:

**https://**<mark style="color:blue;">**\<DOMAIN>**</mark><mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>**/realms/**<mark style="color:green;">**\<REALM\_NAME>**</mark>

* <mark style="color:blue;">**\<DOMAIN>**</mark>: The domain of your Keycloak server, example: **auth.my-company.com**. _Note that in order to avoid issues related to the end of third party cookies it's important that your app and your keycloak server be hosted under the same root domain (my-company.com)._ [_Learn more_](end-of-third-party-cookies.md)_._
* <mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>: The sub path under your which your Keycloak is hosted. By default, in recent version of keycloak it's "" (empty string). On older Keycloak version it used to be "**/auth**" by default. Check how you Keycloak server is configured. This parrameter is usualy set by an environement variable. Example `-e KC_HTTP_RELATIVE_PATH=/auth`.
* <mark style="color:green;">**\<REALM\_NAME>**</mark>: The name of your realm. Example: **myrealm**. One important note is that you should always create create a realm for your organization and **never use the master realm**. To create a realm navigate to **https://**<mark style="color:blue;">**\<DOMAIN>**</mark><mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>**/admin/master/console** login as an administrator, click on [the select at the top left corner of the page](https://github.com/user-attachments/assets/3761894d-486d-4157-af32-d9c2a4c78260), click on "Create a new Realm", give it <mark style="color:green;">a name</mark>, save.&#x20;

### clientId







Connect to the admin panel of your Keycloak server (we assumes it's https://auth.my-domain.net/auth)

* Create a realm called "myrealm" (or something else), go to **Realm settings**
  1. On the tab General
     1. _User Profile Enabled_: **On**
  2. On the tab **login**
     1. _User registration_: **On**
     2. _Forgot password_: **On**
     3. _Remember me_: **On**, or **Off** if you want to implement [auto logout](../auto-logout.md).
  3. On the tab **email,** we give an example with [AWS SES](https://aws.amazon.com/ses/), if you don't have a SMTP server at hand you can skip this by going to **Authentication** (on the left panel) -> Tab **Required Actions** -> Uncheck "set as default action" **Verify Email**. Be aware that with email verification disable, anyone will be able to sign up to your service.
     1. _From_: **noreply@my-domain.net**
     2. _Host_: **email-smtp.us-east-2.amazonaws.com**
     3. _Port_: **465**
     4. _Authentication_: **enabled**
     5. _Username_: **\*\*\*\*\*\*\*\*\*\*\*\*\*\***
     6. _Password_: **\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\***
     7. When clicking "save" you'll be asked for a test email, you have to provide one that correspond **to a pre-existing user** or you will get a silent error and the credentials won't be saved.
  4. On the tab Themes. See [Keycloakify](https://www.keycloakify.dev/) for creating a Keycloak theme that match your webapp.
  5. On the tab **Localization**
     1. _Internationalization_: **Enabled**
     2. _Supported locales_: \<Select the languages you wish to support>
  6. On the tab **Sessions**
     1. SSO Session Idle: **14 days** - This is where you configure the [auto logout](../auto-logout.md) policy. This parameter defines the lifespawn of the refresh token. If you want your user to be automatically loged out after **30 minutes** of inactivity, Inacivity meaning they are not actively interacting with your app by scrolling, moving the mouse or typing, then you want to set this to **30 minutes**.
     2. SSO Session Max: **14 days** - Even if you implement [auto logout](../auto-logout.md) you want to leave this to at least one day. Indeed as long as your users are actively interacting with your app they should remain logged in\*\*.\*\*
     3. SSO Session Idle Remember Me: **365 days** - Same than SSO Session Idle but when the user have checked "Remember me" when login in. If you have enaled "remeber me" and you want this option to make sens you must set it to a value that is greater than SSO Session Idle. If you have set SSO Session Idle to something short because you want to implement an auto logout policy you probably want to go in Realm -> login and disable "Remember me"
     4. SSO Session Max Remember Me: **365 days** - Same note here.
* Create a new OpenID Connect client called "myclient" (or something else) by accessing Clients -> Create Client
  1. _Valid redirect URIs_: **https://onyxia.my-domain.net/oidc-callback.htm, http://localhost\* (for testing in local)**
* (OPTIONAL) On the left pannel you can go to identity provider to enable login via Google, GitHub, Instagram, ect...
* (OPTIONAL) Enable your user to delete their own account (see [user account managment](../user-account-management.md))
  1. In the left bar navigate to **Autentication** -> **Required Action** -> "**Delete Account**" Enabled: **On**
  2. In the left bar navigate to **Realm Setting** -> **User Registration** -> **Default Roles** -> **Assign Role** -> **Filter by client** -> select **Delete Account** and click on assign.

{% hint style="success" %}
Now the parameter that you will have to provide to oidc-spa are:

```
    issuerUri: "https://auth.your-domain.net/realms/myrealm",
    clientId: "myclient"
```

Replace `your-domain.net`, `myrealm` and `myclient` by what you actually used in the configuration process.\
(On older Keycloak the issuerUri will be "https://auth.your-domain.net/**auth**/realms/myrealm")
{% endhint %}
