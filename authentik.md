---
title: Authentic (SSO)
description: 
published: 1
date: 2024-03-22T21:00:48.780Z
tags: 
editor: markdown
dateCreated: 2024-03-22T20:52:04.413Z
---

I've recently implemented SSO in my HomeLab environment. I switched over from [Authelia](https://www.authelia.com) to [Authentik](https://goauthentik.io).

As I set up different applications, I am going to write out the instructions for linking them to Authentik (for future reference).

# OAuth2/OIDC

## Wiki.js
#### Step 1
In Wiki.js, navigate to the Authentication section in the Administration interface.

Add a Generic OpenID Connect / OAuth2 strategy and note the Callback URL / Redirect URI in the Configuration Reference section at the bottom.

#### Step 2

In authentik, under Providers, create an OAuth2/OpenID Provider with these settings:

Redirect URI: The Callback URL / Redirect URI you noted from the previous step.
Signing Key: Select any available key
Note the client ID and client secret, then save the provider. If you need to retrieve these values, you can do so by editing the provider.


#### Step 3

In Wiki.js, configure the authentication strategy with these settings:

`Client ID`: Client ID from the authentik provider.
`Client Secret`: Client Secret from the authentik provider.
`Authorization Endpoint URL`: https://authentik.company/application/o/authorize/
`Token Endpoint URL`: https://authentik.company/application/o/token/
`User Info Endpoint URL`: https://authentik.company/application/o/userinfo/
`Issuer`: https://authentik.company/application/o/wikijs/
`Logout URL`: https://authentik.company/application/o/wikijs/end-session/
`Allow self-registration`: Enabled
`Assign to group`: The group to which new users logging in from authentik should be assigned.


> **NOTE**
> You do not have to enable "Allow self-registration" and select a group to which new users should be assigned, but if you don't you will have to manually provision users in Wiki.js and ensure that their emails match the email they have in authentik.
{.is-warning}

> NOTE
> If you're using self-signed certificates for authentik, you need to set the root certificate of your CA as trusted in WikiJS by setting the NODE_EXTRA_CA_CERTS variable as explained here: https://github.com/Requarks/wiki/discussions/3387.
{.is-warning}

#### Step 4

In authentik, create an application which uses this provider. Optionally apply access restrictions to the application using policy bindings.

Set the Launch URL to the Callback URL / Redirect URI without the /callback at the end, as shown below. This will skip Wiki.js' login prompt and log you in directly.

