---
title: Interfejs API REST usługi Azure App Configuration — przechowywanie wersji
description: Strony referencyjne do przechowywania wersji przy użyciu interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932425"
---
# <a name="versioning"></a>Przechowywanie wersji

Każde żądanie klienta musi udostępniać jawną wersję interfejsu API jako parametr ciągu zapytania. Na przykład: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` jest wyrażony w formacie SemVer (główny. pomocniczy). Negocjowanie zakresu lub wersji nie jest obsługiwane.

Ten artykuł ma zastosowanie do interfejsu API w wersji 1,0.

Poniżej przedstawiono podsumowanie możliwych błędów odpowiedzi zwracanych przez serwer, gdy nie można dopasować żądanej wersji interfejsu API.

## <a name="api-version-unspecified"></a>Nieokreślona wersja interfejsu API

Ten błąd występuje, gdy klient wysyła żądanie bez podawania wersji interfejsu API.

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

## <a name="unsupported-api-version"></a>Nieobsługiwana wersja interfejsu API

Ten błąd występuje, gdy klient zażądał wersji interfejsu API nie jest zgodny z żadną z obsługiwanych wersji interfejsu API przez serwer.

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

## <a name="invalid-api-version"></a>Nieprawidłowa wersja interfejsu API

Ten błąd występuje, gdy klient wysyła żądanie z wersją interfejsu API, ale wartość jest źle sformułowana lub nie można jej przeanalizować przez serwer.

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

## <a name="ambiguous-api-version"></a>Niejednoznaczna wersja interfejsu API

Ten błąd występuje, gdy klient żąda wersji interfejsu API, która jest niejednoznaczna dla serwera (na przykład wiele różnych wartości).

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
