# Modern Auth FAQ

These are some of most frequent questions about OpenID Connect and OAuth.

## How do we control and segregate access in our backend API?

There is more than one way to do this. These are some examples:   
   1. [Access Control List](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#access-control-lists)

      Basically, this method specifies that you should keep a list of App IDs and their level of access in your backend. As an example, when your backend receives an `access token` it can decode it and get the `app id` out of it, then it can compare this app id to its ACL and let it access an endpoint or not. 

   2. [Application Permissions](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#application-permissions)
   
      At a very high level this is an App Role. When using this approach, the `access token` will contain the `Roles` claim. And based on this claim you can program your backend to authorize or not access to certain endpoints. For example, when your backend receives a request it can check if the bearer token contains the role "admin", if it does it can let the request pass and access the endpoint to create a new account, for instance.

      Another interesting fact to note about this method is that it allows you to configure your backend app in the Identify Provider to "require user/app assignment". This means that when that option is `on` a client app will not even be able to request an `access token` from the Identity Provider for this backend app unless it has been explicitly added to an app role defined by the backend.

## We have some applications that need to interact with each other without a user directly involved. How can an app get authorized to access another one?

This can be achieved by using the OAuth `Client Credentials` grant type. This flow was created to authenticate a client application in the Authorization Server using its own app ID and Secret (which can be a x509 certificate). 

## For SPAs, should we use the Implicit or the Authorization Code with PKCE grant type?

You should absolutely use the `Authorization Code with PKCE` grant type.

**However**, some Authorization Servers do not support PKCE for SPAs. Particularly, Azure AD does not yet support this extension for SPAs because it does not allow CORS requests to the /token endpoint. Microsoft's documentation about OIDC/OAuth for SPAs recommend the use of the Implicit flow. 

This is specifically stated in this [comment](https://github.com/MicrosoftDocs/azure-docs/issues/39201#issuecomment-541899381). More information can be found in the links below:

- [SPA usage does not work, due to CORS](https://github.com/MicrosoftDocs/azure-docs/issues/39201)
- [Error when trying to use PKCE with Angular SPA and AAD](https://github.com/IdentityModel/oidc-client-js/issues/1024)
- [SPA sign in using the OAuth 2.0 implicit flow in AAD B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-spa)

## We have an API and we need to implement AuthN and AuthZ to it. Can we use SAML for that?

 

4. Why aren't NTML and Kerberos suitable for the cloud?
