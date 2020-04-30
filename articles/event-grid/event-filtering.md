---
title: Filtrowanie zdarzeń dla Azure Event Grid
description: Opisuje sposób filtrowania zdarzeń podczas tworzenia subskrypcji Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: ce1bb3760ae73a9eaeee3cde957cc94841ebdf29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731939"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Opis filtrowania zdarzeń dla subskrypcji Event Grid

W tym artykule opisano różne sposoby filtrowania zdarzeń wysyłanych do punktu końcowego. Podczas tworzenia subskrypcji zdarzeń dostępne są trzy opcje filtrowania:

* Typy zdarzeń
* Podmiot rozpoczyna się od lub kończąc z
* Zaawansowane pola i operatory

## <a name="event-type-filtering"></a>Filtrowanie typów zdarzeń

Domyślnie wszystkie [typy zdarzeń](event-schema.md) dla źródła zdarzeń są wysyłane do punktu końcowego. Możesz zdecydować się na wysłanie tylko niektórych typów zdarzeń do punktu końcowego. Można na przykład otrzymywać powiadomienia o aktualizacjach zasobów, ale nie powiadomienia o innych operacjach, takich jak usuwanie. W takim przypadku należy filtrować według typu `Microsoft.Resources.ResourceWriteSuccess` zdarzenia. Podaj tablicę z typami zdarzeń lub Określ `All` , aby pobrać wszystkie typy zdarzeń dla źródła zdarzeń.

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

Dla prostego filtrowania według tematu Określ wartość początkową lub końcową podmiotu. Można na przykład określić, że temat ma kończyć `.txt` się tylko zdarzenia związane z przekazywaniem pliku tekstowego do konta magazynu. Można też odfiltrować temat od, `/blobServices/default/containers/testcontainer` aby pobierać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu.

Podczas publikowania zdarzeń w niestandardowych tematach, należy utworzyć tematy dotyczące wydarzeń, które ułatwią subskrybentom dowiedzieć się, czy zainteresują się wydarzeniem. Subskrybenci używają właściwości podmiotu do filtrowania i kierowania zdarzeń. Rozważ dodanie ścieżki do miejsca wystąpienia zdarzenia, dzięki czemu Subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom Zawężanie lub szerokie filtrowanie zdarzeń. Jeśli podano trzy ścieżki segmentu jak `/A/B/C` w temacie, subskrybenci mogą odfiltrować według pierwszego segmentu `/A` , aby uzyskać obszerny zestaw zdarzeń. Subskrybenci uzyskują zdarzenia z podmiotami `/A/B/C` takimi jak lub `/A/D/E`. Inni Subskrybenci mogą odfiltrować, `/A/B` Aby uzyskać węższy zestaw zdarzeń.

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

### <a name="operator"></a>Operator

Dostępne operatory dla liczb to:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* Numer w
* NumberNotIn

Operatorem dostępnym dla wartości logicznych jest: BoolEquals

Dostępne operatory dla ciągów są następujące:

* StringContains
* StringBeginsWith
* StringEndsWith
* Ciąg w
* StringNotIn

Wszystkie porównania ciągów mają wielkość liter-insensitve.

### <a name="key"></a>Key

Dla zdarzeń w schemacie Event Grid należy użyć następujących wartości klucza:

* ID
* Temat
* Podmiot
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

* numer
* ciąg
* wartość logiczna
* tablica

### <a name="limitations"></a>Ograniczenia

Filtrowanie zaawansowane ma następujące ograniczenia:

* Pięć zaawansowanych filtrów na subskrypcję usługi Event Grid
* 512 znaków na wartość ciągu
* Pięć wartości dla operatora **in** i **Not in**

Ten sam klucz może być używany w więcej niż jednym filtrze.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o filtrowaniu zdarzeń przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [filtrowanie zdarzeń dla Event Grid](how-to-filter-events.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
