---
title: Uzyskaj token w aplikacji sieci Web, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak uzyskać token dla aplikacji sieci Web, która wywołuje interfejsy API sieci Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8af9ddd3649e2a5757e47ab2a3d8edca1c83929
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442620"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji

Obiekt aplikacji klienta został skompilowany. Teraz użyjesz go, aby uzyskać token do wywoływania interfejsu API sieci Web. W ASP.NET lub ASP.NET Core wywoływanie interfejsu API sieci Web odbywa się na kontrolerze:

- Uzyskaj token dla interfejsu API sieci Web za pomocą pamięci podręcznej tokenów. Aby uzyskać ten token, należy wywołać metodę MSAL `AcquireTokenSilent` (lub równoważną w Microsoft. Identity. Web).
- Wywołaj chroniony interfejs API, przekazując do niego token dostępu jako parametr.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* dodaje metody rozszerzające, które zapewniają wygodne usługi do wywoływania Microsoft Graph lub podrzędnego interfejsu API sieci Web. Te metody zostały szczegółowo omówione w [aplikacji sieci Web, która wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-app-call-api-call-api.md). Przy użyciu tych metod pomocniczych nie trzeba ręcznie uzyskać tokenu.

Jeśli jednak chcesz ręcznie uzyskać token, poniższy kod przedstawia przykład użycia *Microsoft. Identity. Web* , aby to zrobić na kontrolerze macierzystym. Wywołuje Microsoft Graph przy użyciu interfejsu API REST (zamiast zestawu Microsoft Graph SDK). Aby uzyskać token do wywoływania podrzędnego interfejsu API, należy wstrzyknąć `ITokenAcquisition` usługę przez wstrzyknięcie do niego w konstruktorze (lub w konstruktorze strony, jeśli używasz Blazor), i używać jej w akcjach kontrolera, pobierając token dla użytkownika ( `GetAccessTokenForUserAsync` ) lub aplikacji ( `GetAccessTokenForAppAsync` ) w scenariuszu demona.

Metody kontrolera są chronione przez `[Authorize]` atrybut, który gwarantuje, że tylko uwierzytelnieni użytkownicy mogą korzystać z aplikacji sieci Web.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

`ITokenAcquisition`Usługa jest wstrzykiwana przez ASP.NET przy użyciu iniekcji zależności.

Oto uproszczony kod dla akcji `HomeController` , który pobiera token do wywołania Microsoft Graph:

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Aby lepiej zrozumieć kod wymagany dla tego scenariusza, zobacz krok 2 ([2-1 — wywołania aplikacji sieci Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) w samouczku [MS-Identity-aspnetcore-webapp-samouczka](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

`AuthorizeForScopes`Atrybut na górze akcji kontrolera (lub strony Razor, jeśli używasz szablonu Razor) jest dostarczany przez Microsoft. Identity. Web. Gwarantuje to, że użytkownik zostanie poproszony o zgodę, jeśli jest to konieczne, i przyrostowo.

Istnieją inne złożone odmiany, takie jak:

- Wywoływanie kilku interfejsów API.
- Przetwarzanie przyrostowej zgody i dostępu warunkowego.

Te zaawansowane kroki przedstawiono w rozdziale 3 samouczka [3-webapp-wiele interfejsów API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kod dla ASP.NET jest podobny do kodu pokazanego dla ASP.NET Core:

- Akcja kontrolera, chroniona przez atrybut [autoryzuje], wyodrębnia identyfikator dzierżawy i identyfikator użytkownika `ClaimsPrincipal` elementu członkowskiego kontrolera. (ASP.NET używa `HttpContext.User` .)
- Z tego miejsca kompiluje obiekt MSAL.NET `IConfidentialClientApplication` .
- Na koniec wywołuje `AcquireTokenSilent` metodę poufnej aplikacji klienckiej.
- Jeśli wymagana jest interakcja, aplikacja sieci Web musi zażądać użytkownika (ponowne zalogowanie) i poszukać dalszych oświadczeń.

Poniższy fragment kodu został wyodrębniony z [HomeController. cs # L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) w przykładzie [MS-Identity-ASPNET-webapp-OPENIDCONNECT](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET kod MVC:

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

Aby uzyskać szczegółowe informacje, zobacz kod dla [BuildConfidentialClientApplication ()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) i [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) w przykładowym kodzie


# <a name="java"></a>[Java](#tab/java)

W przykładzie Java kod wywołujący interfejs API jest w metodzie getUsersFromGraph w [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda próbuje wywołać `getAuthResultBySilentFlow` . Jeśli użytkownik musi wyrazić zgodę na więcej zakresów, kod przetwarza `MsalInteractionRequiredException` obiekt, aby zakwestionować użytkownika.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

W przykładzie języka Python kod, który wywołuje Microsoft Graph, znajduje się w [App. PR # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kod próbuje uzyskać token z pamięci podręcznej tokenów. Następnie, po ustawieniu nagłówka autoryzacji, wywoła internetowy interfejs API. Jeśli nie można uzyskać tokenu, podpisuje użytkownika ponownie.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Wywołaj internetowy interfejs API](scenario-web-app-call-api-call-api.md).
