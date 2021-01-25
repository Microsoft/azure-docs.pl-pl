---
title: Uzyskaj token dla internetowego interfejsu API, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web, które wymagają uzyskiwania tokenu dla aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756392"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji

Po skompilowaniu obiektu aplikacji klienckiej Użyj go, aby uzyskać token, którego można użyć do wywołania interfejsu API sieci Web.

## <a name="code-in-the-controller"></a>Kod w kontrolerze

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* dodaje metody rozszerzające, które zapewniają wygodne usługi do wywoływania Microsoft Graph lub podrzędnego interfejsu API sieci Web. Te metody są szczegółowo opisane w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md). Przy użyciu tych metod pomocniczych nie trzeba ręcznie uzyskać tokenu.

Jeśli jednak chcesz ręcznie uzyskać token, poniższy kod przedstawia przykład użycia *Microsoft. Identity. Web* , aby to zrobić w kontrolerze interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.
Aby uzyskać token wywołujący podrzędny interfejs API, należy wstrzyknąć `ITokenAcquisition` usługę przez wstrzyknięcie do niego w konstruktorze (lub w konstruktorze strony, jeśli używasz Blazor), i używać jej w akcjach kontrolera, pobierając token dla użytkownika ( `GetAccessTokenForUserAsync` ) lub aplikacji ( `GetAccessTokenForAppAsync` ) w przypadku scenariusza demona.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Aby uzyskać szczegółowe informacje na temat `callTodoListService` metody, zobacz  [internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md).

### <a name="java"></a>[Java](#tab/java)

Oto przykład kodu, który jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API-Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Interfejs API sieci Web w języku Python wymaga użycia oprogramowania pośredniczącego do weryfikowania tokenu okaziciela otrzymanego od klienta. Interfejs API sieci Web może następnie uzyskać token dostępu dla podrzędnego interfejsu API przy użyciu biblioteki języka Python MSAL przez wywołanie [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metody.
 
Oto przykład kodu, który uzyskuje token dostępu przy użyciu `acquire_token_on_behalf_of` metody i struktury kolb. Wywołuje podrzędny interfejs API — punkt końcowy subskrypcji zarządzania platformy Azure.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Doświadczonych Uzyskiwanie dostępu do pamięci podręcznej zalogowanych użytkowników z aplikacji w tle, interfejsów API i usług

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Wywołaj interfejs API](scenario-web-api-call-api-call-api.md).
