---
layout: post
title: "Adding Email Templates service in your ASP.NET MVC projects using IoC"
date: 2014-02-24 17:18:57 +1100
comments: true
categories: ["asp.net mvc", "dependency injection", "unit test"]
description: Adding Email Templates service in your ASP.NET MVC projects using IoC
keywords: "asp.net mvc, unit test, postal, email, templates, razor, dependency injection, ioc"
---

It looks obvious to give your web applications to send an email with some templates engine.
After some googling, I decided to use <a href="http://aboutcode.net/postal/" target="_blank">Postal</a> as the preferred service because:
1. It uses Razor engine for the template, which everyone is familar with
2. It is based on the .NET framework's built in SmptClient ( so you can just config it using web.config )
3. It is designed to be used in a dependency injection pattern (provide unit testing for your controllers or classes)

## Using the IEmailService ##
With a decoupled design, it is possible you will seperate your logics include sending an email in your logic classes but not in your asp.net controller.
Therefore, an IEmailService that supports dependency injection is a better way to use than having an Email service just be used in a controller.
To apply Postal in your class, we can simply inject it's IEmailService to our business logic class.

{% raw %}
``` cs BusinessService.cs
    public class BusinessService : IBusinessService
    {
        private readonly IEmailService _emailService;
        public BusinessService(IEmailService emailService)
        {
            this._emailService = emailService;
        }
	}
```
{% endraw %}

### And your controller ###
{% raw %}
``` cs SomeController.cs
    public class SomeController : Controller
    {
        private readonly IBusinessService _businessService;
		
        public SomeController(IBusinessService businessService)
        {
            this._businessService = businessService;
        }
```
{% endraw %}

### Register the service using Autoface as our IoC ###
{% raw %}
``` cs
            builder.RegisterType<EmailService>().As<IEmailService>();
```
{% endraw %}

Now you can create your email templates in your `Views\Emails` folder and send it inside your business logics class
