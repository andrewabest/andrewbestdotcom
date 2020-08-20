---
title: "Authorization with Custom Authentication in ASP.NET Core"
date: 2020-08-20T20:00:00+10:00
draft: false
---

There is a lot of good documentation for how to configure [authentication](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-3.1) and [authorization](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/introduction?view=aspnetcore-3.1) in an ASP.NET Core app. If you want to set up a secure application using the out-of-the-box components, Microsoft have you covered.

However, if you are faced with a not-so-standard scenario, it can get a bit hairier.

I was working on some software recently that is migrating to ASP.NET Core. It uses the new hosting infrastructure ASP.NET Core provides, and middleware, and at the end of the request pipeline either routes to new ASP.NET Core controllers, or if no routes match, lets legacy handlers have their turn at the request.

In this software, authentication is handled by custom middleware. It provides a number of non-standard authentication options, so ASP.NET's standard `UseAuthentication` and associated configuration wasn't goign to cut it, along with the fact that the legacy handlers relied on the context set up by the custom middleware.

> Authentication in ASP.NET generally has one job: work with the user to socilict and validate credentials, and upon successful completion of this process establish a `Principal` that the rest of the application can work with through the lifecycle of a request, made available through `HttpContext.User`. This particular concept isn't new to ASP.NET Core - ASP.NET (and other .NET based frameworks) have done it or similar things for a very long time

My goal was to enable _authorization_ of the established principal when requests came in to our new endpoints. In this case it would be very basic authorization - if there was an authenticated principal, let the request proceed to the new controller endpoints. If there was not, _thou shalt not pass (401)_.

I thought this would be reasonably simple. A small amount of reading lead me to set up our controller endpoints with the `RequireAuthorization` [endpoint metadata](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing?view=aspnetcore-3.1#configuring-endpoint-metadata), which applies the `Default Authorization Policy` to the configured routes.

```C#
webHostBuilder.Configure(app =>
{
    ...
    .UseRouting()
    .UseAuthorization()
    .UseEndpoints(builder => builder.MapControllers().RequireAuthorization())
    ...
}
```

I'd expect this to apply the default authorization policy, which \*\* simply enforces that the incoming request's User is not anonymous, and has been authenticated.

I also make sure to set up the required authorization services in the service configuration.

```C#
.ConfigureServices(services =>
{
    ...
    services.AddAuthorization();
    ...
}
```

Note that there are three distinct components here. One is `UseAuthorization`, which puts ASP.NET's `AuthorizationMiddleware` into our request pipeline, and enforces the defined policy for incoming requests depending on the route matched. The second is `RequireAuthorization`, which annotates our routes with `Authorize` metadata, ensuring the `AuthorizationMiddleware` has the information it needs to do it's job. The third is `AddAuthorization`, which registers authorization services such as the `AuthorizationPolicyProvider`, which the `AuthorizationMiddleware` needs to do it's job. If you don't do all three of these, things just won't work. Ask how I know.

Cool, with all of that in place, we should be successfully inspecting and authorizing whatever principal has been assigned to our HttpContext by our custom authentication middleware, right?

![Whoa there](/authorization-with-custom/friendo.png)

## Down The Rabbit Hole

So I fire up the web server, and Postman, and shoot a request at a `Hello World` type endpoint without any type of information to authenticate the request. And it doesn't reach the endpoint!

But it returns `200 OK`.

What on earth? I'm expecting `401 Unauthorized` - we haven't met the policy, so we should be returning an unauthorized response.

Queue re-reading the documentation, StackOverflow, and a bunch of blog posts n\* times. None of these adequately explain the behaviour I am seeing - almost all of of them focus on the 'happy path' of configuring out-of-the-box behaviour, and not mixing custom and out-of-the-box bits.

The only way I'm going to discover why the innards of the ASP.NET Core authorization code isn't returning what I expect is by _reading the code_. Now one way to do that would be to head to [Github](https://github.com/dotnet/aspnetcore) since ASP.NET Core is open source. But let's face it, that's digging for a needle in a very large haystack, and _no one got time for that_.

What I really want to do is to _debug_ the ASP.NET Core code as it processes my request. That's actually really easy to do thanks to the magic of two things: 1) Decompilation, and 2) Symbol Servers. I'm using [Rider](https://www.jetbrains.com/rider/), but the same feat is accomplishable with Visual Studio with Resharper (for decompilation goodness).

The first thing I want to do is add the appropriate symbol source, so that when I have decompiled code open, my debugger can step through it. So `Ctrl + Alt + S` gets me my settings window in Rider, and searching for `symbols` finds me the settings page I am looking for under `Tools > External Symbols`. You want to add `https://msdl.microsoft.com/download/symbols` to your list of Symbol servers.

Next, I want to crack open the entry point for where I want to start debugging. This is going to be the `AuthorizationMiddleware` class from ASP.NET Core. In Rider (or Resharper) I can `Ctrl + T` (because I don't use IntelliJ binding's like some sort of caveman) and search for that type, and it will come up in the list.

With this set up, I can `Alt+F5` (or just `F5` if you are in VS) and start stepping through ASP.NET Core code locally.

![magic](https://media.giphy.com/media/12NUbkX6p4xOO4/giphy.gif)

## The Problem

Stepping through the code, I could see the default authorization policy, once evaluated, would result in `Challenged`. This doesn't mean it struggled to figure out an answer 🥁. What it means is it wants to return a challenge back to the user - they did not meet the policies requirements. In this case the requirements are simply that the user is not anonymous.

It then calls `await context.ChallengeAsync();` to issue the challenge back to the user. This in turn calls `context.RequestServices.GetRequiredService<IAuthenticationService>().ChallengeAsync(context, scheme, properties)`. This resolves the `AuthenticationService`, which goes looking for a default authentication scheme, and 💥. We don't have one. Because we have our own custom authenticaiton middleware, remember?

This is the problem.

By default, `UseAuthorization` relies on you also using [AddAuthentication](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/policyschemes?view=aspnetcore-3.1) and providing a scheme so that the resolved `AuthenticationService` knows what sort of challenge to issue. If not, it will throw an exception. And if you are unlucky like me, you may be set up in such a way that the exception gets swallowed by another rogue piece of middleware, and you may end up with a `200 OK` instead.

So ultimately there is implicit coupling between authentication and authorization in ASP.NET Core's default middleware. I don't think there should be, but there is. Authentication should be responsible for one thing - establishing the request principal. Authorization should be responsible for one thing - determining if the request principal has access to the requested resources.

## The Solution

I've opted for a simple solution. I'll register a custom `IAuthenticationService` that only does one thing - when asked for a challenge, sets the response code to 401, and returns. It looks a bit like this:

```C#
public class ChallengeOnlyAuthenticationService : IAuthenticationService
    {
        public Task<AuthenticateResult> AuthenticateAsync(HttpContext context, string scheme)
        {
            throw new NotImplementedException();
        }

        public Task ChallengeAsync(HttpContext context, string scheme, AuthenticationProperties properties)
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            return Task.CompletedTask;
        }

        public Task ForbidAsync(HttpContext context, string scheme, AuthenticationProperties properties)
        {
            throw new NotImplementedException();
        }

        public Task SignInAsync(HttpContext context, string scheme, ClaimsPrincipal principal, AuthenticationProperties properties)
        {
            throw new NotImplementedException();
        }

        public Task SignOutAsync(HttpContext context, string scheme, AuthenticationProperties properties)
        {
            throw new NotImplementedException();
        }
    }
```

And is registered like so:

```C#
services.AddSingleton<IAuthenticationService, ChallengeOnlyAuthenticationService>();
```

This will return a 401 without caring about schemes as soon as an authorization policy fails.

Simples.
