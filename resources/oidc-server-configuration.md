---
icon: sliders
---

# OIDC Server Configuration

## Specific guides

We have specific configuration instructions for the following Auth Software:

{% content-ref url="keycloak-configuration.md" %}
[keycloak-configuration.md](keycloak-configuration.md)
{% endcontent-ref %}

{% content-ref url="ory-hydra-configuration.md" %}
[ory-hydra-configuration.md](ory-hydra-configuration.md)
{% endcontent-ref %}

{% content-ref url="dex-configuration.md" %}
[dex-configuration.md](dex-configuration.md)
{% endcontent-ref %}

If you are using another OIDC Provider, no problem keep reading.

## Generic Guide

Here are the key takeways for configuring.

* Create a OpenID Connect client with **Standard Flow** also refered to as **Authorization code flow**. It's usualy the default setting.
* Disable client authentication (public client). We don't want to rely on client secret, it would be instantaneously leaked by your SPA.  &#x20;
* Valid Redirect URIs: **https://my-app.com/** and **http://localhost:5173/**
  * The slash at the end is important
  * If your app is hosted on a sub path like /dashboard you would set **https://my-app.com/dashboard/** and **http://localhost:5173/dashboard/**
  * 5173 is the default port used by Vite dev server, adapt according to your setup.
* Valid post logout redirect: Same as the Valid Redirec URIs
* Web Origins: **https://my-app.com**, **http://localhost:5173**
