---
url: /apis
toc: true
title: APIs Overview
description: Learn the basics of APIs, their role in OAuth and how to configure an API in Auth0 Dashboard.
crews: crew-2
topics:
  - api-authentication
  - oidc
  - apis
contentType: concept
useCase:
  - secure-api
  - call-api
---
# APIs

An API is an entity that represents an external resource, capable of accepting and responding to protected resource requests made by applications. At the [OAuth2 spec](https://tools.ietf.org/html/rfc6749) an API maps to the **Resource Server**.

When an application wants to access an API's protected resources it must provide an <dfn data-key="access-token">Access Token</dfn>. The same Access Token can be used to access the API's resources without having to authenticate again, until it expires.

Each API has a set of defined permissions. Applications can request a subset of those defined permissions when they execute the authorization flow, and include them in the Access Token as part of the <dfn data-key="scope">**scope**</dfn> request parameter.

For example, an API that holds a user's appointments, may accept two different levels of authorization: read only (scope `read:appointments`) or write (scope `write:appointments`). When an application asks the API to list a user's appointments, then the Access Token should contain the `read:appointments` scope. In order to edit an existing appointment or create a new one, the Access Token should contain the `write:appointments` scope. See [Tokens](/tokens) for more information. 

## How to configure an API in Auth0

Click on the [APIs menu option](${manage_url}/#/apis) on the left.

::: note
The API tab will already have one API created automatically, the **Auth0 Management API**. For more details on the features of the Management API and its available endpoints, refer to: [Management API](/api/management/v2).
:::

Click the **+ Create API** button.

![Create a new API](/media/articles/api/overview/create-api.png)

You need to provide the following information for your API:

- **Name**: a friendly name for the API. Does not affect any functionality.

- **Identifier**: a unique identifier for the API. Auth0 recommends using a URL. Auth0 does differentiate between URLs that include the last forward slash. For example, https://example.com and https://example.com/ are two different identifiers. The URL does not have to be a publicly available URL. Auth0 will not call your API. This value **cannot** be modified afterwards. 

- **Signing Algorithm**: the algorithm to sign the tokens with. The available values are `HS256` and `RS256`. When selecting `RS256` the token will be signed with the tenant's private key. To learn more about signing algorithms, see [Signing Algorithms paragraph](/tokens/concepts/signing-algorithms).

Fill in the required information and click the **Create** button.

Once you do so you will be navigated to the *Quick Start* of your API. Here you can find details on the implementation changes you have to do to your API, which basically consists of choosing a JWT library from a predefined list and configuring this library to validate the Access Tokens in your API.

![API Quick Starts](/media/articles/api/overview/quickstarts-view.png)


The other available views for your API are:

- **Settings**: lists the settings for your API. Some are editable. Here you can change the token expiration time and enable offline access (this way Auth0 will allow your applications to ask for <dfn data-key="refresh-token">Refresh Tokens</dfn> for this API). For details refer to the [API Settings paragraph](#api-settings).

- **Scopes**: here you can define the scopes for this API, by setting a name and a description.

- **Machine to Machine Applications**: lists all applications for which the **Client Credentials** grant is **enabled**. By default, this grant is **enabled** for [Regular Web Applications and Machine to Machine Applications](/applications). You can authorize any of these applications to request Access Tokens for your API. Optionally, you can select a subset of the defined scopes to limit your authorized application's access. 

- **Test**: from this view, you can execute a sample Client Credentials flow with any of your authorized applications to check that everything is working as expected.

### API Settings

Click on the *Settings* tab of your [API](${manage_url}/#/apis) to review the available settings:

- **Id**: A unique alphanumeric string generated by Auth0. The information is read only and you will only need it if you will be working directly with [Auth0's Management API Resource Servers endpoints](/api/management/v2#!/Resource_Servers/get_resource_servers_by_id).

- **Name**: A friendly name for the API. Does not affect any functionality. The following characters are not allowed: `< >`.

- **Identifier**: A unique identifier for your API. This value is set upon API creation and cannot be modified afterwards. We recommend using a URL but note that this doesn't have to be a publicly available URL, Auth0 will not call your API at all.

- **Token Expiration (Seconds)**: The amount of time (in seconds) before the Auth0 Access Token expires. The default value is 86400 seconds (24 hours). The maximum value you can set is 2592000 seconds (30 days).

- **Allow Skipping User Consent**: When a first party application requests authorized access against an API with the *Allow Skipping User Consent* flag set, the User Consent dialog will not be shown to the final user. Note that if the hostname of your application's <dfn data-key="callback">**callback URL**</dfn> is `localhost` or `127.0.0.1` the consent dialog will always be displayed.

- **Allow Offline Access**: If this setting is enabled, Auth0 will allow applications to ask for Refresh Tokens for this API.

- **Signing Algorithm**: The algorithm to sign the tokens with. The available values are `HS256` and `RS256`. When selecting `RS256` (recommended) the token will be signed with the tenant's private key. This value is set upon API creation and cannot be modified afterwards. To learn more about signing algorithms, see [Signing Algorithms](/tokens/concepts/signing-algorithms).

## Keep reading

- [API Authorization landing page](/api-auth)
- [Identify the proper OAuth 2.0 flow for your use case](/api-auth/which-oauth-flow-to-use)
- [Tokens](/tokens)