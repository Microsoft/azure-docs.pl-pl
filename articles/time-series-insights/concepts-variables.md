---
title: Zmienne modelu — Azure Time Series Insights Gen2 | Microsoft Docs
description: Zmienne modelu
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: dc03a74bfad5b1417eb9cc20b5f211718cc08646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653710"
---
# <a name="time-series-model-variables"></a>Zmienne modelu szeregów czasowych

W tym artykule opisano zmienne modelu szeregów czasowych, które określają reguły formuł i obliczeń dla zdarzeń.

Każda zmienna może być jednym z trzech rodzajów: *liczbowej*, *kategorii*i *agregacji*.

* Rodzaje **liczbowe** pracują z ciągłymi wartościami liczbowymi.
* Rodzaje **kategorii** działają ze zdefiniowanym zestawem wartości dyskretnych.
* Rodzaje **agregacji** łączą wiele zmiennych pojedynczego rodzaju (wszystkie wartości liczbowe lub wszystkie kategorii).

W poniższej tabeli przedstawiono właściwości, które są istotne dla poszczególnych rodzajów zmiennych.

[![Tabela zmiennych modelu szeregów czasowych](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Zmienne liczbowe

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników lub przekształcone przy użyciu wyrażeń szeregów czasowych. Zmienne rodzaju liczbowego muszą być typu *Double*.|
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. Opcje interpolacji *krokowej* i *liniowej* są dostępne dla zmiennych liczbowych. |
| Agregacja zmiennych | Wykonywanie obliczeń za pomocą obsługiwanych [funkcji agregacji dla zmiennych liczbowych](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

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

## <a name="categorical-variables"></a>Zmienne kategorii

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Wartość zmiennej | Wartości telemetryczne używane do obliczeń pochodzących z urządzenia lub czujników. Zmienne typu kategorii muszą mieć wartość *Long* lub *String*. |
| Interpolacja zmiennych | Interpolacja określa, jak odtworzyć sygnał przy użyciu istniejących danych. Opcja interpolacji *kroku* jest dostępna dla zmiennych kategorii. |
| Kategorie zmiennych | Kategorie tworzą mapowanie między wartościami pochodzącymi z urządzenia lub czujników do etykiety. |
| Kategoria domyślna zmiennej | Kategoria domyślna dotyczy wszystkich wartości, które nie są mapowane we właściwości "Kategorie". |

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

## <a name="aggregate-variables"></a>Zmienne agregujące

| Variable — Właściwość | Opis |
| --- | ---|
| Filtr zmiennych | Filtry są opcjonalnymi klauzulami warunkowymi, aby ograniczyć liczbę wierszy, które są brane pod uwagę podczas obliczania. |
| Agregacja zmiennych | Wykonywanie obliczeń za pomocą obsługiwanych [funkcji agregacji dla typów zmiennych agregacji](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

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

Zmienne są przechowywane w definicji typu w modelu szeregów czasowych i mogą być dostarczane wewnętrznie za pośrednictwem interfejsów API w celu przesłonięcia lub uzupełnienia przechowywanej definicji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [modelu szeregów czasowych](./concepts-model-overview.md).

* Przeczytaj więcej na temat sposobu definiowania zmiennych wbudowanych przy użyciu [interfejsów API zapytań](./concepts-query-overview.md).
