---
icon: sign-posts-wrench
---

# Installation

{% hint style="danger" %}
Sorry, oidc-spa is in transitory state. V6 ain't fully ready yet and it would be a shame starting to use v5 now.  \
Come back in a few hours/days
{% endhint %}

{% hint style="info" %}
Before starting be aware that oidc-spa is not suited for Next.js or any other framwork that involves server side rendering.

If you are using Next the closer alternative is to use [NextAuth.js](https://next-auth.js.org/) (with [the Keycloak adapter](https://next-auth.js.org/providers/keycloak) if you are using Keycloak). See [this guide](https://phasetwo.io/docs/securing-applications/next/).
{% endhint %}

If you're having issues don't hesitate to [reach out on Discord](https://discord.gg/mJdYJSdcm4)!

{% tabs %}
{% tab title="npm" %}
```bash
npm install oidc-spa
```
{% endtab %}

{% tab title="yarn" %}
```bash
yarn add oidc-spa
```
{% endtab %}

{% tab title="pnpm" %}
```bash
pnpm add oidc-spa
```
{% endtab %}

{% tab title="bun" %}
```bash
bun add oidc-spa
```
{% endtab %}
{% endtabs %}

In your OIDC server configuration, the Valid Redirect URI is the home of your web application.

