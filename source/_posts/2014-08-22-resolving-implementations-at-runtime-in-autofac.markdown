---
layout: post
title: "Resolving implementations at runtime using Autofac"
date: 2014-08-13 20:02:01 +1000
comments: false
categories: ["autofac", "dependency injection"]
description:
keywords: "resolve runtime implementations, dependency injection, factory, func<>, Dynamic instantiation"
---

There are lots of ways on resolving your services and implementations at runtime in Autofac. Following is a simple example to demonstrate how we can do it.

Assumng we have a simple interface `IDataService` that will provide us some data.


{% raw %}
``` cs IDataService.cs
    public interface IDataService
    {
        string GetData();
    }
```
{% endraw %}


We are consuming this service and will return our users the data based on their `Role`, there are two roles in this example.
And we are providing different implementation for the `IDataService` for each role.

{% raw %}
``` cs
    public enum RoleName
    {
        Premium,
        Normal
    }

    public class PremiumDataService : IDataService
    {
        ....
    }

    public class NormalDataService : IDataService
    {
        ....
    }
```
{% endraw %}


## Simple Solution using Func<> as our factory in Autofac ##


We will use a simple function `Func<RoleName, IDataService>` as our dependency in our `Controller`. It will act as our factory so that we can consume this factory to get our expected data.

{% raw %}
``` cs
        private readonly Func<RoleName, IDataService> _dataServiceFactory;
        public HomeController(Func<RoleName, IDataService> dataServiceFactory)
        {
            this._dataServiceFactory = dataServiceFactory;
        }

        public ActionResult GetData()
        {
            ....

            var dataService = this._dataServiceFactory(roleType);
            var data = dataService.GetData();
            ....
        }
```
{% endraw %}

<!-- more -->

## Autofac registration for Func<> (First version) ##

To config this `Func<RoleName, IDataService>` in Autofac, we can simple do this:

{% raw %}
``` cs
            builder.Register<Func<RoleName, IDataService>>(c =>
            {
                return (roleName) =>
                {
                    switch (roleName)
                    {
                        case RoleName.Premium:
                            return new PremiumDataService();
                        case RoleName.Normal:
                            return new NormalDataService();
                        default:
                            throw new ArgumentException();
                    }
                };
            });
```
{% endraw %}

The above implementation is viable, however, notice that:

1. We are using `new` in creating our services, this is not a best practice in Dependency Injection. Especially when our services may have it's own dependency.

2. We can use a more elegant way in Autfac to identify our services using `Keyed Registration`


## using Keyed Registration for our factory (Second version) ##

Below is our second version for the registration of our services using `Keyed Registration` in Autofac.
Note that in this version, we are also registering our services into Autofac.
Therefore if our services have other dependencies, Autofac will also help us to resolve them automatically.

{% raw %}
``` cs

            builder.RegisterType<PremiumDataService>()
                .As<IDataService>().Keyed<IDataService>(RoleName.Premium);

            builder.RegisterType<NormalDataService>()
                .As<IDataService>().Keyed<IDataService>(RoleName.Normal);

            builder.Register<Func<RoleName, IDataService>>(c =>
            {
                var componentContext = c.Resolve<IComponentContext>();
                return (roleName) =>
                {
                    var dataService = componentContext.ResolveKeyed<IDataService>(roleName);
                    return dataService;
                };
            });
```
{% endraw %}
