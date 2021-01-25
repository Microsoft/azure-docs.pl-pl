---
title: Uzyskiwanie tokenów do wywoływania internetowego interfejsu API (aplikacja demona) — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web (Uzyskiwanie tokenów)
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
ms.openlocfilehash: 295897be03a7dd8e397e8202ff1cf10e6d59cdfb
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753865"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — pozyskiwanie tokenu

Po zbudowaniu poufnej aplikacji klienckiej można uzyskać token dla aplikacji przez wywołanie `AcquireTokenForClient` , przekazanie zakresu i opcjonalnie wymuszenie odświeżenia tokenu.

## <a name="scopes-to-request"></a>Zakresy do żądania

Zakresem żądania dla przepływu poświadczeń klienta jest nazwa zasobu, po którym następuje `/.default` . Ta notacja informuje Azure Active Directory (Azure AD) o korzystaniu z *uprawnień na poziomie aplikacji* , które są zadeklarowane statycznie podczas rejestrowania aplikacji. Ponadto te uprawnienia interfejsu API muszą zostać przyznane przez administratora dzierżawy.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

W MSAL Python plik konfiguracji wygląda podobnie do tego fragmentu kodu:

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

### <a name="azure-ad-v10-resources"></a>Zasoby usługi Azure AD (v 1.0)

Zakres używany na potrzeby poświadczeń klienta zawsze powinien być IDENTYFIKATORem zasobu, po którym następuje `/.default` .

> [!IMPORTANT]
> Gdy MSAL żąda tokenu dostępu dla zasobu, który akceptuje token dostępu w wersji 1,0, usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu.
> Tak więc jeśli, podobnie jak Azure SQL Database (**https: \/ /Database.Windows.NET**), zasób oczekuje odbiorców kończących się ukośnikiem (dla Azure SQL Database `https://database.windows.net/` ), należy zażądać zakresu `https://database.windows.net//.default` . (Należy pamiętać o podwójnym ukośniku). Zobacz również #747 problemu MSAL.NET [: zostanie pominięty końcowy ukośnik adresu URL zasobu, co spowodowało błąd uwierzytelniania SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>Interfejs API AcquireTokenForClient

Aby uzyskać token dla aplikacji, należy użyć `AcquireTokenForClient` lub jej odpowiednik, w zależności od platformy.

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

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient używa pamięci podręcznej tokenów aplikacji

W programie MSAL.NET `AcquireTokenForClient` używa pamięci podręcznej token aplikacji. (Wszystkie inne metody AcquireToken *XX* używają pamięci podręcznej tokenów użytkowników). Nie wywołuj `AcquireTokenSilent` przed wywołaniem `AcquireTokenForClient` , ponieważ `AcquireTokenSilent` używa pamięci podręcznej tokenów *użytkownika* . `AcquireTokenForClient` sprawdza sam pamięć podręczną tokenu *aplikacji* i aktualizuje go.

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

Ten kod jest wyodrębniany z [przykładów deweloperskich języka Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

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

### <a name="protocol"></a>Protokół

Jeśli nie masz jeszcze biblioteki dla wybranego języka, możesz chcieć użyć protokołu bezpośrednio:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Pierwszy przypadek: dostęp do żądania tokenu przy użyciu wspólnego klucza tajnego

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Drugi przypadek: uzyskiwanie dostępu do żądania tokenu przy użyciu certyfikatu

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

Aby uzyskać więcej informacji, zobacz dokumentację protokołu: [Microsoft Identity platform oraz przepływ poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="did-you-use-the-resourcedefault-scope"></a>Czy używasz tego zakresu zasobu/. default?

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że użyto nieprawidłowego zakresu, prawdopodobnie nie był używany `resource/.default` zakres.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Czy pamiętasz o podaniu zgody administratora? Aplikacje demonów wymagają!

W przypadku uzyskania **niewystarczających uprawnień do ukończenia błędu operacji** podczas wywoływania interfejsu API Administrator dzierżawy musi udzielić uprawnień do aplikacji. Zobacz Krok 6 rejestracji aplikacji klienckiej powyżej.
Zwykle zobaczysz błąd, który wygląda jak ten błąd:

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

### <a name="are-you-calling-your-own-api"></a>Czy wywołujesz własny interfejs API?

Jeśli wywołasz własny internetowy interfejs API i nie udało Ci się dodać uprawnienia aplikacji do rejestracji aplikacji dla aplikacji demona, czy chcesz uwidocznić rolę aplikacji w interfejsie API sieci Web?

Aby uzyskać szczegółowe informacje, zobacz [udostępnianie uprawnień aplikacji (ról aplikacji)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) , a w szczególności [zapewnienie, że usługa Azure AD wystawia tokeny dla internetowego interfejsu API tylko dla dozwolonych klientów](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients).

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

Przejdź do następnego artykułu w tym scenariuszu, [wywołując interfejs API sieci Web](./scenario-daemon-call-api.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Przejdź do następnego artykułu w tym scenariuszu, [wywołując interfejs API sieci Web](./scenario-daemon-call-api.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Przejdź do następnego artykułu w tym scenariuszu, [wywołując interfejs API sieci Web](./scenario-daemon-call-api.md?tabs=java).

---
