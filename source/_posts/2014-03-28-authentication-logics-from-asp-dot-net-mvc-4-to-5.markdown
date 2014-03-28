---
layout: post
title: "Authentication logics from ASP.NET MVC 4 to 5"
date: 2014-03-28 16:14:24 +1100
comments: true
categories: ["asp.net mvc", "owin", "authentication", ""]
description: Creating custom Filter Attribute in Orchard
keywords: "asp.net mvc, filterattribute, orchard cms, dependency injection"
---


## OWIN ##
To allow security sharing by other components that can be hosted on <a href="http://owin.org/" target="_blank">OWIN</a>. 
__ASP.NET MVC 5__  has applied a new security feature based on the __OWIN__ authentication middleware.

For more information, please click <a href="http://blogs.msdn.com/b/webdev/archive/2013/07/03/understanding-owin-forms-authentication-in-mvc-5.aspx" target="_blank">here</a>.

To start, let's create a new project based on Visual Studio 2013's  template: __Individual User Accounts__.


## MVC5 Template: Individual User Accounts##

Have a look in this newly created project's `web.config`. You will find that `FormsAuthenticationModule` is removed.

{% raw %}
``` cs web.config 
<system.webServer>
	<modules>
		<remove name="FormsAuthenticationModule" />
	</modules>
</system.webServer>
```
{% endraw %}

Now also have a look in what's the new logics is added for supporting __OWIN__.
{% raw %}
``` cs \app_start\startup.auth.cs start:10 mark:14-18,20
    public void ConfigureAuth(IAppBuilder app)
    {
        // Enable the application to use a cookie to store information for the signed in user
        app.UseCookieAuthentication(new CookieAuthenticationOptions
        {
            AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
            LoginPath = new PathString("/Account/Login")
        });
        // Use a cookie to temporarily store information about a user logging in with a third party login provider
        app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
```
{% endraw %}

It looks like the template offers a `Cookie` authentication as default, does it work if we applies our **ASP.NET MVC 4**'s `FormsAuthenticationTicket` to it?

## FormsAuthenticationTicket in MVC4##
We normally authenticate users in the following way in MVC4
{% raw %}
``` cs
            var ticket = new FormsAuthenticationTicket(..);
            var encryptedTicket = FormsAuthentication.Encrypt(ticket);
            var cookie = new HttpCookie(FormsAuthentication.FormsCookieName, encryptedTicket)
            {
                HttpOnly = true, Path = FormsAuthentication.FormsCookiePath
            };
            Response.Cookies.Add(cookie);
```
{% endraw %}

Now, if we apply this logic in our **MVC5** application, we will find that we do have added our authentication cookie, however, as **MVC5** follows the OWIN middleware, it has no way to understand our user is authenticated or not in `Request.IsAuthenticated`.

## AuthenticationManager in MVC5 ##
Now, lets have a look how MVC5 SignIn a user
{% raw %}
``` cs AccountController.cs start:1 mark:5
        private async Task SignInAsync(ApplicationUser user, bool isPersistent)
        {
            AuthenticationManager.SignOut(DefaultAuthenticationTypes.ExternalCookie);
            var identity = await UserManager.CreateIdentityAsync(user, DefaultAuthenticationTypes.ApplicationCookie);
            AuthenticationManager.SignIn(new AuthenticationProperties() { IsPersistent = isPersistent }, identity);
        }
```
{% endraw %}
A user is authenticated by calling `AuthenticationManager.SignIn`, to understand more details in it, we can have a look in the <a target="_blank" href="http://katanaproject.codeplex.com/SourceControl/latest#src/Microsoft.Owin/Security/AuthenticationManager.cs">Katana Project</a>.


## Solution ##
So, in order to **SignIn** a user, we just have to call the method `AuthenticationManager.SignIn`, which, request us to have a Claims base Identity implementation. By having a look in the <a target="_blank" href="http://katanaproject.codeplex.com">Katana Project</a>, we can easily create one for ourselves, below is an example of the rewrite of the `SignInAsync`:
{% raw %}
``` cs AccountController.cs start:1 mark:3-5
        private async Task SignInAsync(ApplicationUser user, bool isPersistent)
        {
            //AuthenticationManager.SignOut(DefaultAuthenticationTypes.ExternalCookie);
            var claimsIdentity = new ClaimsIdentity(
                new[] { new Claim(ClaimsIdentity.DefaultNameClaimType, user.UserName) },
                DefaultAuthenticationTypes.ApplicationCookie);
            AuthenticationManager.SignIn(new AuthenticationProperties() { IsPersistent = isPersistent } , claimsIdentity);
        }
```
{% endraw %}

Now, let's run our application, and we will see can SignIn a user by our own `Claims`.

## ClaimsIdentity was not present ##
When we run the above example, we will find an exception will be raised complaining




<span id="exception-error" style="display:none">A claim of type 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier' or 'http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider' was not present on the provided ClaimsIdentity. To enable anti-forgery token support with claims-based authentication, please verify that the configured claims provider is providing both of these claims on the ClaimsIdentity instances it generates. If the configured claims provider instead uses a different claim type as a unique identifier, it can be configured by setting the static property AntiForgeryConfig.UniqueClaimTypeIdentifier.</span>