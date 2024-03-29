# Identity Service in .NET
authentication is handled by the IAuthenticationService which is middleware

The registered authentication handlers and their configuration options are called "schemes".

przyklad dodawania schematow dla jwt i cookiesow:
```cs
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```
When using endpoint routing, the call to UseAuthentication must go:

- After UseRouting, so that route information is available for authentication decisions.
- Before UseEndpoints, so that users are authenticated before accessing the endpoints.

Autentykacja zapewnia **ClaimsPrincipal**

There are multiple authentication scheme approaches to select which authentication handler is responsible for generating the correct set of claims:

- Authentication scheme
- The default authentication scheme, discussed in the next section.
- Directly set HttpContext.User.

Authentication scheme == handler ktory generuje set claimsów

Schemey mozna laczyc w policy scheme

Authentication handler dziedziczy po IAuthenticationHandler (<T>) i autentykuje userow przez implementacje zachowania schemea.
Dla przychodzacego requestu handler konstruuje AuthenticationTicket ktory reprezentuje identity usera. 
Jezeli uzytkownik accessuje resource to moze byc:
- nie dopuszczony jezeli jego claimsy nie maja dostatecznych autoryzacji (**forbid**)
- Nie jest zautentykowany (**challenge**)

```cs RemoteAuthenticationHandler<TOptions>  ``` autentykuje remote, podaje mu sie CallbackPath ktora jest wywolywana po odpowiedzi handlera. Uzywa sie go z OAuthem i OIDC, JWT i cookiesy nie używają.

Akcja AUthenticate schematu autentykujacego jest odpowiedzialna za kontstrukcje identity usera. Zwraca **AuthenticateResult**.
Authenticate examples:
- A cookie authentication scheme constructing the user's identity from cookies.
- A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.

Challenge:
Authentication challenge examples include:

- A cookie authentication scheme redirecting the user to a login page.
- A JWT bearer scheme returning a 401 result with a www-authenticate: bearer header.
A challenge action should let the user know what authentication mechanism to use to access the requested resource.

Forbid:
An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to acces

forbid mowi ze user jest authenticated ale nie moze ogladac resourcea
Ex:
- A cookie authentication scheme redirecting the user to a page indicating access was forbidden.
- A JWT bearer scheme returning a 403 result.
- A custom authentication scheme redirecting to a page where the user can request access to the resource.

Asp net nie ma multi tenant support wiec uzywa sie **Orchard Core**

## Identity Service

uwaga Microsoft identity platform jest unrelated- to ewolucja do AD

Pare pojec:
- OAuth: protokół umożliwiający korzystnaie z zewnetrznych dostawcow identity jak google facebook
- Auth0: firma udostepniajaca server identity i gotowe jakby postawione cale auth mikroserwis
- identityServer 4: openId connect i OAuth framework do stawiania w aspnet corze- zamienił się w duende identity server
- OpenID: najnowszy protokół autentykacyjny.  OpenID Connect is an extension on top of OAuth 2.0

Duende Identity server pricing: ~5k dolcow miesiecznie

## BFF
Back end for front-ends- obecnie popularnie uzywane do zabezpieczania SPA

Principle of Least PRivilige:
Give user only minimum resources necessary. By default user has no access.


## binary thistle bearer with AAD

- **Digest**: authentication using hashed password, basically Basic Auth but processed,
- **Bearer authentication**: authentication using token, example implementation of token is **JWT**
- **NTLM**: Microsoft windows user auth

### Registration in Azure Active Directory
We register API, then there are Application (client) ID and Directory (tenant) ID.
Client ID is unique identifier of our API.
Tenant ID is uniqe to our AAD.
We expose API in azure, it gives us REsource ID.
We need to define API MAnifst where we specify API clients
```json
"appRoles": [ 
  { 
    "allowedMemberTypes": [ 
      "Application" 
    ], 
    "description": "Daemon apps in this role can consume the web api.",
    "displayName": "DaemonAppRole",
    "id": "6543b78e-0f43-4fe9-bf84-0ce8b74c06a3",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "DaemonAppRole"
  } 
]
```
We need to put keys in application settings (these are secrets! Normally dont put them in app settings):
```
ResourceId: api://56373378-b187-4ffe-9650-ccb784e32b13
Instance: https://login.microsoftonline.com/
TenantId: 1beb8417-6784-49e0-9555-4e6b5d238434
```
Client setup is done in the example for console so it doenst follow these notes.
Source: https://dotnetplaybook.com/secure-a-net-core-api-using-bearer-authentication/


From reddit comment https://www.reddit.com/r/dotnet/comments/okbo7i/wtf_happened_to_web_app_security_the_past_5_years/: 
Is your SPA being served by the web API project? If yes, then you don't need OIDC. ASP.NET Identity with the default cookie settings should be fine. Just configure the route that serves your SPA to require authorization. Once authorized, your SPA will have the appropriate auth cookie and that will automatically get sent along with every JS API request you make. The cookie will be http-only (the browser won't allow any JS code to directly access it) and encrypted. This combined with asp.net's default CSRF protections is about as secure as it gets. Your SPA will have to handle unauthorized web endpoint failures and redirect to login or reload the page or something when they occur so the API backend can redirect to Identity to get a fresh login.

Now, let's say you're hosting your SPA on Netlify at https://www.mysite.whatever. And you host you web api on digital ocean at https://api.mysite.whatever. You can't use cookie auth here since A) your API server isn't serving your SPA so it can't set the auth cookie. B) even if you somehow get a cookie or a jwt, it won't be http-only or secure. It won't get automatically sent along with your API requests. It'll be completely open to any malicious scripts to steal or click jack. OIDC can get you most of the way there as far as solving this problem. The protocol is verifiable and, in the end you'll have a cryptographically signed jwt and a pretty decent JS library to manage the tokens. Now, most solutions you'll find will still have you storing this jwt in the open in something like localStorage. This still leaves you vulnerable to some malicious scripts stealing your valid token. If your app is on an internal network this may be an acceptable risk. If your app is on the public internet, however, the most secure design is the backend-for-frontend model. Basically you write a super thin backend server that serves your SPA and handles the OIDC auth and can set the secure cookie. That backend would then proxy all API requests to your API server running on a different domain, sending the OIDC auth token. You get best of both worlds: secure http-only cookies that "just work" with all your API calls (no js library to manage tokens in the open and append authorization headers to all you API requests), and a nice decoupling of your API server from your web apps.

It's my advice that, unless you absolutely, 100% need to decouple your frontend from your API server, just have your API serve your frontend and skip all the OIDC/IdentityServer/backend-for-frontend headache.

OIDC== OpenID Connect 