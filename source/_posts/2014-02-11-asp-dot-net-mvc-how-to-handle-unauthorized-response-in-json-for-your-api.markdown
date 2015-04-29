---
layout: post
title: "ASP.NET MVC how to handle unauthorized response in JSON for your API"
date: 2014-02-11 13:16:43 +1100
comments: false
categories: ["mobile", "authentication", "asp.net mvc", "dependency injection"]
description: ASP.NET MVC how to handle unauthorized response in JSON for your API
keywords: "mobile application, api, authentication, asp.net mvc, filterattribute"
---

Assuming you want to prepare some JSON API in your ASP.NET MVC with authorization. 

To share the same authorization logics for our Controller Action, what we should probably do is to implement our own FilterAttributes.

It can be as simple as:
{% raw %}
``` cs ApiAuthorizeAttribute.cs
    public class ApiAuthorizeAttribute : ActionFilterAttribute, IAuthorizationFilter 
    {
		//Property Inject here!
        public IAuthTokenService AuthTokenService { get; set; }

        public ApiAuthorizeAttribute()
        {     
        }

        #region IAuthorizationFilter member
        public void OnActionExecuting(ActionExecutingContext filterContext)
        {
            bool authTokenValid = IsRequestTokenValid(filterContext);

            if (!authTokenValid)
            {
                filterContext.Result = new JsonResult
                {
                    Data = new { Success = false, Data = "Unauthorized" },
                    ContentEncoding = System.Text.Encoding.UTF8,
                    ContentType = "application/json",
                    JsonRequestBehavior = JsonRequestBehavior.AllowGet
                };
            }
        }

    }
```
{% endraw %}
Note we are using property injection here for the `IAuthTokenService` here, check out <a href="https://code.google.com/p/autofac/wiki/MvcIntegration#Inject_Properties_Into_FilterAttributes" target="_blank" >Inject Properties Into FilterAttributes</a> for more information.

## HTTP status codes ##
In order to add our HTTP status codes, we can simple add the following line:
<!-- more -->
{% raw %}
``` cs ApiAuthorizeAttribute.cs start:1 mark:1
				filterContext.HttpContext.Response.StatusCode = 401;
                filterContext.Result = new JsonResult
                {
                    Data = new { Success = false, Data = "Unauthorized" },
                    ContentEncoding = System.Text.Encoding.UTF8,
                    ContentType = "application/json",
                    JsonRequestBehavior = JsonRequestBehavior.AllowGet
                };
```
{% endraw %}

## Problem: The default ASP.NET forms authentication redirect behaviour ##
The default ASP.NET forms authentication behaviour will convert HTTP 401 status codes to 302 in order to redirect to the login page.
This probably not we want here as we are expecting a JSON for our API result.

## Solution ##
If you are using .Net 4.5, you can apply the new <a href="http://msdn.microsoft.com/en-us/library/system.web.httpresponse.suppressformsauthenticationredirect(v=vs.110).aspx" target="_blank">HttpResponse.SuppressFormsAuthenticationRedirect</a> property.

If you are using .Net 4.0 or lower version, it seems it cannot be done at the moment, but we may try to use different HTTP status code like <a href="http://en.wikipedia.org/wiki/HTTP_403" target="_blank">403</a>



