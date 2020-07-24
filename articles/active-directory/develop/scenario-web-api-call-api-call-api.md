---
title: Interfejs API sieci Web, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b756d7df03bd3c06b703617dbf84a194d716f1e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026377"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API

Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API. Można to zrobić na kontrolerze interfejsu API sieci Web.

## <a name="controller-code"></a>Kod kontrolera

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*.

Po uzyskaniu tokenu Użyj go jako tokenu okaziciela, aby wywołać podrzędny interfejs API.

```csharp
private async Task CallTodoListService(string accessToken)
{

// After the token has been returned by Microsoft Identity Web, add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

Poniższy kod kontynuuje przykładowy kod, który jest wyświetlany w [interfejsie API sieci Web, który wywołuje interfejsy API sieci Web: uzyskuje token dla aplikacji](scenario-web-api-call-api-acquire-token.md). Kod jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API programu MS Graph.

Po uzyskaniu tokenu Użyj go jako tokenu okaziciela, aby wywołać podrzędny interfejs API.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Przykład pokazujący ten przepływ przy użyciu języka MSAL Python nie jest jeszcze dostępny.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Przenieś do środowiska produkcyjnego](scenario-web-api-call-api-production.md)
