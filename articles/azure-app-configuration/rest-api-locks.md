---
title: Interfejs API REST usługi Azure App Configuration — blokady
description: Strony referencyjne umożliwiające pracę z blokadami klucz-wartość przy użyciu interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932527"
---
# <a name="locks"></a>Blokady

Ten interfejs API (w wersji 1,0) zapewnia semantykę blokady i odblokowywania dla zasobu klucz-wartość. Obsługuje następujące operacje:

- Umieść blokadę
- Usuń blokadę

Jeśli jest obecny, `label` musi być jawną wartością etykiety (nie symbolem wieloznacznym). Dla wszystkich operacji jest to opcjonalny parametr. W przypadku pominięcia oznacza brak etykiety.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Zablokuj klucz-wartość

- Wymagane: ``{key}`` , ``{api-version}``  
- Obowiązkowe ``label``

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

## <a name="unlock-key-value"></a>Odblokuj klucz-wartość

- Wymagane: ``{key}`` , ``{api-version}``  
- Obowiązkowe ``label``

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

## <a name="conditional-lock-and-unlock"></a>Warunkowe blokowanie i odblokowywanie

Aby zapobiec występowaniu warunków wyścigu, użyj `If-Match` lub `If-None-Match` Zażądaj nagłówków. `etag`Argument jest częścią reprezentacji klucza. Jeśli `If-Match` lub `If-None-Match` zostaną pominięte, operacja jest bezwarunkowa.

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
