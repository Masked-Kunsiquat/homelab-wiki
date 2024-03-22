---
title: Authentic (SSO)
description: 
published: 1
date: 2024-03-22T21:13:19.521Z
tags: 
editor: markdown
dateCreated: 2024-03-22T20:52:04.413Z
---

I've recently implemented SSO in my HomeLab environment. I switched over from [Authelia](https://www.authelia.com) to [Authentik](https://goauthentik.io).

As I set up different applications, I am going to write out the instructions for linking them to Authentik (for future reference).

# OAuth2/OIDC
## [Vikunja](https://docs.goauthentik.io/integrations/services/vikunja/)
> #### Preparation
> 
> The following placeholders will be used:
> 
> `vik.company` is the FQDN of Vikunja.
> `authentik.company` is the FQDN of authentik.
> `authentik Login` is the name shown on Vikunja set in config.yml, and used for the Redirect URI. If the name set in config.yml has capitalization or spaces like in this example, they will be set to lowercase and no spaces in the callback URL, like `authentiklogin`.
{.is-info}

#### Step 1
 
In authentik, under Providers, create an OAuth2/OpenID Provider with these settings:

> **NOTE**
> Only settings that have been modified from default have been listed.
{.is-warning}

**Protocol Settings**
`Name`: Vikunja
`Client ID`: Copy and Save this for Later
`Client Secret`: Copy and Save this for later
`Signing Key`: Select one of the available signing keys (Without this, Vikunja will not recognize Authentik's signing key method as a valid one and the login will not work)
`Redirect URIs/Origins`:
https://vik.company/auth/openid/authentiklogin



#### Step 2

Edit/Create your `config.yml` file for Vikunja. Local authentication can be safely disabled in the Local block if all users must login through authentik, in this example it is left enabled.

Incorporate the following example Auth block into your `config.yml`:

```
auth:
  # Local authentication will let users log in and register (if enabled) through the db.
  # This is the default auth mechanism and does not require any additional configuration.
  local:
    # Enable or disable local authentication
    enabled: true
  # OpenID configuration will allow users to authenticate through a third-party OpenID Connect compatible provider.<br/>
  # The provider needs to support the `openid`, `profile` and `email` scopes.<br/>
  # **Note:** Some openid providers (like gitlab) only make the email of the user available through openid claims if they have set it to be publicly visible.
  # If the email is not public in those cases, authenticating will fail.
  # **Note 2:** The frontend expects to be redirected after authentication by the third party
  # to <frontend-url>/auth/openid/<auth key>. Please make sure to configure the redirect url with your third party
  # auth service accordingly if you're using the default Vikunja frontend.
  # Take a look at the [default config file](https://github.com/go-vikunja/api/blob/main/config.yml.sample) for more information about how to configure openid authentication.
  openid:
    # Enable or disable OpenID Connect authentication
    enabled: true
    # A list of enabled providers
    providers:
      # The name of the provider as it will appear in the frontend.
      - name: "authentik Login"
        # The auth url to send users to if they want to authenticate using OpenID Connect.
        authurl: https://authentik.company/application/o/vikunja/
        # The client ID used to authenticate Vikunja at the OpenID Connect provider.
        clientid: THIS IS THE CLIENT ID YOU COPIED FROM STEP 1 in authentik
        # The client secret used to authenticate Vikunja at the OpenID Connect provider.
        clientsecret: THIS IS THE CLIENT SECRET YOU COPIED FROM STEP 1 in authentik
```


> **NOTE**
> You need to restart the Vikunja API after applying the OpenID configuration to Vikunja.
{.is-warning}

> **NOTE**
> Vikunja Configuration Reference: https://vikunja.io/docs/config-options/#auth
{.is-warning}

#### Step 3

In authentik, create an application which uses this provider. Optionally apply access restrictions to the application using policy bindings.

`Name`: Vikunja
`Slug`: vikunja
`Provider`: vikunja
`Launch URL`: https://vik.company
  


## [Wiki.js](https://docs.goauthentik.io/integrations/services/wiki-js/)
> #### Preparation
> 
> The following placeholders will be used:
> 
> `wiki.company` is the FQDN of Wiki.js.
> `authentik.company` is the FQDN of authentik.
{.is-info}


#### Step 1
In Wiki.js, navigate to the *Authentication* section in the *Administration* interface.

Add a *Generic OpenID Connect / OAuth2* strategy and note the *Callback URL / Redirect URI* in the *Configuration Reference* section at the bottom.

#### Step 2

In authentik, under *Providers*, create an *OAuth2/OpenID Provider* with these settings:

`Redirect URI`: The *Callback URL / Redirect URI* you noted from the previous step.
`Signing Key`: Select any available key
Note the *client ID* and *client secret*, then save the provider. If you need to retrieve these values, you can do so by editing the provider.


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
> If you're using self-signed certificates for authentik, you need to set the root certificate of your CA as trusted in WikiJS by setting the `NODE_EXTRA_CA_CERTS` variable as explained here: https://github.com/Requarks/wiki/discussions/3387.
{.is-warning}

#### Step 4

In authentik, create an application which uses this provider. Optionally apply access restrictions to the application using policy bindings.

Set the Launch URL to the *Callback URL / Redirect URI* without the `/callback` at the end, as shown below. This will skip Wiki.js' login prompt and log you in directly.


