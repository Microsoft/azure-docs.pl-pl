---
title: Interfejs API REST usługi Azure App Configuration — blokady
description: Strony referencyjne umożliwiające pracę z blokadami klucz-wartość za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424311"
---
# <a name="locks"></a>Blokady

wersja interfejsu API: 1,0

Ten interfejs API zapewnia semantykę blokady/odblokowywania dla zasobu klucz-wartość. Obsługuje następujące operacje:

- Umieść blokadę
- Usuń blokadę

Jeśli jest obecny, `label` musi być jawną wartością etykiety ( **nie** symbolem wieloznacznym). Dla wszystkich operacji jest to opcjonalny parametr. W przypadku pominięcia oznacza brak etykiety.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Zablokuj Key-Value

- **Wymagane:** ``{key}`` , ``{api-version}``  
- *Opcjonalne:*``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Jeśli klucz-wartość nie istnieje, zwracana jest następująca odpowiedź:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Odblokuj Key-Value

- **Wymagane:** ``{key}`` , ``{api-version}``  
- *Opcjonalne:*``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Jeśli klucz-wartość nie istnieje, zwracana jest następująca odpowiedź:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Warunkowe blokowanie/odblokowywanie

Aby zapobiec występowaniu warunków wyścigu, użyj `If-Match` lub `If-None-Match` Zażądaj nagłówków. `etag`Argument jest częścią reprezentacji klucza. Jeśli `If-Match` lub `If-None-Match` zostaną pominięte, operacja będzie bezwarunkowo.

Poniższe żądanie stosuje operację tylko wtedy, gdy bieżąca reprezentacja klucza jest zgodna z określoną wartością `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Poniższe żądanie stosuje tę operację tylko wtedy, gdy istnieje bieżąca reprezentacja klucz-wartość, ale nie jest zgodna z określoną wartością `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
