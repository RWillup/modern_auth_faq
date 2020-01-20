# Modern Auth FAQ

These are some of the most frequently asked questions about OpenID Connect and OAuth.

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

In summary, Authorization Code with PKCE is always preferred. However, if the Authorization Server does not support POST requests to the token endpoint then `implicit flow` can be used if the SPA is served by a static server. But, if the SPA is served by a backend such as ASP.NET or Spring Boot, then it's possible to use Authorization Code with PKCE in this backend.

## Why aren't NTML and Kerberos suitable for the cloud?

Protocols like Kerberos and NTLM don't work well in the cloud because they need to use many different ports and most firewalls typically only allow port 80 and 443. Kerberos' Ticket Granting system that issues tickets that only allow access to machines that are within the same Kerberos realm.

**Integrated Windows Authentication (IWA)**
IWA used to be a common choice for ASP.NET applications running on Microsoft IIS. IWA has been popular because it allows a seamless single sign-on experience for users who have logged in to their Windows workstation using domain credentials. 

IWA, however, relies on NTLM or Kerberos and so it requires the user's workstation and the server the application is running on to be joined to the same Active Directory domain or trusting domains. So one of the consequences of relying on IWA is that you cannot have other IdPs, which means you cannot authenticate users or applications from different domains.

## What is an Identity Provider (IdP)?

An *Identity Provider* is a system that provides authentication services for your workloads and ultimately decides whether to authenticate a user.

In addition to providing authentication services, IdPs often manage the identities themselves.

Common identity providers include:
* Active Directory
* Azure Active Directory
* IDaaS pricers such as Okta or Ping Identity
* Other LDAP directories

## What do other Cloud Providers recommend?

The following test was extracted from Google Cloud documentation:

*For applications that you plan to develop, OAuth 2.0 or OIDC should be your preferred choice. These protocols are widely adopted, and you can take advantage of many well-tested libraries and tools.*

## Sources

[Authenticating Corporate Users in a Hybrid Environment](https://cloud.google.com/solutions/authenticating-corporate-users-in-a-hybrid-environment#identity_providers)

