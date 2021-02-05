---
title: Wywoływanie interfejsu API sieci Web z poziomu aplikacji demona | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejs API sieci Web.
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
ms.openlocfilehash: bd0d53049c68843a6fd2cb6128c473d7c4f8d639
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582795"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — wywołuje interfejs API sieci Web z aplikacji

Aplikacje demona .NET mogą wywoływać interfejs API sieci Web. Aplikacje demona .NET mogą również wywołać kilka wstępnie zatwierdzonych interfejsów API sieci Web.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Wywoływanie interfejsu API sieci Web z aplikacji demona

Poniżej przedstawiono sposób użycia tokenu do wywołania interfejsu API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

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

---

## <a name="calling-several-apis"></a>Wywoływanie kilku interfejsów API

W przypadku aplikacji demonów interfejsy API sieci Web, które są wywoływane, muszą być wstępnie zatwierdzone. Nie istnieje przyrostowa zgoda z aplikacjami demona. (Nie ma interakcji ze strony użytkownika). Administrator dzierżawy musi wyrazić zgodę na zaawansowaną aplikację i wszystkie uprawnienia interfejsu API. Jeśli chcesz wywołać kilka interfejsów API, uzyskaj token dla każdego zasobu, przy każdym wywołaniu `AcquireTokenForClient` . MSAL użyje pamięci podręcznej tokenów aplikacji, aby uniknąć niepotrzebnych wywołań usługi.

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](./scenario-daemon-production.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](./scenario-daemon-production.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](./scenario-daemon-production.md?tabs=java).

---
