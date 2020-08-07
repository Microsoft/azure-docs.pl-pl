---
title: Konfigurowanie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web (Konfiguracja kodu aplikacji)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 95cb1181f841ce5f958b8a85697d7261f442b410
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799603"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Konfiguracja kodu

Po zarejestrowaniu internetowego interfejsu API można skonfigurować kod dla aplikacji.

Kod używany do konfigurowania internetowego interfejsu API, dzięki czemu wywołuje podrzędne interfejsy API sieci Web na podstawie kodu, który jest używany do ochrony internetowego interfejsu API. Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API: Konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klucze tajne klienta lub certyfikaty klienta

Ponieważ interfejs API sieci Web wywołuje teraz podrzędny interfejs API sieci Web, należy podać klucz tajny klienta lub certyfikat klienta w *appsettings.js* pliku.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

Zamiast klucza tajnego klienta można podać certyfikat klienta. Poniższy fragment kodu przedstawia użycie certyfikatu przechowywanego w Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft. Identity. Web oferuje kilka sposobów opisywania certyfikatów, zarówno według konfiguracji, jak i kodu. Aby uzyskać szczegółowe informacje, zobacz [Microsoft. Identity. Web wiki — używanie certyfikatów](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) w serwisie GitHub.

## <a name="startupcs"></a>Startup.cs

Jeśli interfejs API sieci Web ma wywoływać podrzędne interfejsy API sieci Web przy użyciu usługi Microsoft. Identity. Web, Dodaj `.AddMicrosoftWebApiCallsWebApi()` wiersz po `.AddMicrosoftWebApiAuthentication(Configuration)` , a następnie wybierz implementację pamięci podręcznej tokenów, na przykład `.AddInMemoryTokenCaches()` w *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi(Configuration)
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Podobnie jak w przypadku aplikacji sieci Web, można wybrać różne implementacje pamięci podręcznej tokenów. Aby uzyskać szczegółowe informacje, zobacz [Microsoft Identity Web wiki — Serializacja pamięci podręcznej](https://aka.ms/ms-id-web/token-cache-serialization) w witrynie GitHub.

Jeśli masz pewność, że internetowy interfejs API będzie potrzebować określonych zakresów, możesz opcjonalnie przekazać je jako argumenty do `AddMicrosoftWebApiCallsWebApi` .

# <a name="java"></a>[Java](#tab/java)

Przepływ "w imieniu" (OBO) służy do uzyskania tokenu wywołującego podrzędny interfejs API sieci Web. W tym przepływie internetowy interfejs API odbiera token okaziciela z uprawnieniami delegowanymi przez użytkownika z aplikacji klienckiej, a następnie wymienia ten token dla innego tokenu dostępu, aby wywołać podrzędny interfejs API sieci Web.

Poniższy kod używa struktury zabezpieczeń wiosennej `SecurityContextHolder` w interfejsie API sieci Web w celu uzyskania zweryfikowanego tokenu okaziciela. Następnie używa biblioteki języka Java MSAL, aby uzyskać token dla interfejsu API podrzędnego przy użyciu `acquireToken` wywołania z `OnBehalfOfParameters` . MSAL buforuje token tak, aby kolejne wywołania interfejsu API mogły użyć `acquireTokenSilently` do uzyskania zbuforowanego tokenu.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Przepływ "w imieniu" (OBO) służy do uzyskania tokenu wywołującego podrzędny interfejs API sieci Web. W tym przepływie internetowy interfejs API odbiera token okaziciela z uprawnieniami delegowanymi przez użytkownika z aplikacji klienckiej, a następnie wymienia ten token dla innego tokenu dostępu, aby wywołać podrzędny interfejs API sieci Web.

Interfejs API sieci Web w języku Python będzie musiał użyć oprogramowania pośredniczącego w celu zweryfikowania tokenu okaziciela otrzymanego od klienta. Interfejs API sieci Web może następnie uzyskać token dostępu dla podrzędnego interfejsu API przy użyciu biblioteki języka Python MSAL, wywołując [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metodę. Aby zapoznać się z przykładem użycia tego interfejsu API, zobacz [kod testu dla elementu Microsoft-Authentication-Library-for-Python w serwisie GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Zapoznaj się również z omówieniem [problemu 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) w tym samym repozytorium dla podejścia, które pomija potrzebę zastosowania aplikacji warstwy środkowej.

---

Można także zobaczyć przykład implementacji przepływu OBO w [Node.js i Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokół

Aby uzyskać więcej informacji na temat protokołu OBO, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji](scenario-web-api-call-api-acquire-token.md)
