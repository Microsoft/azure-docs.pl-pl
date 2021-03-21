---
title: Interfejs API REST usługi Azure App Configuration — klucze
description: Strony referencyjne do pracy z kluczami przy użyciu interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932544"
---
# <a name="keys"></a>Klucze

wersja interfejsu API: 1,0

Następująca składnia reprezentuje zasób klucza:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operacje

Najważniejsze zasoby obsługują następujące operacje:

- Lista

Dla wszystkich operacji `name` jest opcjonalnym parametrem filtru. W przypadku pominięcia oznacza *dowolny* klucz.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Klucze list

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Dzielenie na strony

Wynik jest podzielony na strony, jeśli liczba zwracanych elementów przekracza limit odpowiedzi. Postępuj zgodnie z opcjonalnymi `Link` nagłówkami odpowiedzi i Użyj `rel="next"` na potrzeby nawigacji. Alternatywnie zawartość zawiera następny link w postaci `@nextLink` właściwości. Następny link zawiera `api-version` parametr.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Reakcji**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

## <a name="filtering"></a>Filtrowanie

Filtrowanie według ```name``` jest obsługiwane.

```http
GET /keys?name={key-name}&api-version={api-version}
```

Obsługiwane są następujące filtry:

|Filtr klucza|Efekt|
|--|--|
|Element `name` jest pomijany lub `name=*`|Dopasowuje **dowolny** klucz|
|`name=abc`|Dopasowuje klucz o nazwie  **ABC**|
|`name=abc*`|Pasuje do nazw kluczy, które zaczynają się od ciągu **abc**|
|`name=abc,xyz`|Dopasowuje nazwy kluczy **ABC** lub **XYZ** (ograniczone do 5 woluminów CSV)|

Następujące znaki są zastrzeżone: `*` , `\` , `,`

Jeśli zarezerwowany znak jest częścią wartości, należy użyć klawisza ucieczki `\{Reserved Character}` . Znaki niezastrzeżone mogą również zostać zmienione.

## <a name="filter-validation"></a>Sprawdzanie poprawności filtru

W przypadku błędu walidacji filtru odpowiedź jest HTTP `400` z szczegółowymi informacjami o błędzie:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Przykłady

- Wszystko

    ```http
    GET /keys?api-version={api-version}
    ```

- Nazwa klucza rozpoczyna się od **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- Nazwa klucza jest **ABC** lub **XYZ**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Żądaj pól określonych

Użyj opcjonalnego `$select` parametru ciągu zapytania i podaj rozdzieloną przecinkami listę wymaganych pól. Jeśli `$select` parametr zostanie pominięty, odpowiedź zawiera zestaw domyślny.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Dostęp Time-Based

Uzyskaj reprezentację wyniku tak jak wcześniej. Zobacz sekcję [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
