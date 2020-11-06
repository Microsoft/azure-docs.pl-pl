---
title: Interfejs API REST usługi Azure App Configuration — przechowywanie wersji
description: Strony referencyjne do obsługi wersji za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424267"
---
# <a name="versioning"></a>Przechowywanie wersji

wersja interfejsu API: 1,0

Każde żądanie klienta musi udostępniać jawną wersję interfejsu API jako parametr ciągu zapytania. Na przykład: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` jest wyrażony w formacie SemVer (główny. pomocniczy). Negocjowanie zakresu lub wersji nie jest obsługiwane.

## <a name="error-response"></a>Odpowiedź na błąd

Poniżej przedstawiono podsumowanie możliwych błędów odpowiedzi zwracanych przez serwer, gdy nie można dopasować żądanej wersji interfejsu API.

### <a name="api-version-unspecified"></a>Nieokreślona wersja interfejsu API

Występuje, gdy klient wysyła żądanie bez podawania wersji interfejsu API.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

### <a name="unsupported-api-version"></a>Nieobsługiwana wersja interfejsu API

Występuje, gdy żądana wersja interfejsu API klienta nie jest zgodna z żadną z obsługiwanych wersji interfejsu API przez serwer.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="invalid-api-version"></a>Nieprawidłowa wersja interfejsu API

Występuje, gdy klient wysyła żądanie z wersją interfejsu API, ale wartość jest źle sformułowana lub nie można jej przeanalizować przez serwer.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

### <a name="ambiguous-api-version"></a>Niejednoznaczna wersja interfejsu API

Występuje, gdy klient żąda wersji interfejsu API, która jest niejednoznaczna dla serwera. Na przykład wiele różnych wartości.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
