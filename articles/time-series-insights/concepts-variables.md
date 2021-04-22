---
title: Zmienne modelu — Azure Time Series Insights Gen2 | Microsoft Docs
description: Zmienne modelu
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872969"
---
# <a name="time-series-model-variables"></a>Zmienne modelu szeregów czasu

W tym artykule opisano zmienne modelu szeregów czasu, które określają reguły formuł i obliczeń dla zdarzeń.

Każda zmienna może być jednym z trzech rodzajów: *liczbowych,* kategorii i *agregacji*.

* **Rodzaje liczbowe** działają z ciągłymi wartościami liczbowych.
* **Rodzaje podzielone na kategorie** działają ze zdefiniowanym zestawem wartości dyskretnych.
* **Agregowane** rodzaje łączą wiele zmiennych jednego rodzaju (wszystkich liczbowych lub wszystkich kategorii).

W poniższej tabeli przedstawiono właściwości, które są istotne dla każdego rodzaju zmiennej.

[![Tabela zmiennych modelu szeregów czasu](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Zmienne liczbowe

| Właściwość zmiennej | Opis |
| --- | ---|
| Filtr zmiennych | Filtry to opcjonalne klauzule warunkowe, które ograniczają liczbę wierszy rozważanych do obliczenia. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników albo przekształcane przy użyciu wyrażeń szeregów czasu. Zmienne liczbowe typu muszą mieć wartość lub , `Double` `Long` aby dopasować typ danych przychodzących.|
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. *Opcje* *interpolacji* krokowej i liniowej są dostępne dla zmiennych liczbowych. |
| Agregacja zmiennych | Wykonywanie obliczeń za pomocą obsługiwanych funkcji [agregacji dla rodzajów zmiennych liczbowych](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Zmienne podzielone na kategorii

| Właściwość zmiennej | Opis |
| --- | ---|
| Filtr zmiennych | Filtry to opcjonalne klauzule warunkowe, które ograniczają liczbę wierszy rozważanych do obliczenia. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników. Zmienne rodzaju podzielonego na kategorii muszą być albo lub , aby dopasować `Long` `String` typ danych przychodzących. |
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. Opcja  Interpolacja kroków jest dostępna dla zmiennych kategorii. |
| Kategorie zmiennych | Kategorie tworzą mapowanie wartości pochodzących z urządzenia lub czujników na etykietę. |
| Domyślna kategoria zmiennej | Kategoria domyślna jest dla wszystkich wartości, które nie są mapowane we właściwości "categories". |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Zmienne agregowane

| Właściwość zmiennej | Opis |
| --- | ---|
| Filtr zmiennych | Filtry to opcjonalne klauzule warunkowe, które ograniczają liczbę wierszy rozważanych do obliczenia. |
| Agregacja zmiennych | Wykonywanie obliczeń za pomocą obsługiwanych funkcji [agregacji dla typu zmiennych agregowania](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Zmienne są zgodne z następującym przykładem JSON:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Zmienne są przechowywane w definicji typu modelu szeregów czasowych i mogą być udostępniane w tekście za pośrednictwem interfejsów API w celu zastąpienia lub uzupełnienia przechowywanej definicji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [modelu szeregów czasu](./concepts-model-overview.md).

* Przeczytaj więcej na temat sposobu definiowania zmiennych w tekście przy użyciu [interfejsów API zapytań.](./concepts-query-overview.md)
