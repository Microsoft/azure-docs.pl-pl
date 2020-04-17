---
title: Pobierz token w aplikacji sieci web, która wywołuje internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak uzyskać token dla aplikacji sieci web, która wywołuje internetowe interfejsy API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1069b4288f8253ccb9a7774b3144d10d85dcdd36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537121"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Aplikacja internetowa, która wywołuje internetowe interfejsy API: uzyskaj token dla aplikacji

Obiekt aplikacji klienckiej został utworzony. Teraz użyjesz go do uzyskania tokenu do wywołania internetowego interfejsu API. W ASP.NET lub ASP.NET Core wywoływanie internetowego interfejsu API odbywa się w kontrolerze:

- Pobierz token dla internetowego interfejsu API przy użyciu pamięci podręcznej tokenu. Aby uzyskać ten token, `AcquireTokenSilent` należy wywołać metodę.
- Wywołanie chronionego interfejsu API, przekazując token dostępu do niego jako parametr.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontrolera są chronione `[Authorize]` przez atrybut, który wymusza uwierzytelnianie użytkowników do korzystania z aplikacji sieci web. Oto kod, który wywołuje Microsoft Graph:

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

Usługa `ITokenAcquisition` jest wstrzykiwany przez ASP.NET za pomocą iniekcji zależności.

Oto uproszczony kod dla akcji `HomeController`, który pobiera token do wywołania Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Aby lepiej zrozumieć kod wymagany w tym scenariuszu, zobacz fazę 2[(2-1-Aplikacja sieci Web wywołuje microsoft graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) krok [ms-identity-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Istnieją inne złożone odmiany, takie jak:

- Wywoływanie kilku interfejsów API.
- Przetwarzanie przyrostowej zgody i dostępu warunkowego.

Te zaawansowane kroki są omówione w rozdziale 3 [samouczka 3-WebApp-multi-API.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kod dla ASP.NET jest podobny do kodu wyświetlanego dla ASP.NET Core:

- Akcja kontrolera, chroniona atrybutem [Authorize], wyodrębnia identyfikator dzierżawy i `ClaimsPrincipal` identyfikator użytkownika członka kontrolera. (ASP.NET używa `HttpContext.User`.)
- Stamtąd tworzy `IConfidentialClientApplication` obiekt MSAL.NET.
- Na koniec wywołuje `AcquireTokenSilent` metodę poufnej aplikacji klienckiej.

# <a name="java"></a>[Java](#tab/java)

W przykładzie Java kod, który wywołuje interfejs API jest w getUsersFromGraph metody w [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda próbuje wywołać `getAuthResultBySilentFlow`. Jeśli użytkownik musi wyrazić zgodę na więcej `MsalInteractionRequiredException` zakresów, kod przetwarza obiekt, aby zakwestionować użytkownika.

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

W przykładzie języka Python kod, który wywołuje Microsoft Graph jest w [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kod próbuje uzyskać token z pamięci podręcznej tokenu. Następnie po ustawieniu nagłówka autoryzacji wywołuje internetowy interfejs API. Jeśli nie może uzyskać tokenu, ponownie podpisuje użytkownika.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-web-app-call-api-call-api.md)
