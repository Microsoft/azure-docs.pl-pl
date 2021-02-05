---
title: Wywoływanie interfejsu API sieci Web z aplikacji mobilnej | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web. (Wywoływanie interfejsu API sieci Web).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584401"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Wywoływanie interfejsu API sieci Web z aplikacji mobilnej

Gdy aplikacja zaloguje się do użytkownika i odbierze tokeny, Biblioteka Microsoft Authentication Library (MSAL) ujawnia informacje o użytkowniku, środowisku użytkownika i wystawionych tokenach. Twoja aplikacja może używać tych wartości do wywoływania internetowego interfejsu API lub wyświetlania użytkownikowi komunikatu powitalnego.

W tym artykule najpierw Przyjrzyjmy się wynikowi MSAL. Następnie dowiesz się, jak używać tokenu dostępu z programu `AuthenticationResult` lub `result` do wywoływania chronionego internetowego interfejsu API.

## <a name="msal-result"></a>Wynik MSAL
MSAL udostępnia następujące wartości: 

- `AccessToken` wywołuje chronione interfejsy API sieci Web w żądaniu HTTP okaziciela.
- `IdToken` zawiera użyteczne informacje dotyczące zalogowanego użytkownika. Te informacje obejmują nazwę użytkownika, dzierżawę domową i unikatowy identyfikator magazynu.
- `ExpiresOn` to czas wygaśnięcia tokenu. MSAL obsługuje automatyczne odświeżanie aplikacji.
- `TenantId` jest identyfikatorem dzierżawy, w której zalogowany jest użytkownik. Dla użytkowników-Gości w usłudze Azure Active Directory (Azure AD) B2B ta wartość identyfikuje dzierżawcę, w którym użytkownik zalogował się. Wartość nie identyfikuje dzierżawy głównej użytkownika.  
- `Scopes` wskazuje zakresy, które zostały przyznane z tokenem. Przyznane zakresy mogą być podzbiorem żądanych zakresów.

MSAL udostępnia również streszczenie dla `Account` wartości. `Account`Wartość reprezentuje konto zalogowanego bieżącego użytkownika:

- `HomeAccountIdentifier` Identyfikuje dzierżawcę w domu użytkownika.
- `UserName` jest preferowaną nazwą użytkownika. Ta wartość może być pusta dla Azure AD B2C użytkowników.
- `AccountIdentifier` Identyfikuje zalogowanego użytkownika. W większości przypadków ta wartość jest taka sama jak wartość, `HomeAccountIdentifier` chyba że użytkownik jest gościem w innej dzierżawie.

## <a name="call-an-api"></a>Wywoływanie interfejsu API

Po uzyskaniu tokenu dostępu można wywołać internetowy interfejs API. Aplikacja użyje tokenu do skompilowania żądania HTTP, a następnie uruchomienia żądania.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>Biblioteka MSAL dla systemów iOS i macOS

Metody uzyskiwania tokenów zwracają `MSALResult` obiekt. `MSALResult` uwidacznia `accessToken` Właściwość. Możesz użyć, `accessToken` Aby wywołać internetowy interfejs API. Dodaj tę właściwość do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego internetowego interfejsu API.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Wykonaj kilka żądań interfejsu API

Aby wywołać ten sam interfejs API kilka razy lub wywołać wiele interfejsów API, podczas kompilowania aplikacji należy wziąć pod uwagę następujące zagadnienia:

- **Przyrostowa zgoda**: platforma tożsamości firmy Microsoft umożliwia aplikacjom uzyskanie zgody użytkownika, gdy wymagane są uprawnienia, a nie wszystkie na początku. Za każdym razem, gdy aplikacja jest gotowa do wywołania interfejsu API, powinna zażądać tylko wymaganych zakresów.

- **Dostęp warunkowy**: po wprowadzeniu kilku żądań interfejsu API w niektórych scenariuszach może być konieczne spełnienie dodatkowych wymagań dostępu warunkowego. Wymagania mogą wzrosnąć w ten sposób, jeśli pierwsze żądanie nie ma zasad dostępu warunkowego, a aplikacja próbuje uzyskać dostęp do nowego interfejsu API, który wymaga dostępu warunkowego. Aby obsłużyć ten problem, należy zwrócić uwagę na błędy żądań dyskretnych i przygotować się do przeprowadzenia interakcyjnego żądania.  Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące dostępu warunkowego](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API przy użyciu funkcji przyrostowej zgody i dostępu warunkowego

Aby wywoływać kilka interfejsów API dla tego samego użytkownika, po uzyskaniu tokenu dla użytkownika można uniknąć wielokrotnego monitowania użytkownika o poświadczenia, wywołując `AcquireTokenSilent` do pobrania token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakcja jest wymagana w przypadku:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi powyrazić zgody na więcej zakresów. W takim przypadku używana jest wyrażanie zgody.
- Pierwszy interfejs API nie wymaga [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md), ale następny interfejs API wykonuje.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-mobile-production.md).
