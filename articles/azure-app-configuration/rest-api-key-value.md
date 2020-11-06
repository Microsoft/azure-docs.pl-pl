---
title: Interfejs API REST usługi Azure App Configuration — Key-Value
description: Strony referencyjne do pracy z kluczowymi wartościami przy użyciu interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424138"
---
# <a name="key-values"></a>Key-Values

wersja interfejsu API: 1,0

Klucz-wartość to zasób identyfikowany przez unikatową kombinację `key`  +  `label` . Parametr `label` jest opcjonalny. Aby jawnie odwoływać się do wartości klucza bez etykiet, użyj "\ 0" (adres URL zakodowany jako ``%00`` ). Zobacz szczegóły każdej operacji.

## <a name="operations"></a>Operacje

- Get
- Lista wielu
- Set
- Usuń

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Składnia

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Pobierz Key-Value

**Wymagane:** ``{key}`` , ``{api-version}``  
*Opcjonalne:* ``label`` -Jeśli pominięty, oznacza to, że klucz-wartość nie jest etykietą

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Jeśli klucz nie istnieje, zwracana jest następująca odpowiedź:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Pobierz (warunkowo)

Aby poprawić buforowanie klienta, użyj `If-Match` lub `If-None-Match` Zażądaj nagłówków. `etag`Argument jest częścią reprezentacji klucza. Zobacz [sekcję 14,24 i 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Poniższe żądanie pobiera wartość klucza tylko wtedy, gdy bieżąca reprezentacja nie jest zgodna z określoną `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Reagowani**

```http
HTTP/1.1 304 NotModified
```

lub

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Key-Values listy

Zobacz **filtrowanie** dla opcji dodatkowych

*Opcjonalne:* ``key`` -Jeśli nie zostanie określony, oznacza **dowolny** klucz.
*Opcjonalne:* ``label`` -Jeśli nie zostanie określony, oznacza to, że **wszystkie** etykiety.

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>Dzielenie na strony

Wynik jest podzielony na strony, jeśli liczba zwracanych elementów przekracza limit odpowiedzi. Postępuj zgodnie z opcjonalnymi `Link` nagłówkami odpowiedzi i Użyj `rel="next"` na potrzeby nawigacji.
Alternatywnie zawartość zawiera następny link w postaci `@nextLink` właściwości. Połączony identyfikator URI zawiera `api-version` argument.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

`key`Obsługiwane są kombinacje i `label` filtrowanie.
Użyj parametrów opcjonalnych `key` i `label` ciągu zapytania.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Obsługiwane filtry

|Filtr klucza|Efekt|
|--|--|
|Element `key` jest pomijany lub `key=*`|Dopasowuje **dowolny** klucz|
|`key=abc`|Dopasowuje klucz o nazwie **ABC**|
|`key=abc*`|Dopasowuje nazwy kluczy, które zaczynają się od **ABC**|
|`key=abc,xyz`|Dopasowuje nazwy kluczy **ABC** lub **XYZ** (ograniczone do 5 woluminów CSV)|

|Filtr etykiet|Efekt|
|--|--|
|Element `label` jest pomijany lub `label=*`|Dopasowuje **dowolną** etykietę|
|`label=%00`|Dopasowuje KV bez etykiety|
|`label=prod`|Dopasowuje etykietę **prod**|
|`label=prod*`|Dopasowuje etykiety rozpoczynające się od **prod**|
|`label=prod,test`|Dopasowuje etykiety **prod** lub **test** (ograniczone do 5 woluminów CSV)|

**_Znaki zastrzeżone_* _

`_`, `\`, `,`

Jeśli zarezerwowany znak jest częścią wartości, należy użyć klawisza ucieczki `\{Reserved Character}` . Znaki niezastrzeżone mogą również zostać zmienione.

***Sprawdzanie poprawności filtru** _

W przypadku błędu walidacji filtru odpowiedź jest HTTP `400` z szczegółowymi informacjami o błędzie:

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

_ *Przykłady**

- Wszystko

    ```http
    GET /kv?api-version={api-version}
    ```

- Nazwa klucza rozpoczyna się od **ABC** i zawiera wszystkie etykiety

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Nazwa klucza rozpoczyna się od **ABC** i etykieta jest równa **V1** lub **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Żądaj pól określonych

Użyj opcjonalnego `$select` parametru ciągu zapytania i podaj rozdzieloną przecinkami listę wymaganych pól. Jeśli `$select` parametr zostanie pominięty, odpowiedź zawiera zestaw domyślny.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Dostęp Time-Based

Uzyskaj reprezentację wyniku tak jak wcześniej. Zobacz sekcję [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). Podział na strony jest nadal obsługiwany zgodnie z definicją powyżej.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Reakcji**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>Ustaw klucz

- **Wymagane:**``{key}``
- *Opcjonalne:* ``label`` -Jeśli nie określono lub etykieta = %00, oznacza to, że nie ma etykiety.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Reagowani**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Jeśli element jest zablokowany, otrzymasz następującą odpowiedź:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Ustaw klucz (warunkowo)

Aby zapobiec występowaniu warunków wyścigu, użyj `If-Match` lub `If-None-Match` Zażądaj nagłówków. `etag`Argument jest częścią reprezentacji klucza.
Jeśli `If-Match` lub `If-None-Match` zostaną pominięte, operacja będzie bezwarunkowo.

Następująca odpowiedź aktualizuje wartość tylko wtedy, gdy bieżąca reprezentacja pasuje do określonego `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Następująca odpowiedź aktualizuje wartość tylko wtedy, gdy bieżąca reprezentacja *nie* jest zgodna z określoną `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Poniższe żądanie dodaje wartość tylko wtedy, gdy reprezentacja już istnieje:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Poniższe żądanie dodaje wartość tylko wtedy, gdy reprezentacja jeszcze *nie* istnieje:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Odpowiedzi**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

lub

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Usuń

- **Wymagane:** `{key}` , `{api-version}`
- *Opcjonalne:* `{label}` -Jeśli nie określono lub etykieta = %00, oznacza to, że nie ma etykiety.

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Odpowiedź:** Zwróć usunięty klucz-wartość lub brak, jeśli klucz-wartość nie istnieje.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

lub

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Usuń klucz (warunkowo)

Podobne do **ustawiania klucza (warunkowo)**
