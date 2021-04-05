---
title: Korzystanie z metod bezpośrednich w analizie wideo na żywo na IoT Edge na platformie Azure
description: Analiza filmów wideo na żywo na IoT Edge uwidacznia kilka metod bezpośrednich. Metody bezpośrednie są oparte na konwencjach opisanych w tym temacie.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656244"
---
# <a name="direct-methods"></a>Metody bezpośrednie

Analiza filmów wideo na żywo na IoT Edge uwidacznia kilka metod bezpośrednich, które mogą być wywoływane z IoT Hub. Metody bezpośrednie reprezentują interakcję typu żądanie-odpowiedź z urządzeniem podobnym do wywołania HTTP, które powiodło się lub natychmiast kończą się niepowodzeniem (po upływie limitu czasu określonego przez użytkownika). Ta metoda przydaje się w scenariuszach, gdzie procedura natychmiastowego działania różni się w zależności od tego, czy urządzenie było w stanie odpowiedzieć. Aby uzyskać więcej informacji, zobacz [Omówienie i wywoływanie metod bezpośrednich z IoT Hub](../../iot-hub/iot-hub-devguide-direct-methods.md).

W tym temacie opisano te metody i konwencje.

## <a name="conventions"></a>Konwencje

Metody bezpośrednie są oparte na następujących konwencjach:

1. Metody bezpośrednie mają określoną wersję w języku głównym. Format POMOCNICZy (jak pokazano w poniższym przykładzie):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Dana wersja usługi Analiza filmów wideo na żywo w module IoT Edge będzie obsługiwała wszystkie metody bezpośrednie do bieżącej wersji. Na przykład moduł w wersji 1,3 będzie obsługiwał metody bezpośrednie z wersjami 1,3, 1,2, 1,1 i 1,0.
3. Wszystkie metody bezpośrednie są synchroniczne.
4. Wyniki błędu są zgodne ze schematem błędu OData.
5. Nazwy powinny mieć następujące ograniczenia:
    
    * Tylko znaki alfanumeryczne i łączniki, o ile nie zaczynają się i kończą znakiem kreski
    * Bez spacji
    * Maks. 32 znaków

### <a name="example"></a>Przykład

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Kody błędów najwyższego poziomu     

|Stan |Kod   |Komunikat|
|---|---|---|
|400|   BadRequest| Żądanie jest nieprawidłowe|
|400|   InvalidResource|    Zasób jest nieprawidłowy|
|400|   InvalidVersion| Nieprawidłowa wersja interfejsu API|
|402|   ConnectivityRequired    |Moduł Edge wymaga łączności z chmurą w celu zapewnienia prawidłowego działania.|
|404|   NotFound    |Nie znaleziono zasobu|
|409|   Konflikt|   Konflikt operacji|
|500|   InternalServerError|    Wewnętrzny błąd serwera|
|503|   ServerBusy| Serwer zajęty|

### <a name="detailed-error-codes"></a>Szczegółowe kody błędów

Szczegóły błędu sprawdzania poprawności, takie jak walidacje modułu grafu, są dodawane jako informacje o błędzie:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Stan|    Kod szczegółowy   |Opis|
|---|---|---|
|400|   GraphValidationError|   Ogólne błędy grafu, takie jak cykle lub partycjonowanie itd.|
|400|   ModuleValidationError|  Błędy sprawdzania poprawności specyficzne dla modułu.|
|409|   GraphTopologyInUse| Topologia grafu nadal odwołuje się do co najmniej jednego wystąpienia grafu.|
|409|   OperationNotAllowedInState| Żądana operacja nie może zostać wykonana w bieżącym stanie.|
|409|   ResourceValidationError|    Przywoływany zasób (przykład: element zawartości) nie jest w prawidłowym stanie.|

## <a name="details"></a>Szczegóły  

### <a name="graphtopologyget"></a>GraphTopologyGet

Ta metoda Direct pobiera topologię pojedynczego wykresu.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Znaleziono jednostkę | 200 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Nie znaleziono jednostki | 404 |  |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Tworzy pojedynczą topologię, jeśli nie istnieje już istniejąca o podaną nazwę lub aktualizacje i istniejąca nazwa o tej samej nazwie.

Kluczowe aspekty:

* Topologia może być swobodnie aktualizowana, ponieważ nie ma żadnego odwołującego się do niego grafu.
* Topologię można swobodnie aktualizować, jeśli wszystkie wykresy odwołujące są dezaktywowane tak długo, jak:

    * Nowo dodane parametry mają wartości domyślne
    * Żaden Graf nie odwołuje się do usuniętych parametrów
* Aktualizacje topologii są niedozwolone, jeśli istnieją aktywne wykresy

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Kody stanu

|Warunek  |Kod stanu    |Szczegółowy kod błędu|
|---|---|---|
Zaktualizowano istniejącą jednostkę |200|   Nie dotyczy|
Utworzono nową jednostkę  |201|   Nie dotyczy|
Ogólne błędy użytkowników |400 zakresu  ||
Błędy walidacji wykresu |400    |GraphValidationError|
Błędy sprawdzania poprawności modułu|   400 |ModuleValidationError|
Ogólne błędy serwera   |500 zakresu  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Usuwa topologię pojedynczego wykresu.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Usunięto jednostkę | 200 | Nie dotyczy |
| Nie znaleziono jednostki | 204 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Topologia grafu jest przywoływana przez co najmniej jedno wystąpienie grafu | 409 | GraphTopologyInUse |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphtopologylist"></a>GraphTopologyList

Pobiera listę wszystkich topologii wykresu, które pasują do kryteriów filtrowania.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Obsługa filtrów

|Operacja |Pola:    |Operatory|
|---|---|---|
|$orderby|name  |rosnąc|


#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Powodzenie | 200 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Pobiera pojedyncze wystąpienie grafu:

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Znaleziono jednostkę | 200 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Nie znaleziono jednostki | 404 |  |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Tworzy pojedyncze wystąpienie grafu, jeśli nie istnieje istniejący obiekt o podaną nazwę lub aktualizacje oraz istniejący o tej samej nazwie.

Kluczowe aspekty:

* Wystąpienie grafu można swobodnie aktualizować w stanie "zdezaktywowane".

    * Program Graph jest ponownie weryfikowany przy każdej aktualizacji.
* Aktualizacje wystąpienia grafu są częściowo ograniczone, gdy wykres nie jest w stanie "nieaktywny".
* Aktualizacje wystąpienia grafu nie są dozwolone dla aktywnych wykresów.

#### <a name="request"></a>Żądanie

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Reakcja

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Zaktualizowano istniejącą jednostkę | 200 | Nie dotyczy |
| Utworzono nową jednostkę | 201 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Błędy walidacji wykresu | 400 | GraphValidationError |
| Błędy sprawdzania poprawności modułu | 400 | ModuleValidationError |
| Błędy sprawdzania poprawności zasobów | 409 | ResourceValidationError |
| Ogólne błędy serwera | 500 zakresu |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Usuwa wystąpienie pojedynczego wykresu.

Kluczowe aspekty:

* Można usuwać tylko wykresy dezaktywowane.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Pomyślnie usunięto wykres | 200 | Nie dotyczy |
| Nie znaleziono grafu | 204 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Wykres nie jest w stanie "Zatrzymano" | 409 | OperationNotAllowedInState |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Jest to podobne do GraphTopologyList. Umożliwia użycie do wyliczenia wystąpień grafu.
Pobiera listę wszystkich wystąpień grafów, które pasują do kryteriów filtrowania.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Obsługa filtrów

|Operacja  |   Pola:|   Operatory|
|---|---|---|
|$orderby|  name|   rosnąc|

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Powodzenie | 200 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Aktywuje pojedyncze wystąpienie grafu. 

Kluczowe aspekty

* Metoda zwraca tylko wtedy, gdy wykres jest aktywowany 
* Program Graph przyjmuje stan "Aktywacja" podczas aktywowania.

    * Operacja list/Get na wykresie zwróci wykres w odpowiednim stanie.
* Idempotentności

    * Uruchamianie grafu w stanie "Aktywowanie" działa tak samo jak wtedy, gdy wykres został zdezaktywowany (to jest: bloki wywołań do momentu aktywowania grafu)
    * Aktywowanie grafu w stanie "aktywny" powiodło się.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Kody stanu

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Pomyślnie aktywowano wykres | 200 | Nie dotyczy |
| Utworzono nową jednostkę | 201 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Błędy sprawdzania poprawności modułu | 400 | ModuleValidationError |
| Błędy sprawdzania poprawności zasobów | 409 | ResourceValidationError |
| Wykres jest w stanie dezaktywowania | 409 | OperationNotAllowedInState |
| Ogólne błędy serwera | 500 zakresu |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Dezaktywuje pojedyncze wystąpienie grafu. Zdezaktywowanie wykresu pozwala bezpiecznie dezaktywować przetwarzanie multimediów i zapewnić, że wszystkie zdarzenia i nośniki przejściowo przechowywane na brzegu są zatwierdzane w chmurze, o ile ma to zastosowanie.

Kluczowe aspekty:

* Metoda zwraca tylko wtedy, gdy wykres jest dezaktywowany
* Podczas dezaktywowania wykresu zostanie przyjęty stan "dezaktywowanie".

    * Operacja list/Get na wykresie zwróci wykres w odpowiednim stanie.
    * Wartość Zatrzymaj kończy się tylko wtedy, gdy wszystkie nośniki zostały przekazane do chmury.
* Idempotentności

    * Dezaktywowanie wykresu w stanie "dezaktywowanie" ma taki sam sposób jak w przypadku dezaktywowania grafu (czyli bloków wywołań do momentu dezaktywowania grafu)
    * Dezaktywowanie wykresu w stanie "nieaktywny" powiodło się natychmiast.

#### <a name="request"></a>Żądanie

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reakcja

```
{
    "status": 200,
    "payload": null
}
```

| Warunek | Kod stanu | Szczegółowy kod błędu |
|--|--|--|
| Pomyślnie aktywowano wykres | 200 | Nie dotyczy |
| Utworzono nową jednostkę | 201 | Nie dotyczy |
| Ogólne błędy użytkowników | 400 zakresu |  |
| Wykres jest w stanie aktywowania | 409 | OperationNotAllowedInState |
| Ogólne błędy serwera | 500 zakresu |  |

## <a name="next-steps"></a>Następne kroki

[Schemat konfiguracji bliźniaczej reprezentacji modułu](module-twin-configuration-schema.md)
