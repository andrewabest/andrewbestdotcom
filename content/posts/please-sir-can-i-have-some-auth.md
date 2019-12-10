---
title: "Azure REST API: Please Sir, Can I Have Some Auth?"
date: 2019-12-10T15:00:00+09:30
draft: false
---

I've been building a _Thing_. This thing works with the Microsoft Azure platform, and allows users to provision and modify certain Azure resources outside of the Azure Portal itself.

The _Thing_ is entirely an Angular SPA. To work with Microsoft Azure resources, it communicates via the [Azure REST API](https://docs.microsoft.com/en-us/rest/api/azure/). It doesn't use any special frameworks or SDKs to do so, just plain old HTTP calls to resource endpoints, and some TypeScript type goodness to provide some type safety around inputs and outputs to and from the API.

I'm working with the Azure Management API published under https://management.azure.com. I'm using [MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics), Microsoft's authentication library for JS, and successor of [ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/ADAL-basics), to facilitate authentication to the Azure Management API.

I came across a number of challenges when setting up the Authentication and Authorization for this solution that I thought I'd share.

Azure AD Setup
===

Before attempting to query the API I knew I was going to need to create an Azure Active Directory App Registration for my app to authenticate against - this is analogous to a [Client](https://tools.ietf.org/html/rfc6749#section-1.1) Registration in OAuth parlance.

(This is a trend that permeates Microsoft and Azure - calling well-known things by different names)

In the App Registration I configured the appropriate `Authentication` properties to allow my Angular app to authenticate against it - redirect URLs and the like, I won't go into detail as these are straight-forward, and were not the source of my headaches.

I then went into `API Permissions` for the App Registration to set the permissions that my application would be requesting so that it could work with the Management API.

With Azure AD, `API Permissions` serve two purposes:

1. They enforce the consent process - this has two layers in Azure Active Directory. A Directory Admin must consent to the App Registration having a given permission on behalf of the organization, and individual users must consent to the permission being allowed during their authentication process

2. They provide permission to access resources - once the organization and the user have consented to a given permission, it can be requested by an application to provide access to resources. Each resources will define its own permission model

![The API Permissions Page](/have-some-auth/api-permissions.png)

Notice that the permission is called *user_impersonation*? I didn't. 

Red Herrings
===

Now that I had set up the Azure AD App Registration to facilitate authentication for my app, I wanted to test that I could access the REST API before I went and wrote code that does so. My favourite tool to do this sort of API spelunking is Postman.

I crafted a basic request to a resource I knew existed, and was promptly met with a `401` - which is to be expected, as I had not provided any authentication along with the request. Onto the next step: figuring out how to retrieve a token that when supplied would change that `401` into a `200`.

After reading through the [Getting Started with REST](https://docs.microsoft.com/en-us/rest/api/azure/) page I was left having to connect a number of dots. [It does mention](https://docs.microsoft.com/en-us/rest/api/azure/#acquire-an-access-token) that the article only deals with pure OAuth endpoints and doesn't talk to any frameworks, and it does not cover the authorization model for resources, so generally leaves you with more questions than answers. 

Let's be clear: you will almost never write your own code to interact with OAuth endpoints exposed by a given identity provider. In fact you _shouldn't_, unless you really know what you are doing and know [the](https://tools.ietf.org/html/rfc6749#section-1.1) [specs](https://openid.net/specs/openid-connect-core-1_0.html) back-to-front. Providing an example that details doing this in a _Getting Started_ document for your API is _not nice_. 

Next I went down the rabbit hole of Azure documentation. [The MSAL Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/api-scopes#request-specific-scopes-for-a-web-api) provided more information about `Scopes` (which were not mentioned at all in the _Getting Started_ article) which would provide me with the authorization I needed, but all of the examples I could find still related to _Graph API_, which seemed to have a different resource security model than the _Management API_.

I began seeing a few references to a `https://management.azure.com/user_impersonation` scope when googling - primarily in StackOverflow. Look familiar? It matches the API permission name I defined above for my App Registration. Unfortunately I did these two things far enough apart that that detail eluded me for quite some time. 

`user_impersonation` is the scope that you need to request in your authentication flow to work with the Azure Management API. Just this one scope, no others are required. It doesn't have granular scopes like Graph with its `User.Read` etc, just a simple delegated access that will allow you to authorize to the Management API. The API will then use your own user account permissions that exist within the Azure AD to govern your access to any underlying resources within that API.

As far as I can see - **this is not documented anywhere officially and explicitly**. If you can actually point me to where this is documented, I will buy you a beer / coffee.

To compound the confusion, Microsoft also refer to it as a `Permission` within Azure AD, but it is a `Scope` within the authentication flow context:

```TS
    import {Logger, LogLevel, UserAgentApplication, AuthResponse} from 'msal';

    ...

    this.clientApp = new UserAgentApplication({
      auth: {
        clientId: environment.aad.appId,
        authority: `https://login.microsoftonline.com/${environment.aad.tenantId}`,
        postLogoutRedirectUri: window.location.origin + '/logout',
        redirectUri: window.location.origin + '/authorize',
      },
      system: {
        logger,
      },
      cache: {
        cacheLocation: 'sessionStorage'
      }
    });

    ...

    this.clientApp.loginRedirect({ scopes: ['https://management.azure.com/user_impersonation'] });
```

Remember when we talked about referring to well-known things by different names? Yup, that strikes again here.

Microsoft have not created a pit of success with their API security model, and the primary culprit is the lack of cohesiveness and discoverability with their documentation. You need to put in a fair bit of work to connect disparate chunks of information available, generally discovered via Google and not via any of the provided navigation or search mechanisms, to get a complete view of what you need to do. 

In an ideal world they would have a directory of known Microsoft-published resources you could request access to, and scopes for those resources, in a single, easily discoverable place. You know, perhaps beside the [Getting Started with REST](https://docs.microsoft.com/en-us/rest/api/azure/) page.

But for now: hopefully this helps anyone else attempting to work with the Azure Management REST API.