---
icon: sliders
---

# Keycloak Configuration

## Getting the `issuerUri` and `clientId`

Oidc-spa need to two parameter to connect with your Keycloak instance, the issuerUri and the clientId.

<pre class="language-typescript"><code class="lang-typescript">const { ... } = createOidc({
<strong>    issuerUri: "...",
</strong><strong>    clientId: "...",
</strong>    // ...
});
</code></pre>

### `issuerUri`

In Keycloak, the OIDC issuer uri is formatted as such:

**https://**<mark style="color:blue;">**\<KC\_DOMAIN>**</mark><mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>**/realms/**<mark style="color:green;">**\<REALM\_NAME>**</mark>

* <mark style="color:blue;">**\<KC\_DOMAIN>**</mark>: The domain of your Keycloak server, example: **auth.my-company.com**.
* <mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>: The sub path under your which your Keycloak is hosted. By default, in recent version of keycloak it's "" (empty string). On older Keycloak version it used to be "**/auth**" by default. Check how you Keycloak server is configured. This parrameter is usualy set by an environement variable. Example `-e KC_HTTP_RELATIVE_PATH=/auth`.
* <mark style="color:green;">**\<REALM\_NAME>**</mark>: The name of your realm. Example: **myrealm**. One important note is that you should always create create a realm for your organization and **never use the master realm**. To create a realm navigate to **https://**<mark style="color:blue;">**\<DOMAIN>**</mark><mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>**/admin/master/console** login as an administrator, click on [the select at the top left corner of the page](https://github.com/user-attachments/assets/3761894d-486d-4157-af32-d9c2a4c78260), click on "Create a new Realm", give it <mark style="color:green;">a name</mark>, save.&#x20;

### `clientId`

The  client it is usualy something like '<mark style="color:yellow;">myapp</mark>'. Let's see how to create a client suitable for your SPA.

1. Connect to your Keycloak Admin Console: **https://**<mark style="color:blue;">**\<KC\_DOMAIN>**</mark><mark style="color:purple;">**\<KC\_RELATIVE\_PATH>**</mark>**/admin/master/console**
2. Login as an admin
3. Using [the select input in the top left corner](https://github-production-user-asset-6210df.s3.amazonaws.com/6702424/407624868-3761894d-486d-4157-af32-d9c2a4c78260.png?X-Amz-Algorithm=AWS4-HMAC-SHA256\&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20250129%2Fus-east-1%2Fs3%2Faws4_request\&X-Amz-Date=20250129T075418Z\&X-Amz-Expires=300\&X-Amz-Signature=c39fdbbb65f30b9be70a9810c6a783e21a3bc7a6b1e6478b7c9f76430f098bfb\&X-Amz-SignedHeaders=host), navigate to <mark style="color:green;">your realm</mark>.
4. In the left pannel click on **Clients**.
5. Click on **Create Client**.
6. Fill in the <mark style="color:yellow;">**Client ID**</mark>, for example <mark style="color:yellow;">myapp</mark>, click on **next**.
7. Make sure **Client authentication** is off and that **Standard Flow** is checked in, click **next**.
8. Set two Valid Redirect URIs: **https://**<mark style="color:orange;">**\<APP\_DOMAIN>**</mark><mark style="color:red;">**\<BASE\_URL>**</mark>**oidc-callback.htm** and **http://localhost:\<DEV\_PORT>/oidc-callback.htm**.
   1. <mark style="color:orange;">**\<APP\_DOMAIN>**</mark>: Examples: **https://my-company.com** or _https://**app.**&#x6D;y-company.com_. _Note that in order to avoid issues related to_ [_the end of third party cookies_](end-of-third-party-cookies.md) _it's important that_ <mark style="color:orange;">**\<APP\_DOMAIN>**</mark> _and_ <mark style="color:blue;">**\<KC\_DOMAIN>**</mark> _be hosted under the same root domain (**my-company.com**)._
   2. <mark style="color:red;">**\<BASE\_URL>**</mark>: Examples: "**/**" or "**/dashboard/**".
   3. **\<DEV\_PORT>**: Example: **5173** (Default port of the Vite dev server)
   4. If you are not using using the **oidc-callbak.htm** file, remove the **/oidc-callback.htm** portion of the urls, there should be no trailing slashes at the end.
9. Feel free to fill in the other field but they are not required. Click **Save**, you're done!

## Session lifespawn configuration

One important policy you want to define is how often you want your user to have to authenticate again when they visite your site.&#x20;

Let's see the good defaults for the two more common scenario:

### Sensitive apps like Banking, Admin pannels ect...











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
