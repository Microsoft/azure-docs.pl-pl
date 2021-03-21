---
title: Interfejs API REST usługi Azure App Configuration — etykiety
description: Strony referencyjne do pracy z etykietami przy użyciu interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932510"
---
# <a name="labels"></a>Etykiety

wersja interfejsu API: 1,0

Zasób **etykiety** jest definiowany w następujący sposób:

```json
{
      "name": [string]             // Name of the label
}
```

Program obsługuje następujące operacje:

- Lista

Dla wszystkich operacji ``name`` jest opcjonalnym parametrem filtru. W przypadku pominięcia pociąga za **sobą etykiet.**

## <a name="prerequisites"></a>Wymagania wstępne

- Wszystkie żądania HTTP muszą zostać uwierzytelnione. Zobacz sekcję [uwierzytelnianie](./rest-api-authentication-index.md) .
- Wszystkie żądania HTTP muszą podawać jawne `api-version` . Zobacz sekcję [przechowywanie wersji](./rest-api-versioning.md) .

## <a name="list-labels"></a>Etykiety list

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Dzielenie na strony

Wynik jest podzielony na strony, jeśli liczba zwracanych elementów przekracza limit odpowiedzi. Postępuj zgodnie z opcjonalnymi `Link` nagłówkami odpowiedzi i Użyj `rel="next"` na potrzeby nawigacji. Alternatywnie zawartość zawiera następny link w postaci `@nextLink` właściwości. Następny link zawiera `api-version` parametr.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Reakcji**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtrowanie

Filtrowanie według `name` jest obsługiwane.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Obsługiwane filtry

|Filtr klucza|Efekt|
|--|--|
|Element `name` jest pomijany lub `name=*`|Dopasowuje **dowolną** etykietę|
|`name=abc`|Dopasowuje etykietę o nazwie  **ABC**|
|`name=abc*`|Dopasowuje nazwy etykiet, które zaczynają się od **ABC**|
|`name=abc,xyz`|Dopasowuje nazwy etykiet **ABC** lub **XYZ** (ograniczone do 5 woluminów CSV)|

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
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Przykłady

- Wszystko

    ```http
    GET /labels?api-version={api-version}
    ```

- Nazwa etykiety rozpoczyna się od **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- Nazwa etykiety jest **ABC** lub **XYZ**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Żądaj pól określonych

Użyj opcjonalnego `$select` parametru ciągu zapytania i podaj rozdzieloną przecinkami listę wymaganych pól. Jeśli `$select` parametr zostanie pominięty, odpowiedź zawiera zestaw domyślny.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Dostęp Time-Based

Uzyskaj reprezentację wyniku tak jak wcześniej. Zobacz sekcję [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
