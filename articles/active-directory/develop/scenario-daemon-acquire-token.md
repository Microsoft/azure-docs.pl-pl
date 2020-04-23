---
title: Pozyskiwanie tokenów w celu wywołania internetowego interfejsu API (aplikacji demona) — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje internetowe interfejsy API (pobieranie tokenów)
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
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868994"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikacja Demon, która wywołuje internetowe interfejsy API — uzyskaj token

Po skonstruowaniu poufnej aplikacji klienckiej można uzyskać token dla `AcquireTokenForClient`aplikacji, wywołując, przekazując zakres i opcjonalnie wymuszając odświeżenie tokenu.

## <a name="scopes-to-request"></a>Zakresy, o które należy poprosić

Zakres żądania przepływu poświadczeń klienta jest nazwą `/.default`zasobu, po którym następuje . Ta notacja informuje usługę Azure Active Directory (Azure AD) o użyciu *uprawnień na poziomie aplikacji zadeklarowanych* statycznie podczas rejestracji aplikacji. Ponadto te uprawnienia interfejsu API muszą być przyznane przez administratora dzierżawy.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

W programie MSAL Python plik konfiguracyjny wygląda jak ten fragment kodu:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Zasoby usługi Azure AD (w wersji 1.0)

Zakres używany dla poświadczeń klienta powinien `/.default`być zawsze identyfikatorem zasobu, po którym następuje .

> [!IMPORTANT]
> Gdy msal żąda token dostępu dla zasobu, który akceptuje token dostępu w wersji 1.0, usługa Azure AD analizuje żądaną publiczność z żądanego zakresu, biorąc wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu.
> Jeśli więc, na przykład usługa Azure SQL Database (**https:\//database.windows.net**), zasób oczekuje `https://database.windows.net/`od odbiorców, którzy kończą się `https://database.windows.net//.default`ukośnikiem (dla usługi Azure SQL Database), musisz zażądać zakresu . (Zwróć uwagę na podwójne ukośnik). Zobacz też MSAL.NET problem [#747: Adres URL zasobu jest pomijany, co spowodowało niepowodzenie sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Aby uzyskać token dla aplikacji, użyjesz `AcquireTokenForClient` lub jej odpowiednika, w zależności od platformy.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Ten kod jest wyodrębniany z [msal java dev próbek](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocol (Protokół)

Jeśli nie masz jeszcze biblioteki dla wybranego języka, możesz użyć tego protokołu bezpośrednio:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Pierwszy przypadek: dostęp do żądania tokenu przy użyciu udostępnionego klucza tajnego

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Drugi przypadek: dostęp do żądania tokenu przy użyciu certyfikatu

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Aby uzyskać więcej informacji, zobacz dokumentację protokołu: [Platforma tożsamości firmy Microsoft i przepływ poświadczeń klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Pamięć podręczna tokenów aplikacji

W MSAL.NET `AcquireTokenForClient` używa pamięci podręcznej tokenu aplikacji. (Wszystkie inne metody AcquireToken*XX* używają pamięci podręcznej tokenu użytkownika). Nie `AcquireTokenSilent` wywoż `AcquireTokenForClient`przed `AcquireTokenSilent` wywołaniem, ponieważ używa pamięci podręcznej tokenu *użytkownika.* `AcquireTokenForClient`sprawdza samą pamięć podręczną tokenu *aplikacji* i aktualizuje ją.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="did-you-use-the-resourcedefault-scope"></a>Czy użyto zakresu zasobu/.default?

Jeśli zostanie wyświetlony komunikat o błędzie informujący, że użyto nieprawidłowego zakresu, prawdopodobnie nie użyto `resource/.default` zakresu.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Czy zapomniałeś wyrazić zgodę administratora? Aplikacje demonów tego potrzebują!

Jeśli otrzymasz **niewystarczające uprawnienia, aby zakończyć** błąd operacji podczas wywoływania interfejsu API, administrator dzierżawy musi udzielić uprawnień do aplikacji. Zobacz krok 6 zarejestruj aplikację kliencką powyżej.
Zazwyczaj pojawi się błąd, który wygląda jak ten błąd:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja Demon - wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja Demon - wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja Demon - wywoływanie internetowego interfejsu API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
