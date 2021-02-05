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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1ab7da9753642093505fa91ea76b9327612a6ac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582370"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Konfiguracja kodu

Po zarejestrowaniu internetowego interfejsu API można skonfigurować kod dla aplikacji.

Kod używany do konfigurowania internetowego interfejsu API, dzięki czemu wywołuje podrzędne interfejsy API sieci Web na podstawie kodu, który jest używany do ochrony internetowego interfejsu API. Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API: Konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Firma Microsoft zaleca, aby używać pakietu NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) podczas tworzenia ASP.NET Core CHRONIONEGO interfejsu API, wywołującego podrzędne internetowego API. Zobacz [chroniony internetowy interfejs API: Konfiguracja kodu | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) — szybka Prezentacja tej biblioteki w kontekście internetowego interfejsu API.

## <a name="client-secrets-or-client-certificates"></a>Klucze tajne klienta lub certyfikaty klienta

Ponieważ interfejs API sieci Web wywołuje teraz podrzędny interfejs API sieci Web, Podaj klucz tajny klienta lub certyfikat klienta w *appsettings.js* pliku. Możesz również dodać sekcję, która określa:

- Adres URL podrzędnego interfejsu API sieci Web
- Zakresy wymagane do wywoływania interfejsu API

W poniższym przykładzie `GraphBeta` sekcja określa te ustawienia.

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
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
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
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity. Web oferuje kilka sposobów opisywania certyfikatów, zarówno według konfiguracji, jak i kodu. Aby uzyskać szczegółowe informacje, zobacz [Microsoft. Identity. Web wiki — używanie certyfikatów](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) w serwisie GitHub.

## <a name="startupcs"></a>Startup.cs

Interfejs API sieci Web będzie musiał uzyskać token dla podrzędnego interfejsu API. Należy ją określić, dodając `.EnableTokenAcquisitionToCallDownstreamApi()` wiersz po `.AddMicrosoftIdentityWebApi(Configuration)` . Ten wiersz `ITokenAcquisition` przedstawia usługę, która może być używana w akcjach kontrolera/stron. Jednak jak widać w następnych dwóch punktach punktorów, można to jeszcze prostsze. Należy również wybrać implementację pamięci podręcznej tokenów, na przykład `.AddInMemoryTokenCaches()` w *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Jeśli nie chcesz samodzielnie uzyskać tokenu, *firma Microsoft. Identity. Web* udostępnia dwa mechanizmy wywoływania podrzędnego interfejsu API sieci Web z innego interfejsu API. Wybrana opcja zależy od tego, czy chcesz wywołać Microsoft Graph lub inny interfejs API.

### <a name="option-1-call-microsoft-graph"></a>Opcja 1: Wywołaj Microsoft Graph

Jeśli chcesz wywołać Microsoft Graph, Microsoft. Identity. Web umożliwia bezpośrednio korzystanie z programu `GraphServiceClient` (dostępnego w Microsoft Graph SDK) w akcjach interfejsu API. Aby uwidocznić Microsoft Graph:

1. Dodaj pakiet NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) do projektu.
1. Dodaj `.AddMicrosoftGraph()` po `.EnableTokenAcquisitionToCallDownstreamApi()` pliku *Startup.cs* . `.AddMicrosoftGraph()` ma kilka zastąpień. Użycie przesłonięcia, które przyjmuje sekcję konfiguracyjną jako parametr, staje się następujące:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opcja 2: wywoływanie podrzędnego interfejsu API sieci Web innego niż Microsoft Graph

Aby wywołać interfejs API podrzędny inny niż Microsoft Graph, *Microsoft. Identity. Web* udostępnia `.AddDownstreamWebApi()` , który żąda tokenów i wywołuje podrzędny interfejs API sieci Web.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Podobnie jak w przypadku aplikacji sieci Web, można wybrać różne implementacje pamięci podręcznej tokenów. Aby uzyskać szczegółowe informacje, zobacz [serializacji pamięci podręcznej Microsoft Identity Web-token](https://aka.ms/ms-id-web/token-cache-serialization) w witrynie GitHub.

Na poniższej ilustracji przedstawiono różne możliwości *Microsoft. Identity. Web* i ich wpływ na plik *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagram blokowy przedstawiający opcje konfiguracji usługi w kropce startowej C S na potrzeby wywoływania internetowego interfejsu API i określania implementacji pamięci podręcznej token":::

> [!NOTE]
> Aby w pełni zrozumieć przykłady kodu, należy zapoznać się z tematami [ASP.NET Core Fundamentals](/aspnet/core/fundamentals), w szczególności z [iniekcją](/aspnet/core/fundamentals/dependency-injection) i [opcjami](/aspnet/core/fundamentals/configuration/options)zależności.

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

Można także zobaczyć przykład implementacji przepływu OBO w [Node.js i Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokół

Aby uzyskać więcej informacji na temat protokołu OBO, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji](scenario-web-api-call-api-acquire-token.md).
