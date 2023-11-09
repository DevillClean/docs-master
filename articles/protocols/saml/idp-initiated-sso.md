---
title: IdP-Initiated Single Sign-On
description: How to setup SAML Identity Provider-initiated Single Sign-on (SSO).
topics:
  - saml
  - sso
contentType:
  - how-to
  - concept
useCase:
  - add-idp
---

# IdP-Initiated Single Sign-On

Many instructions for setting up a <dfn data-key="security-assertion-markup-language">SAML</dfn> federation begin with <dfn data-key="single-sign-on">Single Sign-on (SSO)</dfn> initiated by the service provider. The service provider redirects the user to the identity provider for the purposes of authentication. This process is commonly used for consumer-facing scenarios.

However, in enterprise scenarios, it is sometimes common to begin with the identity provider initiating SSO, not the service provider. For example, an enterprise company might set up a portal to ensure that users navigate to the correct application after they sign on to the portal.

## Risks of using an IdP-Initiated SSO flow

::: warning
IdP-Initiated flows carry a security risk and are therefore <strong>NOT</strong> recommended. Make sure you understand the risks before enabling IdP-Initiated SSO. 
:::

In an IdP-initiated flow neither Auth0 (which receives the unsolicited response from the Identity Provider) nor the application (that receives the unsolicited response generated by Auth0) can verify that the user actually started the flow. Because of this, enabling this flow opens the possibility of an [Login CSRF attack](https://support.detectify.com/support/solutions/articles/48001048951-login-csrf), where an attacker can trick a legitimate user into unknowingly logging into the application with the identity of the attacker.  

The recommendation is to use SP-Initiated flows whenever possible.

### On IdP-Initiated flows and OpenID Connect

<dfn data-key="openid">OpenID Connect (OIDC)</dfn> does not support the concept of an IdP-Initiated flow. So while Auth0 offers the possibility of translating a SAML IdP-Initiated flow (from a SAML connection) into an OIDC response for an application, any application that properly implements the OIDC/OAuth2 protocol will reject an unrequested response. 

When using OIDC applications, the best option is to have the users start the login flow **at the application** or configure the portals to send the user to the application's login initiation endpoint (without an IdP-Initiated SAML response) so that, again, the application starts the authentication flow.

## How to set up IDP-initiated SSO

<iframe width="560" height="315" src="https://www.youtube.com/embed/hZGYWeBvZQ8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

To setup IdP-Initiated SSO, go to the [Enterprise Connections](${manage_url}/#/connections/enterprise) section of the dashboard and choose **SAMLP Identity Provider**. Under the **Settings** section you can see the configuration for IdP-Initiated SSO.

![Configure SAML Idp-Initiated SSO Settings](/media/articles/dashboard/connections/enterprise/conn-enterprise-saml-idp-initiated-sso.png)

**Default Application:** When the IdP initiated login succeeds this is the application where users are routed. This setting shows available applications enabled for this connection. Select the application from the dropdown that you want the users to login with IdP initiated. Only one application can be selected for an IdP-initiated login per SAML connection.

**Response Protocol:** This is the protocol used to connect your selected **Default Application**. Most commonly applications are configured with the OpenID Connect protocol (read [here](#on-idp-initiated-flows-and-OIDC) for possible pitfalls). However if you have configured a SAML2 Web App addon for your application and want to route the SAML assertion you will need to select SAML.

**Query String:** Query string options help to customise the behavior when the OpenID Connect protocol is used. You can set multiple options similar to setting parameters with a [query string](https://en.wikipedia.org/wiki/Query_string). You can set:

* `redirect_uri`: When the IdP-initiated login has completed the request is then redirected to the first URL listed in the **Allowed <dfn data-key="callback">Callback URLs</dfn>** for the application. However if you set a `redirect_uri` the IdP will redirect to this URL. This brings flexibility for cases like when you have set subdomain scheme with a wildcard and you only want to redirect to one specific subdomain.
* `scope`: You could define <dfn data-key="scope">scopes</dfn> for the ID Token sent. Note that it is possible to set multiple scopes.
* `response_type`: This field is used to set the token for Implicit Grant Flow for SPA applications. You could set code for Authorization Code Grant Flow for regular web applications.

Example Query String:

`redirect_uri=https://jwt.io&scope=openid email&response_type=token`

## Post-back URL

When using **IdP-Initiated SSO**, please make sure to include the `connection` parameter in the post-back URL: `https://${account.namespace}/login/callback?connection=YOUR_CONNECTION_NAME`

## Lock/Auth0.js

If your application is a Single-Page Application that uses <dfn data-key="lock">Lock</dfn> or Auth0.js to process the authentication results, you will have to explicitly indicate that you want to allow IdP-Initiated flows (and thus [open the application to possible Login CSRF attacks](#risks-of-using-an-idp-Initiated-SSO-flow)).

If you are using [Auth0.js](/libraries/auth0js), you have to update the **webAuth.parseHash** of the [library](/libraries/auth0js/v9#extract-the-authresult-and-get-user-info) and set the flag **__enableIdPInitiatedLogin** to `true`.

```javascript
var data = webAuth.parseHash(
  {
    ...
    __enableIdPInitiatedLogin: true
    ...
  }
```

If you're using [Lock](/lock), you can include the flag using the options parameter sent to the constructor.

```javascript
const lock = new Auth0Lock(clientID, domain, options)
```

Here's the flag itself:

```javascript
var options = {
    _enableIdPInitiatedLogin: true
};
```

Note that the **enableIdPInitiatedLogin** flag is preceded by **one** underscore when used with Lock and **two** underscores when used with the auth0.js library.