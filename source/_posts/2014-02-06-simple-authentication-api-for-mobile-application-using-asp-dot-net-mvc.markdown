---
layout: post
title: "Simple Authentication API for mobile application using ASP.NET MVC"
date: 2014-02-06 17:22:34 +1100
comments: true
categories: ["mobile", "authentication", "asp.net mvc", "dependency injection", "mocking", "unit test"]
description: Simple Authentication API for mobile application in ASP.NET MVC
keywords: "mobile application, api, authentication, asp.net mvc, unit test"
---

This is actually part of server-side implementaion for the previous post: [How to authenticate from a mobile application to an existing web pplication](/blog/2014/02/05/how-to-authenticate-from-a-mobile-application-to-an-existing-web-application/)
We are going to create an ASP.NET MVC action as a JSON API to authenticate a mobile client.

## The Token Interface ##
First, let's create a token service interface for our API Controller. We will be using dependency injection pattern.
{% raw %}
``` cs IAuthTokenService.cs
    public interface IAuthTokenService
    {
        /// <summary>
        /// Issue a token for a user
        /// </summary>
        /// <param name="username"></param>
        /// <returns>tokenId</returns>
        string IssuseToken(string username);

        /// <summary>
        /// Check if a token is valid
        /// </summary>
        /// <param name="username"></param>
        /// <param name="tokenId"></param>
        /// <returns></returns>
        bool IsTokenValid(string username, string tokenId);

        /// <summary>
        /// Expire users' tokens (designed to be called when user changed their password)
        /// </summary>
        /// <param name="username"></param>
        /// <param name="tokensCreatedBefore">expire all tokens created before this time</param>
        void ExpireUserTokens(string username, DateTime? tokensCreatedBefore);
    }
```
{% endraw %}

<!-- more -->

## The API Controller ##
Our API controller can now use the token authentication service as simple as
{% raw %}
``` cs SimpleApiController.cs

    public class SimpleApiController : Controller
    {
        private readonly IMembershipService _membershipService;
        private readonly IAuthTokenService _tokenService;

        public SimpleApiController(IMembershipService membershipService, IAuthTokenService tokenService)
        {
            this._membershipService = membershipService;
            this._tokenService = tokenService;
        }
	
        [HttpPost]
        public JsonResult MobileAuthenticate(string email, string password)
        {
            bool valid = this._membershipService.ValidateUser(email, password);

            var result = new AuthTokenResult() {Success = false};

            if (valid)
            {
                var token = this._tokenService.IssuseToken(email);
                result.Success = true;
                result.TokenId = token;
            }

            return Json(result);
        }
	}
```
{% endraw %}

## Unit Test ##
Now that we have our interface, and we have our controller. 
Let's create an unit test first, yes, we don't actually need to implement our interface when we write our test.
We can actually assign someone to help us to implement the interface later.

Alright, below is one of our test. We will use <a target="_blank" href="https://github.com/Moq/moq4">Moq</a> to help us mock our interface.
{% raw %}
``` cs SimpleApiControllerTest.cs

    [TestClass()]
    public class SimpleApiControllerTest
    {
        private readonly IMembershipService _membershipService;
        private readonly IAuthTokenService _tokenService;

        [TestMethod()]
        public void MobileAuthenticateSuccess()
        {
            var tokenId = "dummyToken";
            var email = "some@email.com";
            var password = "abcdef";

            var memberMock = new Mock<IMembershipService>();
            memberMock.Setup(m => m.ValidateUser(It.IsAny<string>(), It.IsAny<string>())).Returns(true);

            var tokenMock = new Mock<IAuthTokenService>();
            tokenMock.Setup(t => t.IssuseToken(It.IsAny<string>())).Returns(tokenId);

            var controller = new SimpleApiController(memberMock.Object, tokenMock.Object);
            var result = controller.MobileAuthenticate(email, password);

            var authResult = ((JsonResult)result).Data as AuthTokenResult;

            Assert.AreEqual(true, authResult.Success);
            Assert.AreEqual(tokenId, authResult.TokenId);
        }
    }
```
{% endraw %}

