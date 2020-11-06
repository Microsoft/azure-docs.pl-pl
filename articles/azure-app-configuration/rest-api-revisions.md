---
title: Interfejs API REST usługi Azure App Configuration — wersje klucz-wartość
description: Strony referencyjne do pracy z poprawkami klucz-wartość za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7d1990d6bc524a69de2b22b4f7c5aeec88c3ce9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424275"
---
# <a name="key-value-revisions"></a>Wersje klucz-wartość

wersja interfejsu API: 1,0

**Poprawka klucz-wartość** definiuje historyczną reprezentację zasobu klucz-wartość. Poprawki wygasają po upływie 7 dni dla magazynów w warstwie Bezpłatna lub przez 30 dni dla magazynów w warstwie Standardowa. Poprawki obsługują następujące operacje:

- Lista

Dla wszystkich operacji, ``key`` jest opcjonalnym parametrem. W przypadku pominięcia oznacza **dowolny** klucz.
Dla wszystkich operacji, ``label`` jest opcjonalnym parametrem. W przypadku pominięcia oznacza **każdą** etykietę.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Listy poprawek

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Dzielenie na strony

Wynik jest podzielony na strony, jeśli liczba zwracanych elementów przekracza limit odpowiedzi. Postępuj zgodnie z opcjonalnym ``Link`` nagłówkiem odpowiedzi i Użyj ``rel="next"`` na potrzeby nawigacji.  Alternatywnie zawartość zawiera następny link w postaci ``@nextLink`` właściwości.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Reakcji**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Lista podzestawów poprawek

Użyj `Range` nagłówka żądania. Odpowiedź będzie zawierać `Content-Range` nagłówek. Jeśli serwer nie może spełnić żądanego zakresu, będzie odpowiadać przy użyciu protokołu HTTP `416` (RangeNotSatisfiable)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Odpowiedź**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtrowanie

`key`Obsługiwane są kombinacje i `label` filtrowanie.
Użyj parametrów opcjonalnych `key` i `label` ciągu zapytania.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Obsługiwane filtry

|Filtr klucza|Efekt|
|--|--|
|Element `key` jest pomijany lub `key=*`|Dopasowuje **dowolny** klucz|
|`key=abc`|Dopasowuje klucz o nazwie  **ABC**|
|`key=abc*`|Dopasowuje nazwy kluczy, które zaczynają się od **ABC**|
|`key=*abc`|Dopasowuje nazwy kluczy kończące się na **ABC**|
|`key=*abc*`|Dopasowuje nazwy kluczy, które zawierają **ABC**|
|`key=abc,xyz`|Dopasowuje nazwy kluczy **ABC** lub **XYZ** (ograniczone do 5 woluminów CSV)|

|Filtr etykiet|Efekt|
|--|--|
|Element `label` jest pomijany lub `label=`|Dopasowuje wpis bez etykiety|
|`label=*`|Dopasowuje **dowolną** etykietę|
|`label=prod`|Dopasowuje etykietę **prod**|
|`label=prod*`|Dopasowuje etykiety rozpoczynające się od **prod**|
|`label=*prod`|Dopasowuje etykiety kończące się na **produkcyjnym**|
|`label=*prod*`|Dopasowuje etykiety zawierające **produkcyjny**|
|`label=prod,test`|Dopasowuje etykiety **prod** lub **test** (ograniczone do 5 woluminów CSV)|

### <a name="reserved-characters"></a>Znaki zastrzeżone

`*`, `\`, `,`

Jeśli zarezerwowany znak jest częścią wartości, należy użyć klawisza ucieczki `\{Reserved Character}` . Znaki niezastrzeżone mogą również zostać zmienione.

### <a name="filter-validation"></a>Sprawdzanie poprawności filtru

Jeśli wystąpi błąd walidacji filtru, odpowiedź jest HTTP `400` z szczegółowymi informacjami o błędzie:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Przykłady

- Wszystko

    ```http
    GET /revisions
    ```

- Elementy, w których nazwa klucza rozpoczyna się od **ABC**

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Elementy, w których nazwa klucza to **ABC** lub **XYZ** , a etykiety zawierają kod **produkcyjny**

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Żądaj pól określonych

Użyj opcjonalnego `$select` parametru ciągu zapytania i podaj rozdzieloną przecinkami listę wymaganych pól. Jeśli `$select` parametr zostanie pominięty, odpowiedź zawiera zestaw domyślny.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Dostęp Time-Based

Uzyskaj reprezentację wyniku tak jak wcześniej. Zobacz sekcję [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
