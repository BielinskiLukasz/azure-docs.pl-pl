---
title: Napisz aplikację sieci Web, która loguje się/out użytkowników — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która loguje użytkowników z/z zewnątrz
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cbec6c5644690f6d7e522294a37c8ea5e2d49e30
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701522"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplikacja internetowa, która loguje użytkowników: Logowanie i wylogowywanie

Dowiedz się, jak dodać logowanie do kodu aplikacji sieci Web, które logują się w użytkownikach. Następnie Dowiedz się, jak umożliwić im wylogowywanie się.

## <a name="sign-in"></a>Logowanie

Logowanie składa się z dwóch części:

- Przycisk logowania na stronie HTML
- Akcja logowania w kodzie w kontrolerze

### <a name="sign-in-button"></a>Przycisk logowania

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk logowania jest widoczny w `Views\Shared\_LoginPartial.cshtml`. Jest on wyświetlany tylko wtedy, gdy nie ma żadnego uwierzytelnionego konta. Oznacza to, że jest wyświetlany, gdy użytkownik jeszcze się nie zalogował lub wylogował się.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk Wyloguj jest widoczny w `Views\Shared\_LoginPartial.cshtml`. Jest on wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik zalogował się wcześniej.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk logowania znajduje się w pliku [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) .

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start dla języka Python nie ma przycisku logowania. Kod zostanie wyświetlony automatycznie Monituj użytkownika o zalogowanie się, gdy dociera do katalogu głównego aplikacji sieci Web. Zobacz [App. PR # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` akcji kontrolera

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET, wybierając przycisk **logowania** w aplikacji sieci Web wyzwala akcję `SignIn` na kontrolerze `AccountController`. W poprzednich wersjach szablonów ASP.NET Core kontroler `Account` został osadzony w aplikacji sieci Web. To nie jest już przypadek, ponieważ kontroler jest teraz częścią ASP.NET Core Framework.

Kod dla `AccountController` jest dostępny w repozytorium ASP.NET Core w [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta wzywa użytkownika przez przekierowanie do punktu końcowego platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje, zobacz metodę [logowania](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) podaną w części ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane przez metodę `SignOut()` na kontrolerze (na przykład [elementu AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ta metoda nie jest częścią ASP.NET Framework (w przeciwieństwie do co dzieje się w ASP.NET Core). Wysyła wyzwanie logowania OpenID Connect po zaproponowaniu identyfikatora URI przekierowania.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie `logout` punktu końcowego platformy tożsamości firmy Microsoft i podanie wartości `post_logout_redirect_uri`. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przeciwieństwie do innych platform, MSAL Python wymaga, aby użytkownik zalogować się na stronie logowania. Zobacz [App. PR # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Metoda `_build_msal_app()` jest zdefiniowana w [App. PR # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) w następujący sposób:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Po zalogowaniu się użytkownika do aplikacji należy włączyć ich wylogowywanie.

## <a name="sign-out"></a>Wylogowywanie

Wylogowywanie z aplikacji sieci Web obejmuje więcej niż usuwanie informacji o koncie zalogowanym ze stanu aplikacji sieci Web.
Aplikacja sieci Web musi również przekierować użytkownika do platformy tożsamości firmy Microsoft `logout` Endpoint, aby się wylogować. 

Gdy aplikacja internetowa przekierowuje użytkownika do punktu końcowego `logout`, ten punkt końcowy czyści sesję użytkownika z przeglądarki. Jeśli Twoja aplikacja nie przejdzie do punktu końcowego `logout`, użytkownik będzie ponownie uwierzytelniać się w aplikacji bez konieczności ponownego wprowadzania poświadczeń. Przyczyną jest to, że będzie ona mieć prawidłową sesję logowania jednokrotnego z punktem końcowym platformy tożsamości firmy Microsoft.

Aby dowiedzieć się więcej, zobacz sekcję [Wysyłanie żądania wylogowania](v2-protocols-oidc.md#send-a-sign-out-request) z [platformy tożsamości firmy Microsoft i dokumentacji protokołu OpenID Connect Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Rejestracja aplikacji

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `https://localhost:44321/signout-oidc` w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `https://localhost:44308/Account/EndSession` w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** . Aby uzyskać szczegółowe informacje, zobacz [Rejestrowanie aplikacji webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Podczas rejestracji aplikacji rejestrujesz identyfikator URI po wylogowaniu. W naszym samouczku zarejestrowano `http://localhost:8080/msal4jsample/sign_out` w polu **adres URL wylogowania** w sekcji **Ustawienia zaawansowane** na stronie **uwierzytelnianie** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Podczas rejestracji aplikacji nie trzeba rejestrować dodatkowego adresu URL wylogowywania. Aplikacja zostanie wywołana z powrotem na swoim głównym adresie URL.

---

### <a name="sign-out-button"></a>Przycisk Wyloguj

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET Core przycisk Wyloguj jest widoczny w `Views\Shared\_LoginPartial.cshtml`. Jest on wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik zalogował się wcześniej.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET MVC przycisk Wyloguj jest widoczny w `Views\Shared\_LoginPartial.cshtml`. Jest on wyświetlany tylko wtedy, gdy istnieje uwierzytelnione konto. Oznacza to, że jest wyświetlany, gdy użytkownik zalogował się wcześniej.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W naszym przewodniku szybki start dla języka Java przycisk Wyloguj znajduje się w pliku main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python przycisk Wyloguj znajduje się w pliku [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` akcji kontrolera

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

W ASP.NET, wybierając przycisk **Wyloguj** w aplikacji sieci Web wyzwala akcję `SignOut` na kontrolerze `AccountController`. W poprzednich wersjach szablonów ASP.NET Core kontroler `Account` został osadzony w aplikacji sieci Web. To nie jest już przypadek, ponieważ kontroler jest teraz częścią ASP.NET Core Framework.

Kod dla `AccountController` jest dostępny w repozytorium ASP.NET Core w [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola konta:

- Ustawia identyfikator URI przekierowania OpenID Connect na `/Account/SignedOut`, aby kontroler został wywołany, gdy usługa Azure AD ukończy wylogowywanie.
- Wywołuje `Signout()`, co umożliwia nawiązywanie połączenia przez oprogramowanie pośredniczące z platformą Microsoft Identity `logout` Endpoint. Następnie punkt końcowy:

  - Czyści plik cookie sesji z przeglądarki.
  - Wywołuje adres URL wylogowania. Domyślnie adres URL wylogowania wyświetla stronę widoku z podpisem [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Ta strona jest również dostępna jako część ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET wylogowanie jest wyzwalane przez metodę `SignOut()` na kontrolerze (na przykład [elementu AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ta metoda nie jest częścią ASP.NET Framework, w przeciwieństwie do działania w ASP.NET Core. Go

- Wysyła wyzwanie wylogowania OpenID Connect.
- Czyści pamięć podręczną.
- Przekierowuje do strony, którą chce.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W języku Java wylogowanie jest obsługiwane przez wywołanie `logout` punktu końcowego platformy tożsamości firmy Microsoft i podanie wartości `post_logout_redirect_uri`. Aby uzyskać szczegółowe informacje, zobacz [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Kod, który wyloguje użytkownika, znajduje się w [aplikacji App. PR # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Przechwytywanie wywołania punktu końcowego `logout`

Identyfikator URI po wylogowaniu umożliwia aplikacjom uczestnictwo w globalnym wylogowaniu.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect łączy oprogramowanie pośredniczące umożliwia aplikacji przechwycenie wywołania do platformy tożsamości firmy Microsoft `logout` punktu końcowego przez udostępnienie zdarzenia OpenID Connect Connect o nazwie `OnRedirectToIdentityProviderForSignOut`. Aby zapoznać się z przykładem sposobu subskrybowania tego zdarzenia (w celu wyczyszczenia pamięci podręcznej tokenów), zobacz [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

W ASP.NET można delegować do oprogramowania pośredniczącego w celu przeprowadzenia wylogowania, czyszcząc plik cookie sesji:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

W przewodniku szybki start w języku Java identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index. html.

# <a name="pythontabpython"></a>[Python](#tab/python)

W przewodniku szybki start w języku Python identyfikator URI przekierowania po wylogowaniu po prostu wyświetla stronę index. html.

---

## <a name="protocol"></a>Protocol (Protokół)

Jeśli chcesz dowiedzieć się więcej o wylogowaniu, Przeczytaj dokumentację dotyczącą protokołu dostępną w programie [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-web-app-sign-user-production.md)
