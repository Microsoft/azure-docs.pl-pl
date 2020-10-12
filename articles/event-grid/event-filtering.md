---
title: Filtrowanie zdarzeń dla Azure Event Grid
description: Opisuje sposób filtrowania zdarzeń podczas tworzenia subskrypcji Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 837209d4197c271598155776b8d171a705e1f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120096"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Opis filtrowania zdarzeń dla subskrypcji Event Grid

W tym artykule opisano różne sposoby filtrowania zdarzeń wysyłanych do punktu końcowego. Podczas tworzenia subskrypcji zdarzeń dostępne są trzy opcje filtrowania:

* Typy zdarzeń
* Podmiot rozpoczyna się od lub kończąc z
* Zaawansowane pola i operatory

## <a name="event-type-filtering"></a>Filtrowanie typów zdarzeń

Domyślnie wszystkie [typy zdarzeń](event-schema.md) dla źródła zdarzeń są wysyłane do punktu końcowego. Możesz zdecydować się na wysłanie tylko niektórych typów zdarzeń do punktu końcowego. Można na przykład otrzymywać powiadomienia o aktualizacjach zasobów, ale nie powiadomienia o innych operacjach, takich jak usuwanie. W takim przypadku należy filtrować według `Microsoft.Resources.ResourceWriteSuccess` typu zdarzenia. Podaj tablicę z typami zdarzeń lub określ, `All` Aby pobrać wszystkie typy zdarzeń dla źródła zdarzeń.

Składnia JSON dla filtrowania według typu zdarzenia:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrowanie podmiotu

Dla prostego filtrowania według tematu Określ wartość początkową lub końcową podmiotu. Można na przykład określić, że temat ma kończyć się `.txt` tylko zdarzenia związane z przekazywaniem pliku tekstowego do konta magazynu. Można też odfiltrować temat od, `/blobServices/default/containers/testcontainer` Aby pobierać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu.

Podczas publikowania zdarzeń w niestandardowych tematach, należy utworzyć tematy dotyczące wydarzeń, które ułatwią subskrybentom dowiedzieć się, czy zainteresują się wydarzeniem. Subskrybenci używają właściwości podmiotu do filtrowania i kierowania zdarzeń. Rozważ dodanie ścieżki do miejsca wystąpienia zdarzenia, dzięki czemu Subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom Zawężanie lub szerokie filtrowanie zdarzeń. Jeśli podano trzy ścieżki segmentu jak `/A/B/C` w temacie, subskrybenci mogą odfiltrować według pierwszego segmentu, `/A` Aby uzyskać obszerny zestaw zdarzeń. Subskrybenci uzyskują zdarzenia z podmiotami takimi jak `/A/B/C` lub `/A/D/E` . Inni Subskrybenci mogą odfiltrować, `/A/B` Aby uzyskać węższy zestaw zdarzeń.

Składnia JSON dla filtrowania według tematu to:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Aby filtrować według wartości w polach danych i określić operator porównania, użyj opcji filtrowania zaawansowanego. W zaawansowanym filtrowaniu należy określić:

* Typ operatora — typ porównania.
* klucz — pole w danych zdarzenia, które są używane do filtrowania. Może to być liczba, wartość logiczna lub ciąg.
* wartości — wartość lub wartości, które mają zostać porównane z kluczem.

Jeśli określisz jeden filtr z wieloma wartościami, wykonywana jest operacja **lub** , więc wartość pola klucza musi być jedną z tych wartości. Oto przykład:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Jeśli określisz wiele różnych filtrów, operacja **i** zostanie wykonana, więc należy spełnić każdy warunek filtru. Oto przykład: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operatory

Dostępne operatory dla **liczb** to:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* Numer w
* NumberNotIn

Operatorem dostępnym dla **wartości logicznych** jest: 
- BoolEquals

Dostępne operatory dla **ciągów** są następujące:

* StringContains
* StringBeginsWith
* StringEndsWith
* Ciąg w
* StringNotIn

W przypadku wszystkich porównań ciągów **nie** jest rozróżniana wielkość liter.

### <a name="key"></a>Klucz

Dla zdarzeń w schemacie Event Grid należy użyć następujących wartości klucza:

* ID (Identyfikator)
* Temat
* Temat
* Typ zdarzenia
* Wersja
* Dane zdarzenia (np. Data. Klucz1)

Dla zdarzeń w schemacie zdarzenia w chmurze Użyj następujących wartości klucza:

* EventId
* Element źródłowy
* Typ zdarzenia
* EventTypeVersion
* Dane zdarzenia (np. Data. Klucz1)

W przypadku niestandardowego schematu wprowadzania Użyj pól danych zdarzenia (np. Data. Klucz1).

### <a name="values"></a>Wartości

Mogą to być następujące wartości:

* liczba
* ciąg
* boolean
* array

### <a name="limitations"></a>Ograniczenia

Filtrowanie zaawansowane ma następujące ograniczenia:

* 5 filtry zaawansowane i 25 wartości filtru dla wszystkich filtrów dla każdej subskrypcji usługi Event Grid
* 512 znaków na wartość ciągu
* Pięć wartości dla operatora **in** i **Not in**
* Klucze ze znakiem ** `.` (kropką)** . Na przykład: `http://schemas.microsoft.com/claims/authnclassreference` lub `john.doe@contoso.com` . Obecnie nie ma obsługi znaków ucieczki w kluczach. 

Ten sam klucz może być używany w więcej niż jednym filtrze.

### <a name="examples"></a>Przykłady

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>Ciąg w

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>Numer w

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o filtrowaniu zdarzeń przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [filtrowanie zdarzeń dla Event Grid](how-to-filter-events.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
