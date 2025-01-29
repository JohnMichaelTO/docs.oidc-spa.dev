---
icon: sliders
---

# OIDC Server Configuration

## Specific guides

We have specific configuration instructions for the following Auth Software:

{% content-ref url="usage-with-keycloak.md" %}
[usage-with-keycloak.md](usage-with-keycloak.md)
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

* Create a OpenID Connect client with Standard Flow AKA Authorization code flow. It's the default setting.
* In the configuration of the client, you only need a single valid redirect URIs. Let's assume that the home of your page is https://my-app.com you would set https://my-app.com/oidc-callback.htm, if your app is hosted under a sub path like https://my-app.com/dashboard, use https://my-app.com/dashboard/oidc-callback.htm.  \
  You may also want to add **http://localhost:**[**5173**](#user-content-fn-1)[^1]**/oidc-callback.htm** for local developement.
* Valid post logout redirect: Same as the Valid Redirec URIs
* Web Ogigins: http://my-app.com, http://localhost:5173



[^1]: This is the default port used by vite developement server, of course adapt to your setup.
