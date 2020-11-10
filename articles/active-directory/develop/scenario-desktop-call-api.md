---
title: Wywołaj interfejsy API sieci Web z aplikacji klasycznej — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web
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
ms.openlocfilehash: 274dc731b69686a67a9ac5dba25a5d9c98581652
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444048"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web: wywoływanie interfejsu API sieci Web

Teraz, gdy masz token, możesz wywołać chroniony internetowy interfejs API.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[MacOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Wywoływanie interfejsu API sieci Web w programie MSAL dla systemów iOS i macOS

Metody uzyskiwania tokenów zwracają `MSALResult` obiekt. `MSALResult` uwidacznia `accessToken` Właściwość, która może służyć do wywoływania internetowego interfejsu API. Dodaj token dostępu do nagłówka autoryzacji HTTP przed wywołaniem dostępu do chronionego internetowego interfejsu API.

Cel-C:

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

Adres

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API: przyrostowej zgody i dostępu warunkowego

Aby wywołać kilka interfejsów API dla tego samego użytkownika, po uzyskaniu tokenu dla pierwszego interfejsu API Wywołaj polecenie `AcquireTokenSilent` . Otrzymasz token dla innych interfejsów API w sposób cichy przez większość czasu.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakcja jest wymagana w przypadku:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi powyrazić zgody na więcej zakresów. Ten rodzaj zgody jest znany jako poprzednia zgoda.
- Pierwszy interfejs API nie wymagał uwierzytelniania wieloskładnikowego, ale Następna z nich robi.

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
---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-desktop-production.md).
