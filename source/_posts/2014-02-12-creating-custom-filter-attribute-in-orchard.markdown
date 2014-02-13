---
layout: post
title: "Creating custom Filter Attribute in Orchard"
date: 2014-02-12 17:14:23 +1100
comments: true
categories: ["asp.net mvc", "orchard cms", "dependency injection"]
description: Creating custom Filter Attribute in Orchard
keywords: "asp.net mvc, filterattribute, orchard cms, dependency injection"
---

## Filter in Orchard ##
When working in Orchard, it's always a good idea to look at the current codebase as an example.

The one I chose as a reference in creating my custom filter attribute is the `Theme` filter.

It is located in `$\Orchard\Themes\ThemeFilter.cs` inside the `Orchard.Framework` project.

You will notice that `ThemeFilter` is actually inherited from a class called `FilterProvider`.
{% raw %}
``` cs ThemeFilter.cs start:1 mark:2
namespace Orchard.Themes {
    public class ThemeFilter : FilterProvider, IActionFilter, IResultFilter {
        public void OnActionExecuting(ActionExecutingContext filterContext) {
```
{% endraw %}

Now lets have a look inside `FilterProvider`.
{% raw %}
``` cs ThemeFilter.cs start:8 mark:14-21
    public abstract class FilterProvider : IFilterProvider {
        void IFilterProvider.AddFilters(FilterInfo filterInfo) {
            AddFilters(filterInfo);
        }

        protected virtual void AddFilters(FilterInfo filterInfo) {
            if (this is IAuthorizationFilter)
                filterInfo.AuthorizationFilters.Add(this as IAuthorizationFilter);
            if (this is IActionFilter)
                filterInfo.ActionFilters.Add(this as IActionFilter);
            if (this is IResultFilter)
                filterInfo.ResultFilters.Add(this as IResultFilter);
            if (this is IExceptionFilter)
                filterInfo.ExceptionFilters.Add(this as IExceptionFilter);
        }

    }
```
{% endraw %}


Bingo, it turns out that if you create a class to inherit `FilterProvider` and some `IActionFilter`, they will be automatically applied to your actions as `ActionFilter` already.

Note that Orchard is clever enough to help you to inject your dependencies using Autofac by constructor injection too.

## Attribute in Orchard ##

<!-- more -->

As you can see, when you created your custom filter, it actually automatically applied to **all** your actions.

What happanes if you only want to apply your filter in certain actions using attribute?

Its easy, just check the `ThemeFilter` and `ThemeAttribute` as an example:

1. create your own attribute
2. check whether the action contains this attribute inside your filter. 
3. apply what you want if the attribute exists or do nothing if it's not.



