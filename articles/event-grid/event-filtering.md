---
title: Filtrowanie zdarzeń dla usługi Azure Event Grid
description: W tym artykule opisano sposób filtrowania zdarzeń podczas tworzenia subskrypcji usługi Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: ce1bb3760ae73a9eaeee3cde957cc94841ebdf29
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731939"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Opis filtrowania zdarzeń dla subskrypcji usługi Event Grid

W tym artykule opisano różne sposoby filtrowania zdarzeń, które są wysyłane do punktu końcowego. Podczas tworzenia subskrypcji zdarzeń dostępne są trzy opcje filtrowania:

* Typy zdarzeń
* Temat zaczyna się od
* Zaawansowane pola i operatorzy

## <a name="event-type-filtering"></a>Filtrowanie typu zdarzenia

Domyślnie wszystkie [typy zdarzeń](event-schema.md) dla źródła zdarzeń są wysyłane do punktu końcowego. Można zdecydować się na wysłanie tylko niektóre typy zdarzeń do punktu końcowego. Na przykład można otrzymywać powiadomienia o aktualizacjach zasobów, ale nie są powiadamiane o innych operacjach, takich jak usunięcia. W takim przypadku filtruj według typu `Microsoft.Resources.ResourceWriteSuccess` zdarzenia. Podaj tablicę z typami zdarzeń lub określ, `All` aby uzyskać wszystkie typy zdarzeń dla źródła zdarzeń.

Składnia JSON do filtrowania według typu zdarzenia jest:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrowanie obiektów

W przypadku prostego filtrowania według tematu należy określić wartość początkową lub końcową obiektu. Na przykład można określić temat `.txt` kończy się tylko uzyskać zdarzenia związane z przekazywaniem pliku tekstowego do konta magazynu. Lub można filtrować temat `/blobServices/default/containers/testcontainer` zaczyna się, aby uzyskać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu.

Publikując zdarzenia w tematach niestandardowych, utwórz tematy dla wydarzeń, które ułatwiają subskrybentom dowiedzieć się, czy są zainteresowani wydarzeniem. Subskrybenci używają właściwości podmiot do filtrowania i rozsyłania zdarzeń. Należy rozważyć dodanie ścieżki dla miejsca, w którym zdarzenie miało miejsce, dzięki czemu subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom wąsko lub szeroko filtrować zdarzenia. Jeśli podasz ścieżkę `/A/B/C` trzech segmentów, jak w temacie, subskrybenci mogą filtrować według pierwszego segmentu, `/A` aby uzyskać szeroki zestaw zdarzeń. Ci subskrybenci otrzymują `/A/B/C` `/A/D/E`wydarzenia z tematami takimi jak lub . Inni subskrybenci mogą filtrować, `/A/B` aby uzyskać węższy zestaw zdarzeń.

Składnia JSON do filtrowania według tematu jest:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Aby filtrować według wartości w polach danych i określać operator porównania, użyj opcji filtrowania zaawansowanego. W przypadku filtrowania zaawansowanego należy określić:

* typ operatora — typ porównania.
* - pole w danych zdarzenia, których używasz do filtrowania. Może to być liczba, wartość logiczna lub ciąg.
* wartości — wartość lub wartości do porównania z kluczem.

Jeśli określisz pojedynczy filtr z wieloma wartościami, wykonywana jest operacja **OR,** więc wartość pola klucza musi być jedną z tych wartości. Oto przykład:

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

Jeśli określisz wiele różnych filtrów, wykonywana jest operacja **AND,** więc każdy warunek filtru musi być spełniony. Oto przykład: 

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

Dostępne operatory dla numerów to:

* LiczbaGreaterThan
* LiczbaGreaterThanOrEquals
* Liczba Bezduszny
* NumberLessThanOrEquals (Liczba bezliory)
* LiczbaIn
* LiczbaNotIn

Dostępny operator dla booleans jest: BoolEquals

Dostępne operatory dla ciągów to:

* CiągiZawiera
* StringBeginsWith
* StringEndsWith
* StringIn (Ciągin)
* StringNotIn (Niemy w ciągu ręczną

Wszystkie porównania ciągów są bez uwzględniania wielkości liter.

### <a name="key"></a>Klucz

W przypadku zdarzeń w schemacie siatki zdarzeń użyj następujących wartości dla klucza:

* ID
* Temat
* Podmiot
* Typ zdarzenia
* DataVersion (Wersja danych)
* Dane zdarzeń (takie jak Data.key1)

W przypadku zdarzeń w schemacie zdarzeń w chmurze użyj następujących wartości dla klucza:

* Eventid
* Element źródłowy
* Typ zdarzenia
* EventTypeVersion (Wersja eventowa)
* Dane zdarzeń (takie jak Data.key1)

W przypadku niestandardowego schematu wejściowego należy użyć pól danych zdarzeń (takich jak Data.key1).

### <a name="values"></a>Wartości

Wartości mogą być następujące:

* numer
* ciąg
* wartość logiczna
* tablica

### <a name="limitations"></a>Ograniczenia

Zaawansowane filtrowanie ma następujące ograniczenia:

* Pięć zaawansowanych filtrów na subskrypcję sieci zdarzeń
* 512 znaków na wartość ciągu
* Pięć wartości dla **operatorów, a** **nie dla** operatorów

Ten sam klucz może być używany w więcej niż jednym filtrze.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o filtrowaniu zdarzeń za pomocą programów PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](how-to-filter-events.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
