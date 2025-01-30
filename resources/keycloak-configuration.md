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
7. Make sure **Client authentication** is _off_ and that **Standard Flow** is checked in, click **next**.
8. Set two Valid Redirect URIs: **https://**<mark style="color:orange;">**\<APP\_DOMAIN>**</mark><mark style="color:red;">**\<BASE\_URL>**</mark>**oidc-callback.htm** and **http://localhost:\<DEV\_PORT>/oidc-callback.htm**.
   1. <mark style="color:orange;">**\<APP\_DOMAIN>**</mark>: Examples: **https://my-company.com** or _https://**app.**&#x6D;y-company.com_. _Note that in order to avoid issues related to_ [_the end of third party cookies_](end-of-third-party-cookies.md) _it's important that_ <mark style="color:orange;">**\<APP\_DOMAIN>**</mark> _and_ <mark style="color:blue;">**\<KC\_DOMAIN>**</mark> _be hosted under the same root domain (**my-company.com**)._
   2. <mark style="color:red;">**\<BASE\_URL>**</mark>: Examples: "**/**" or "**/dashboard/**".
   3. **\<DEV\_PORT>**: Example: **5173** (Default port of the Vite dev server)
   4. If you are not using using the **oidc-callbak.htm** file, remove the **/oidc-callback.htm** portion of the urls, there should be no trailing slashes at the end.
9. Feel free to fill in the other field but they are not required. Click **Save**, you're done!

## Session lifespawn configuration

One important policy you want to define is how often you want your user to have to authenticate again when they visite your site. \
\
Note that the parameter that we will configure here do not affect the lifespawn of the access token that remains 5 minuts by default. What we are tweaking here is for how long Keycloak will keep the session active and that is reflected in the livespawn of the reflesh token,  this how oidc-spa is able to learn about it.&#x20;

Let's see the good defaults for the two more common scenario:

### Sensitive apps like Banking, Admin pannels ect...

For thoses kind of web applications, you want the user to have to login again each time they visit your app. You also want them to be automatically loged out after [a period of inactivity](#user-content-fn-1)[^1].

To enforce this policy you want to:

* Disable the  "Remeber Me" checkbox when logging in:
  * Select <mark style="color:green;">your realm</mark>
  * In the left menu navigate to realm settings
  * Go to the Login tab
  * Set "Remember Me" to Off
* Set the session Idle time:&#x20;
  * &#x20;In the Realm settings go to the Session Tab
  * Set Session idle: 5min&#x20;
  * Session idle MAX: 14 days, if your user is actively using your app for days, there's no reason to desconect them.&#x20;

You can display a coundown timer before auto logout with:&#x20;

{% content-ref url="../auto-logout.md" %}
[auto-logout.md](../auto-logout.md)
{% endcontent-ref %}

### Non sensitive app like ecomerce boutique or social media app

For thoses kind of app you don't want your user to have to authenticate every other day. Take YouTube for example, each time you reach the site your logged in already, we want this type of behaviour. &#x20;

To enable it&#x20;

* Enable the possibility for your user to check a "Remeber Me" checkbox when logging in:
  * Select <mark style="color:green;">your realm</mark>
  * In the left menu navigate to realm settings
  * Go to the Login tab
  * Set "Remember Me" to On
* Set the session idle: You want the users that haven't checked "Remember Me" to have to re authenticate once every 2 week.&#x20;
  * &#x20;In the Realm settings go to the Session Tab
  * Set Session idle and Session Idle Max to  14 days
* Set the session idle Remember Me: You want the users that have explicitely checked "Remeber Me" when logging in to only have to authenticate again once every year.
  * In the Session tab of the Realm settings, set Session idle Remeber Me and Session Idle Max Remember me to 356 days.

## Enabling user to delete their own account

By default on Keycloak, users are not allowed to delete their accout. &#x20;

If you try to implement [a delete account button](../user-account-management.md), when clicking on it, your users will be granted with an "action non permited" screen. &#x20;

To enable it:&#x20;

1. In the left bar navigate to **Autentication** -> **Required Action** -> "**Delete Account**" Enabled: **On**
2. In the left bar navigate to **Realm Setting** -> **User Registration** -> **Default Roles** -> **Assign Role** -> **Filter by client** -> select **Delete Account** and click on assign.

[^1]: A user is considered inactive by oidc-spa if the browser tab of your web app isn't focused or if the tab is focused but he not actively interacting with the app: moving the mouse, typing on keyboard or touching the screen.
