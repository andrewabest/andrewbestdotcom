---
title: "Learn Authentication The Hard Way"
date: 2020-01-10T18:00:00+10:00
draft: true
---

If you are a software developer, security is one of _your primary concerns_. If you ship code, and that code deals with any sort of sensitive or personal information, you need to ensure your code and the systems you build allow people to transact on your systems safely and securely, free from fear of compromise or consequence. Your user's security is not [Someone Else's Problem](https://en.wikipedia.org/wiki/Somebody_else%27s_problem).

The prevailing advice for building secure modern software systems is to _not roll your own security_. I mentioned this in my previous post on [securely acessing Azure APIs](https://www.andrew-best.com/posts/please-sir-can-i-have-some-auth/), but didn't dwell on the topic. I think it deserves more attention, as there are conflicting pieces of advice here. The first is that _we_ should be responsible for ensuring our systems are secure. The second is that we should _delegate_ security to experts.

These statements sound like they might be contradictory, but they don't have to be. Let's try and combine them into a statement that reflects their combined intent:

> If you are an expert, be responsible for security. If you are not an expert, be accountable for security

What this means is although we might not hand-craft our own security solutions, we need to take accountability for the solutions we choose to use and integrate, solutions that are produced by the experts. To take accountability for these choices, we need to understand these solutions - there is no accountability without understanding.

I'm going to slightly pivot in terminology now, and talk about _authentication_ solutions, as opposed to the more generic sounding _security_ solutions. We are talking software here, and authentication is the primary security mechanism that ensures people can only access what they should be able to access within our systems.

Most modern application authentication solutions you'll encounter are built on published standards. Whilst implementations tend to provide their own specific abstractions on top of these standards - if you understand the _how_ and _why_ of the standard, you will have a solid understanding of how the solution will make _your solution_ secure.

These standards - primarily the [OAuth 2 RFC](https://tools.ietf.org/html/rfc6749) or the [OpenID Connect Spec](https://openid.net/specs/openid-connect-core-1_0.html) seem unapproachable at first glance. They read like legal documents - huge tables of context, tonnes of defined terms, and dry prose. But standards are key to us developing our knowledge of how modern authentication solutions work, and will allow us to be **accountable** when we select a security solution for our own software.

Seek First To Understand
===

If you aren't yet comfortable with the high level concepts involved in modern application authentication, I can highly recommend a presentation my good friend [Rob Moore](https://robdmoore.id.au/) gave at NDC Sydney which provides a top-notch set of metaphors that make the different roles that play a part in the solution easier to grasp. 

{{< youtube X6a9bjNutEw >}}

Once you are comfortable with the high level concepts, there is no avoiding it - we need to read through these specs. Anyone who has cracked open an RFC before knows that they are not the epitomy of user experience. That being said, there are ways to make them more palatable. 

[RFCRestyle](https://github.com/FredGandt/RFCRestyle) is a Chrome Extension that formats any *.ietf.org page into a more consumable format - better font usage, colouring (dark mode!), and most thankfully an always-present table of contents that is available to you as you continue down the document. 

![Restyled ietf RFC document](/roll-your-own/rfcrestyle.png)

The Hard Way
===

I learn best not by reading things, but by doing things. So I thought the best way to embed my knowledge of these standards would be to have a go at implementing them. Not all of them though - I'll leave that in the capable hands of experts like [Dominick Baier](https://leastprivilege.com/).

What I want to implement is the [client](https://openid.net/specs/openid-connect-core-1_0.html#Authentication) component of the solution - this should give me a good understanding of the process flow between my software (the client) and the thing that is responsible for authentication (the server), and the responsibilities of each component in ensuring the user's security.

More specifically, I've chosen to implement a client that will perform the [Authorization Code Flow](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth). It is [one of the two flows](https://leastprivilege.com/2019/09/09/two-is-the-magic-number/) we need to know to cover most of our application authentication needs. We will mostly adhere to [the guidance](https://openid.net/specs/openid-connect-core-1_0.html#RPMTI) on what parts of the specification we must implement, but deviate a little at times to ensure we have a well-rounded understanding of the components of the solution.

I will use Dominick's fantastic [Identity Server 4](http://docs.identityserver.io/en/latest/) to fulfill the server component of the specifications. Identity Server 4 supports a variety of OAuth and OpenID Connect [specifications](http://docs.identityserver.io/en/latest/intro/specs.html) and is [OpenID certified](https://openid.net/certification/), so we can safely assume it will faithfully implement and represent the specifications. Plus it is open source, so if we need to dig into its code at any point to reason about its behaviour - we can!

Prerequisites
---

I've created a sample that will get us up and running quickly with [Identity Server 4 in Docker](https://github.com/andrewabest/LocalDevIdentityServer) that I will be working against.

I'll be building my client as a bare-bones Asp.Net Core WebAPI project - but the code should largely be translatable to any other language and web framework you might want to use. 

Implementation
===

Here are the steps [taken from the specification](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps) that we are going to facilitate to perform an end-to-end Authentication Code Flow:

```
1. Client prepares an Authentication Request containing the desired 
   request parameters.
2. Client sends the request to the Authorization Server.
3. Authorization Server Authenticates the End-User.
4. Authorization Server obtains End-User Consent/Authorization.
5. Authorization Server sends the End-User back to the Client with an
   Authorization Code.
6. Client requests a response using the Authorization Code at the Token
   Endpoint.
7. Client receives a response that contains an ID Token and Access 
   Token in the response body.
8. Client validates the ID token and retrieves the End-User's Subject 
   Identifier.
```

**1. Client prepares an Authentication Request containing the desired request parameters**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

This is the very first step in our process. A user has arrived at our software without any evidence of being authenticated, and our software needs to prepare an Authentication Request to ask the Authorization Server nicely to authenticate them on our behalf. 

The spec says we can either use a GET or POST for the interaction, but if we GET, we need to ensure we [serialize our query parameters](https://openid.net/specs/openid-connect-core-1_0.html#QuerySerialization) on the query string. We will be using this approach, as it tends to be the 'standard' way of doing it. 

Most of this is fairly straight forward, and composing the request looks like this:

```C#
// 3.1.2.1.  Authentication Request
var requestContent = 
    "scope=openid%20profile%20email&" +
    "response_type=code&" +
    "client_id=client&" +
    "redirect_uri=https%3A%2F%2Flocalhost%3A8090%2Fapi%2Fauthorize&" +
    $"state={WebUtility.UrlEncode(AntiForgeryToken.GetForCurrentUser())}";
```

👀 but what is this line about?

```C#
$"state={WebUtility.UrlEncode(AntiForgeryToken.GetForCurrentUser())}"
```

This line is the result of a _spec rabbit hole_. A spec rabbit hole is when you see a seemingly innocuous statement, and then spend the next _n hours_ chasing it through the spec and internet. This particular rabbit hole opens up like so                  

```
3.1.2.1.  Authentication Request
...
state
    RECOMMENDED. Opaque value used to maintain state between the
    request and the callback. Typically, Cross-Site Request Forgery
    (CSRF, XSRF) mitigation is done by cryptographically binding the
    value of this parameter with a browser cookie.
```

This doesn't really tell us much about how we should go about implementing the CSRF mitigation recommended. If we continue reading, we will come across this shortly after

```
3.1.2.7.  Authentication Response Validation
    When using the Authorization Code Flow, the Client MUST validate 
    the response according to RFC 6749, especially Sections 4.1.2 and 
    10.12.
```

This happens continuously throughout the OpenID Connect specification. Since it sits on top of OAuth 2.0 (RFC 6749), it does not redefine it, but references it at appropriate times. This makes sense, but leads to us needing to jump between the specs to build a complete picture.

If we follow the rabbit hole over to RFC 6749 10.12, we arrive at

```
10.12. Cross-Site Request Forgery
...
    The client MUST implement CSRF protection for its redirection URI.
    This is typically accomplished by requiring any request sent to the
    redirection URI endpoint to include a value that binds the request
    to the user-agent's authenticated state (e.g., a hash of the 
    session cookie used to authenticate the user-agent).

    The client SHOULD utilize the "state" request parameter to deliver
    this value to the authorization server when making an authorization
    request.
...
    The binding value used for CSRF protection MUST contain a
    non-guessable value (as described in Section 10.10)
```

Quick! Over to 10.10!

```
10.10. Credentials-Guessing Attacks
... 
    The probability of an attacker guessing generated tokens (and other
    credentials not intended for handling by end-users) MUST be less
    than or equal to 2^(-128) and SHOULD be less than or equal to
    2^(-160).
```

_Alrighty then_.

This is where the spec leaves us to our own devices to figure out what it is talking about. 

What it means in more-plain-language is that whatever token you use should have at least 128 bits of random information within it, and preferably have at least 160 bits of random information (often called _entropy_). [OWASP supply a nice formula](https://www.owasp.org/index.php/Insufficient_Session-ID_Length) for calculating how long it would take to force a given session identifier by token length.

OWASP also happen to supply some great advice around implementing security mechanisms including CSRF. Let's use their [HMAC based token pattern](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#hmac-based-token-pattern), and combine that with a SessionId that meets our randomness requirement. That should satisfy our spec, and allow us to ascend out of this rabbit hole.

```C#
public static class UserProvider
{
    private static User _user;
    public static User Current => _user ??= new User();
}

public class User
{
    public User()
    {
        var id = new byte[20];
        RandomNumberGenerator.Create().GetBytes(id);
        SessionId = Convert.ToBase64String(id);
    }

    public string SessionId { get; }
}

public class AntiForgeryToken
{
    public static string Secret = "super-secret";

    // HMAC-Based CSRF Token Generation
    // https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#hmac-based-token-pattern
    //
    public static string GetForCurrentUser()
    {
        var now = DateTimeOffset.UtcNow.ToUnixTimeSeconds();
        return CreateToken(now.ToString());
    }

    private static string CreateToken(string currentTimeStamp)
    {
        var secretKeyBytes = Encoding.UTF8.GetBytes(Secret);
        var stringToSignBytes = Encoding.UTF8.GetBytes($"{UserProvider.Current.SessionId}{currentTimeStamp}");

        using var hmac = new HMACSHA256(secretKeyBytes);
        var signatureBytes = hmac.ComputeHash(stringToSignBytes);
        return $"{Convert.ToBase64String(signatureBytes)}:{currentTimeStamp}";
    }

    ...
}
```

> Neat side-learning - Dotnet Core now has a `RandomNumberGenerator` class which by default exposes a `RNGCryptoServiceProvider` instance to work with to generate cryptographically-strong psuedo-random numbers in a cross-platform friendly way!

I'm going to skip the `OPTIONAL` parameters for the sake of this exercise and move on.

TODO: 15.5.2.  Nonce Implementation Notes https://openid.net/specs/openid-connect-core-1_0.html#CodeNotes

=== ARTICLE SEPARATOR ===

**2. Client sends the request to the Authorization Server**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

Now it is time to move on to _sending_ the request we spent so much time crafting. 

This is reasonably easy, but we do need to remember - the authorization request requires user interaction, so we can't just send it server-to-server - the Resource Owner (person using a browser) needs to issue the request via their browser so that they can interact with the Authorization Server. The server will then call our Client application back on the supplied `redirect_uri` on the query string as described.

```
3.1.2.1.  Authentication Request
...
redirect_uri
    REQUIRED. Redirection URI to which the response will be sent. 
    This URI MUST exactly match one of the Redirection URI values for
    the Client pre-registered at the OpenID Provider, with the matching
    performed as described in Section 6.2.1 of [RFC3986]...
```

We can accomplish this by issuing a redirect like so

```C#
[HttpGet("login")]
public IActionResult Login()
{
    // 3.1.2.  Authorization Endpoint
    const string authorizationEndpoint = "http://localhost:8080/connect/authorize";

    // 3.1.2.1.  Authentication Request
    var requestContent = 
        "scope=openid%20profile%20email&" +
        "response_type=code&" +
        "client_id=client&" +
        "redirect_uri=https%3A%2F%2Flocalhost%3A8090%2Fapi%2Fauthorize&" +
        $"state={WebUtility.UrlEncode(AntiForgeryToken.GetForCurrentUser())}";

    return Redirect($"{authorizationEndpoint}?{requestContent}");
}
```

The key learning here is from this line: _This URI MUST exactly match one of the Redirection URI values for the Client pre-registered at the OpenID Provider_. This ensures our Authentication Server (or OpenID Provider, or Identity Provider, yay naming things!) will only return tokens to a known endpoint configured within it, preventing a range of attacks that could occur otherwise.

**3. Authorization Server Authenticates the End-User**

**4. Authorization Server obtains End-User Consent/Authorization**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequestValidation)

These responsibilities are taken care of by Identity Server 4 for us, and cover `3.1.2.2.  Authentication Request Validation` - `3.1.2.4.  Authorization Server Obtains End-User Consent/Authorization` in the Open ID Connect specification.

We won't dig into these specifications in this post, but it is worth reading them, as these are the things you'll expect your authentication solution to enforce, and lets you reason about its configuration and expected behaviour.

**5. Authorization Server sends the End-User back to the Client with an Authorization Code**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#AuthResponse)

Once we have successfully authenticated with our Authentication Server, it is going to want to send us our Authorization Code - we will exchange this later for identity and access tokens, and optionally a refresh token too.

```
3.1.2.5.  Successful Authentication Response
...
    When using the Authorization Code Flow, the Authorization Response 
    MUST return the parameters defined in Section 4.1.2 of OAuth 2.0 
    [RFC6749] by adding them as query parameters to the redirect_uri 
    specified in the Authorization Request using the 
    application/x-www-form-urlencoded format...
```

And then jump over to RFC 6749 4.1.2
```
...
For example, the authorization server redirects the user-agent by 
sending the following HTTP response:

HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
        &state=xyz
...
```

RFC 6749 4.1.2 and 4.1.2.1, along with the OpenID Connect Specification 3.1.2.6 define the complete response schema, which can be deserialised into the following model

```C#
public class AuthenticationResponse
{
    public string Code { get; set; }
    public string Scope { get; set; }
    public string State { get; set; }
    public string Error { get; set; }
    [FromQuery(Name = "error_description")]
    public string ErrorDescription { get; set; }
    [FromQuery(Name = "error_uri")]
    public string ErrorUri { get; set; }
}
```

But wait! Just recieving the Authentication Response isn't all we have to do here, we also need to validate it. Remember our CSRF token? This is where we need to validate it when we recieve it back from the Authentication Server.

```
3.1.2.7.  Authentication Response Validation
    When using the Authorization Code Flow, the Client MUST validate 
    the response according to RFC 6749, especially Sections 4.1.2 and 
    10.12.
```

The following code in our `AntiForgeryToken` class will complete our token validation mechanism

```c#
public static bool IsValid(string token)
{
    var timestampLocation = token.LastIndexOf(':');
    var timestamp = token[^timestampLocation..];

    if (DateTimeOffset.FromUnixTimeSeconds(long.Parse(timestamp)).AddHours(1) < DateTimeOffset.UtcNow)
    {
        throw new InvalidOperationException("Expired");
    }

    return CreateToken(timestamp).Equals(token);
}
```

Which we can then consume within our redirect endpoint

```C#
[HttpGet("authorize")]
// 3.1.2.5.  Successful Authentication Response
// 3.1.2.6.  Authentication Error Response
public async Task<IActionResult> Authorize([FromQuery] AuthenticationResponse response)
{
    // 3.1.2.7.  Authentication Response Validation
    // https://tools.ietf.org/html/rfc6749#section-10.12
    if (AntiForgeryToken.IsValid(response.State) == false)
    {
        return Forbid();
    }
    ...
}
```

**6. Client requests a response using the Authorization Code at the Token Endpoint**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#TokenEndpoint)

We have now recieved our Authorization Code, and verified the authenticity of our CSRF and nonce protection measures. We want to exchange these for an Access Token, and ID Token. To do this, we need to construct a valid Access Token Request 

```
4.1.3. Access Token Request
...
   grant_type
         REQUIRED.  Value MUST be set to "authorization_code".

   code
         REQUIRED.  The authorization code received from the
         authorization server.

   redirect_uri
         REQUIRED, if the "redirect_uri" parameter was included in the
         authorization request as described in Section 4.1.1, and their
         values MUST be identical.
...
```

```C#
// 3.1.3.1.  Token Request
var requestContent = new List<KeyValuePair<string, string>>()
{
    new KeyValuePair<string, string>("grant_type", "authorization_code"),
    new KeyValuePair<string, string>("code", response.Code),
    new KeyValuePair<string, string>("redirect_uri", "https://localhost:8090/api/authorize")
};
```

However we are then confronted with this statement

```
If the Client is a Confidential Client, then it MUST authenticate to
the Token Endpoint using the authentication method registered for 
its client_id, as described in Section 9.
```

**New Term Alert** Are we a Confidential Client? Since there are two specifications we are working with, and one sits on top of the other, sometimes progressive disclosure fails and we are confronted with a term we haven't encountered before. If we head over to RFC 6749 and do a quick `Ctrl + F` for `confidential`, we will quickly discover the following:

```
2.1. Client Types
...
   confidential
      Clients capable of maintaining the confidentiality of their
      credentials (e.g., client implemented on a secure server with
      restricted access to the client credentials)
...
   web application
      A web application is a confidential client running on a web
      server.  Resource owners access the client via an HTML user
      interface rendered in a user-agent on the device used by the
      resource owner.  The client credentials as well as any access
      token issued to the client are stored on the web server and are
      not exposed to or accessible by the resource owner.
```

Since we are implementing a Client as a server-side web application (a .NET Core WebAPI in this case) we _are_ a confidential Client, and therefore MUST implement this. 

We have registered a client secret of `secret` in our [Identity Server 4](https://github.com/andrewabest/localDevIdentityServer/) instance via its [Secret](http://docs.identityserver.io/en/latest/topics/secrets.html) support.

As per `9.  Client Authentication` in the OpenID Connect specification, the default client authentication mechanism is Basic Authentication, which is further supported by `2.3.1. Client Password` in RFC 6749. Identity Server will happily support Basic Auth or including these credentials in the POST body. Basic Authentication is a well understood mechanism, so we will use that, but won't delve into it here beyond looking at the code we will write to apply it to our token endpoint call. 

```C#
// https://tools.ietf.org/html/rfc6749#section-2.3.1
var authorizationHeader = new AuthenticationHeaderValue("Basic", 
    Convert.ToBase64String(Encoding.UTF8.GetBytes("client:secret")));

var client = new HttpClient();
client.DefaultRequestHeaders.Authorization = authorizationHeader;

var result = await client.PostAsync(tokenEndpoint, 
    new FormUrlEncodedContent(requestContent));
```

**7. Client receives a response that contains an ID Token and Access Token in the response body**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)

Before this step, the Authorization Server would have performed `3.1.3.2.  Token Request Validation` and ensured our request to it was well formed and secure.

Now we have supplied the `token` endpoint of our Authorization Server with our Access Code, it is going to want to return us our ID and Access Tokens, and optionally a Refresh token.

`3.1.3.3.  Successful Token Response` from OpenID Connect, along with `4.1.4. Access Token Response` and `5.2. Error Response` from RFC 6749 give us our complete object model that we can deserialize responses into.

```C#
public class TokenResponse
{
    public string AccessToken { get; set; }
    public string TokenType { get; set; }
    public string RefreshToken { get; set; }
    public int ExpiresIn { get; set; }
    public string IdToken { get; set; }
    public string Error { get; set; }
    [FromQuery(Name = "error_description")]
    public string ErrorDescription { get; set; }
    [FromQuery(Name = "error_uri")]
    public string ErrorUri { get; set; }
}
```

When we are digging through `3.1.3.3.  Successful Token Response` we run across this line

```
3.1.3.3.  Successful Token Response
...
    The OAuth 2.0 token_type response parameter value MUST be Bearer,  
    as specified in OAuth 2.0 Bearer Token Usage [RFC6750]
```

**New Spec Alert** Does this mean we need to dig into ANOTHER spec in order to ensure our Client implementation is correct? 🙊🙉🙈

It turns out the answer is no. [RFC 6750](https://tools.ietf.org/html/rfc6750) was originally created to allow for multiple token usage types, with Bearer being the original, and one called "Proof of Possession" to follow. [But it never did](https://leastprivilege.com/2020/01/15/oauth-2-0-the-long-road-to-proof-of-possession-access-tokens/). It seems even published specifications aren't immune to _just-in-case engineering_.


=== ARTICLE SEPARATOR ===

**8. Client validates the ID token and retrieves the End-User's Subject Identifier**

[Specification Link](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponseValidation)

We are now on the home stretch, all we need to do is to validate the payload we have received, and then we will have successfully completed the Authorization Code Flow!

```
3.1.3.5.  Token Response Validation
...
    Follow the validation rules in RFC 6749, especially those in 
    Sections 5.1 and 10.12.
...
```

So this is an interesting piece of advice. The first part of the statement is a cover-all for the OAuth 2.0 spec which isn't particularly useful. If we start our way from `4.1.4. Access Token Response` in RFC 6479, we can see it is mostly ensuring we only accept specified values from our Authorization Server, and ignore anything else - this covers `Section 5.1` as well.

The interesting part comes in advising us to follow `Section 10.12` - which we covered earlier. It was our CSRF protection mechanism for the initial redirection and callback, ensuring unwanted types couldn't randomly call our redirect endpoint. Why would we need to implement CSRF protection on a single HTTPS call to the `token` endpoint? Well we don't need to - it doesn't make any sense to, as the communication isn't succeptible to CSRF. So published specifications can also have _bugs_.

Our deserialization process will ensure the statement is adhered to, so let's move on to validating our tokens. I'll only address validation items that apply to us within `3.1.3.7.  ID Token Validation` - the skipped items are configuration-specific, and tend to address less common or more sophisticated scenarios, beyond the scope of this article.

```
3.1.3.7.  ID Token Validation

Clients MUST validate the ID Token in the Token Response in the 
following manner:
...
    2. The Issuer Identifier for the OpenID Provider (which is typically
     obtained during Discovery) MUST exactly match the value of the 
     iss (issuer) Claim.
...
```

**New Term Alert** What is Discovery? Once again, let's `Ctrl+f` our way to success. Way up in `1. Introduction` in the Open ID spec, the following information is offered:

> This specification assumes that the Relying Party has already obtained configuration information about the OpenID Provider, including its Authorization Endpoint and Token Endpoint locations. This information is normally obtained via Discovery, as described in OpenID Connect Discovery 1.0.

We have been working through the OpenID _Core_ specification, which details the main functionality OpenID Connect layers on top of the OAuth 2.0 spec. However OpenID also defines a number of other specifications dealling with ancillary concerns - one of them being _Discovery_.

[4.  Obtaining OpenID Provider Configuration Information](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig) within the Discovery specification tells us how to obtain provider information - by issuing a `GET` request to `/.well-known/openid-configuration` - commonly referred to as the 'well known configuration endpoint`.

The spec provides us with a schema for the expected response. At this point we are going to start dealing with _lots_ of json formatted data. To make this job easier, I'll be using [QuickType](https://app.quicktype.io/#l=cs&r=json2csharp), an awesome tool for scaffolding C# classes from json schemas, which comes with the added benefit of providing both neat type inference _and_ serialization helpers so that you can just 'plug and play' what it supplies.

```C#
public class OpenIDConfiguration
{
    [JsonProperty("issuer")]
    public string Issuer { get; set; }

    [JsonProperty("jwks_uri")]
    public string JwksUri { get; set; }

    ...
}
```
_[OpenIDConfiguration.cs](https://github.com/andrewabest/openid-client/blob/master/OpenIDClient/Models/OpenIDConfiguration.cs)_

We now have our Authorization Server's OpenID Configuration information, so we can now begin validating the ID Token we recieved in the response from the `/token` endpoint request.

```
3.1.3.7.  ID Token Validation
...
    2. The Issuer Identifier for the OpenID Provider (which is typically
obtained during Discovery) MUST exactly match the value of the iss 
(issuer) Claim.
    3. The Client MUST validate that the aud (audience) Claim contains
its client_id value registered at the Issuer identified by the iss 
(issuer) Claim as an audience. 
...
```

```c#
const string introspectionEndpoint = "http://localhost:8080/.well-known/openid-configuration";
var configurationJson = await new HttpClient().GetStringAsync(introspectionEndpoint);
var configuration =
    JsonConvert.DeserializeObject<OpenIDConfiguration>(configurationJson, Converter.Settings);

// 3.1.3.7.  ID Token Validation
body.Iss.MustEqual(configuration.Issuer, "Invalid issuer encountered");
body.Aud.MustEqual("client", "Invalid audience encountered");
```

Just when you thought we were out of the woods with this implementation, we come across this statement:

```
3.1.3.7.  ID Token Validation
...
    6. If the ID Token is received via direct communication between the
 Client and the Token Endpoint (which it is in this flow), the TLS 
 server validation MAY be used to validate the issuer in place of 
 checking the token signature. The Client MUST validate the signature 
 of all other ID Tokens according to JWS using the algorithm 
 specified in the JWT alg Header Parameter. The Client MUST use the 
 keys provided by the Issuer.
...
```

Although our scenario has us communicating directly with the Token Endpoint (and we should be using HTTPS), understanding how tokens and their signatures function is core to our understanding of how we should handle and utilize them in our solutions. If we skip this step, we have a glaring gap in our knowledge of the overall authentication solution. So, let's dive in!

**New Spec(s) Alert** [The JWS specification](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41) describes the mechanism used to ensure JWTs are made tamper-proof and validatable. The JWS specification relies on [the JWA specification](https://tools.ietf.org/html/rfc7518) to define what cryptographic mechanisms are available to generate signatures. The JWS specification is strongly linked to [the JWT specification](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32), which refers back to the JWS specification when describing [how to validate JWTs](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32#section-7.2). 

![Run away](/roll-your-own/homer.gif)

Come back! We aren't going to have to read three new specs end-to-end, although this is the danger of diving into specifications - sometimes you don't know where the rabbit holes will end. In our case we are only concerned with validating the signature on our ID Token, so let's try and limit our exploration of the token specifications to this topic.

Starting with [7.2. Validating a JWT](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32#section-7.2), there are some basic formatting and encoding checks to perform, but the interesting part starts with:

```
   6.   Determine whether the JWT is a JWS or a JWE using any of the
        methods described in Section 9 of [JWE].

   7.   Depending upon whether the JWT is a JWS or JWE, there are two
        cases:

        *  If the JWT is a JWS, follow the steps specified in [JWS] for
           validating a JWS.  Let the Message be the result of base64url
           decoding the JWS Payload.
```

I find their usage of the labels "JWS" and "JWE" to infer a type of token, rather than the cryptographic mechanism used to secure it, to be very confusing. The short of it is **JWS == signed token, JWE == encrypted token**. Both are JWTs. 

If we jump across to [Section 9](https://tools.ietf.org/html/rfc7516#section-9) of the JWE specification as directed, it gives us a straightforward way to determine which type we might be dealing with - JWS uses a three segment token delimited by `.`, and JWE uses a five segment token delimited by `.`. If you want a more sophisticated way, you can examine the value of the `alg` header parameter - if it is one of the values defined in [Section 3](https://tools.ietf.org/html/rfc7518#section-3) of the JWA specification, it is an algorithm for digital signatures or MAC, and the token is therefore a JWS JWT.

Which algorithm is used to sign the token will depend on what our Client's registration with the Authorization Server. OpenID Connect specifies a default of `RS256`, which refers to the `RSASSA-PKCS1-v1_5 using SHA-256` algorithm. 

`RS256` utilises a [Digital Signature](https://en.wikipedia.org/wiki/Message_authentication_code)-based approach, which involves using an asymmetric key pair (public/private keys) along with a strong hashing algorithm , composing the _JWS Signing Input_ out of the JWT contents, computing the hash over the input, encrypting the has using the private key from the key pair, and appending the hash to the JWT. If you really want to crack open another spec and dive into the guts of how this works, you can read [RSA Cryptography - 8.2. RSASSA-PKCS1-v1_5](https://tools.ietf.org/html/rfc3447#section-8.2).

Note that this digital signature is more sophisticated than a simple [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) based approach - the use of an asymmetric key pair makes stronger guarantees of a signatures authenticity, where the MAC approach simply relies on a single shared secret being available to all parties involved. 

The [JWS specification - 5.2. Message Signature or MAC Validation](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41#section-5) tells us what we need to do to validate our JWS token:

```
Validate the JWS Signature against the JWS Signing Input
ASCII(BASE64URL(UTF8(JWS Protected Header)) || '.' ||
BASE64URL(JWS Payload)) in the manner defined for the algorithm
being used, which MUST be accurately represented by the value of
the "alg" (algorithm) Header Parameter, which MUST be present.
```

Clear as mud right - _in the manner defined for the algorithm being used_ is a super useful instruction! 

This is instruction is going to cause us to pore over the JWS and JWE spec to try and find more explicit instructions, and likely start googling like mad for implementations in our language of choice. 

Our signing algorithm `RS256` uses an asymmetric key-pair to do its job, and we need to retrieve the public key of this key pair so that we can validate the signature generated by the algorithm. 

[The JWS specification](https://tools.ietf.org/html/rfc7515#section-4.1.2) specifies the location where keys can be retrieved from, and references YET ANOTHER specification for how keys are represented within JWTs - [the JWKs specification](https://tools.ietf.org/html/rfc7517). [Appendix B](https://tools.ietf.org/html/rfc7517#appendix-B) provides a neat example of how JWK represents a RSA public key.

Piecing all of this together, what we need to accomplish is:

1. Retrieve our RSA key in JWK format via the endpoint defined in our Authorization Server's _well known configuration_
2. Follow the [JWS specification - Section 5.2](https://tools.ietf.org/html/rfc7515#section-5.2) to construct the _JWS Signing Input_ in the format `ASCII(BASE64URL(UTF8(JWS Protected Header)) || '.' || BASE64URL(JWS Payload))`
3. Compute the hash of the _JWS Signing Input_ with `SHA256`.
4. Verify the computed hash against the signature we recieved in the third segment of our ID token 
5. Profit! If the verification passes, we can be comfortable that the token we have recieved could only have been constructed by our Authorization Server, and that it has not been tampered with in transit.

```C#
var rawHeader = DecodeSegment(idToken, TokenSegments.Header);
var rawBody = DecodeSegment(idToken, TokenSegments.Body);

// IETF JWS Draft 
// 5.2. Message Signature or MAC Validation
// https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41#section-5.2

var keysetJson = await new HttpClient().GetStringAsync(configuration.JwksUri);
var keyset = JsonConvert.DeserializeObject<KeySet>(keysetJson, Converter.Settings);

var rsa = RSA.Create();
rsa.ImportParameters(new RSAParameters
{
    Exponent = WebEncoders.Base64UrlDecode(keyset.Keys[0].E),
    Modulus = WebEncoders.Base64UrlDecode(keyset.Keys[0].N),
});

var canonicalPayload =
    Encoding.ASCII.GetBytes(
        $"{WebEncoders.Base64UrlEncode(Encoding.UTF8.GetBytes(rawHeader))}." +
        $"{WebEncoders.Base64UrlEncode(Encoding.UTF8.GetBytes(rawBody))}");

var hash = SHA256.Create().ComputeHash(canonicalPayload);

// https://tools.ietf.org/html/rfc3447#section-8.2.1
var result = rsa.VerifyHash(hash, rawSignature, HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);
    
if (!result) throw new InvalidOperationException("The supplied signature does not match the calculated signature");

...

private enum TokenSegments
{
    Header = 0,
    Body,
    Signature
}

private static string DecodeSegment(string token, TokenSegments desiredSegment)
{
    var tokenSegments = token.Split(".");
    if (tokenSegments.Length != 3) throw new InvalidOperationException("Unexpected token format encountered");

    var target = tokenSegments[(int)desiredSegment];

    // NOTE: WebEncoders.Base64UrlDecode deals with IDSrv truncating padding from encoded tokens
    var unencodedBytes = WebEncoders.Base64UrlDecode(target);
    var segment = Encoding.UTF8.GetString(unencodedBytes);

    return segment;
}
```

And now, we understand ~~JW(T/S/E/K)~~ _tokens_.

![So smart](/roll-your-own/homer-smart.gif)

With signature verification out of the way, the last part of the ID token we need to verify is:

```
3.1.3.7.  ID Token Validation

    9. The current time MUST be before the time represented by the exp 
    Claim.
```

This is much easier to do - although we do need to understand what format the time is expressed in, and that is an implementation detail left up to the implementer of the Authorization Server. Identity Server uses seconds from Unix epoch.

```C#
...

if (DateTimeOffset.FromUnixTimeSeconds(body.Exp) <= DateTimeOffset.UtcNow)
{
    throw new InvalidOperationException("Token has expired");
}
...
```

The final specification for the Authorization Code Flow is:

```
3.1.3.8.  Access Token Validation
When using the Authorization Code Flow, if the ID Token contains an 
at_hash Claim, the Client MAY use it to validate the Access Token in the
 same manner as for the Implicit Flow, as defined in Section 3.2.2.9, 
 but using the ID Token and Access Token returned from the Token Endpoint.
```

Although it isn't a _MUST_, anything regarding how we validate the payloads delivered by this solution will boost our understanding of it. If we go over to `3.2.2.9.  Access Token Validation` as directed, our instructions are to:

```
Hash the octets of the ASCII representation of the access_token...
Take the left-most half of the hash and base64url encode it.
The value of at_hash in the ID Token MUST match the value produced in 
the previous step.
```

Did it just give a succinct and explicit explanation of how to implement one of its specifications? 

![Best friends](/roll-your-own/best-friends.gif)

```C#
// 3.1.3.8.  Access Token Validation
// 3.2.2.9.  Access Token Validation
// https://openid.net/specs/openid-connect-core-1_0.html#ImplicitTokenValidation
private static TokenValidationResult ValidateAccessToken(string idToken, string accessToken)
{
    var rawBody = DecodeSegment(idToken, TokenSegments.Body);
    var body = JsonConvert.DeserializeObject<JwtBody>(rawBody, Converter.Settings);

    var octets = Encoding.ASCII.GetBytes(accessToken);
    var hash = SHA256.Create().ComputeHash(octets);
    var encoded = WebEncoders.Base64UrlEncode(hash[..(hash.Length / 2)]);

    if (encoded.Equals(body.AtHash) == false) throw new InvalidOperationException("The supplied signature does not match the calculated signature");

    return new TokenValidationResult { Succeeded = true };
}
```

And with that, we are **DONE**.

Postface
===

All of the code referenced in this article is [available on GitHub](https://github.com/andrewabest/openid-client) - I'd actually recommend cloning this and following along so you have a little more visibility as to how it all pieces together.

