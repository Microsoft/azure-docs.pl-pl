---
title: Tworzenie jednostronicowej aplikacji wywołującej internetowy interfejs API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową, która wywołuje interfejs API sieci Web
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 3b71f66dfcbd33cdecc1a6fea46871ba0cda687d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442892"
---
# <a name="single-page-application-call-a-web-api"></a>Aplikacja jednostronicowa: wywoływanie interfejsu API sieci Web

Zalecamy wywołanie `acquireTokenSilent` metody w celu uzyskania lub odnowienia tokenu dostępu przed wywołaniem internetowego interfejsu API. Po uzyskaniu tokenu można wywołać chroniony internetowy interfejs API.

## <a name="call-a-web-api"></a>Wywoływanie interfejsu API sieci Web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Użyj tokenu uzyskanego dostępu jako okaziciela w żądaniu HTTP w celu wywołania dowolnego internetowego interfejsu API, takiego jak Microsoft Graph API. Na przykład:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL wykorzystuje Interceptor HTTP, aby automatycznie uzyskiwać tokeny dostępu w trybie dyskretnym i dołączać je do żądań HTTP do interfejsów API. Aby uzyskać więcej informacji, zobacz [pozyskiwanie tokenu do wywoływania interfejsu API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Przejdź do środowiska produkcyjnego](scenario-spa-production.md).