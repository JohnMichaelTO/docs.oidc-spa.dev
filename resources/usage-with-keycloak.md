---
icon: sliders
---

# Keycloak Configuration



### Configuring your Keycloak server

Let's configure your Keycloak server with good default for an SPA.

Connect to the admin panel of your Keycloak server (we assumes it's https://auth.my-domain.net/auth)

* Create a realm called "myrealm" (or something else), go to **Realm settings**
  1. On the tab General
     1. _User Profile Enabled_: **On**
  2. On the tab **login**
     1. _User registration_: **On**
     2. _Forgot password_: **On**
     3. _Remember me_: **On**, or **Off** if you want to implement [auto logout](../documentation/auto-logout.md).
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
     1. SSO Session Idle: **14 days** - This is where you configure the [auto logout](../documentation/auto-logout.md) policy. This parameter defines the lifespawn of the refresh token. If you want your user to be automatically loged out after **30 minutes** of inactivity, Inacivity meaning they are not actively interacting with your app by scrolling, moving the mouse or typing, then you want to set this to **30 minutes**.
     2. SSO Session Max: **14 days** - Even if you implement [auto logout](../documentation/auto-logout.md) you want to leave this to at least one day. Indeed as long as your users are actively interacting with your app they should remain logged in\*\*.\*\*
     3. SSO Session Idle Remember Me: **365 days** - Same than SSO Session Idle but when the user have checked "Remember me" when login in. If you have enaled "remeber me" and you want this option to make sens you must set it to a value that is greater than SSO Session Idle. If you have set SSO Session Idle to something short because you want to implement an auto logout policy you probably want to go in Realm -> login and disable "Remember me"
     4. SSO Session Max Remember Me: **365 days** - Same note here.
* Create a new OpenID Connect client called "myclient" (or something else) by accessing Clients -> Create Client
  1. _Valid redirect URIs_: **https://onyxia.my-domain.net/oidc-callback.htm, http://localhost\* (for testing in local)**
* (OPTIONAL) On the left pannel you can go to identity provider to enable login via Google, GitHub, Instagram, ect...
* (OPTIONAL) Enable your user to delete their own account (see [user account managment](../documentation/user-account-management.md))
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
