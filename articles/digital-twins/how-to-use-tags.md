---
title: Dodawanie tagów do cyfrowego bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zobacz, jak zaimplementować Tagi w Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c2620b52c426871b0ec85e3db237be2d373d42f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458706"
---
# <a name="add-tags-to-digital-twins"></a>Dodawanie tagów do cyfrowego bliźniaczych reprezentacji 

Można użyć koncepcji tagów do dalszej identyfikacji i kategoryzacji cyfrowego bliźniaczych reprezentacji. W szczególności użytkownicy mogą chcieć replikować Tagi z istniejących systemów, takich jak [Tagi Haystack](https://project-haystack.org/doc/TagModel), w ramach swoich wystąpień bliźniaczych reprezentacji cyfrowych platformy Azure. 

W tym dokumencie opisano wzorce, których można użyć w celu zaimplementowania tagów w Digital bliźniaczych reprezentacji.

Tagi są najpierw dodawane jako właściwości w [modelu](concepts-models.md) , który opisuje wieloosiową cyfrę. Ta właściwość jest następnie ustawiana na sznurze, gdy zostanie utworzony na podstawie modelu. Po tym znaczniki mogą być używane w [zapytaniach](concepts-query-language.md) w celu identyfikowania i filtrowania bliźniaczych reprezentacji.

## <a name="marker-tags"></a>Znaczniki znaczników 

Znacznik **znacznika** jest prostym ciągiem, który jest używany do oznaczania lub kategoryzowania dwucyfrowego sznurka, takiego jak "Blue" lub "Red". Ten ciąg jest nazwą tagu, a znaczniki znacznika nie mają znaczącej wartości — tag jest istotny tylko przez jego obecność (lub nieobecność). 

### <a name="add-marker-tags-to-model"></a>Dodaj znaczniki znacznika do modelu 

Znaczniki znacznika są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z elementu `string` do `boolean` . Wartość logiczna `mapValue` jest ignorowana, ponieważ obecność znacznika jest istotna. 

Poniżej znajduje się fragment modelu dwuosiowego implementujący tag znacznika jako właściwość:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Dodawanie tagów znacznika do cyfrowego bliźniaczych reprezentacji

Gdy `tags` Właściwość jest częścią modelu przędzy cyfrowej, można ustawić tag znacznika w dwucyfrowej formie, ustawiając wartość tej właściwości. 

Oto przykład, który wypełnia znacznik `tags` dla trzech bliźniaczych reprezentacji:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Zapytanie ze znacznikami znaczników

Po dodaniu tagów do cyfrowego bliźniaczych reprezentacji Tagi mogą służyć do filtrowania bliźniaczych reprezentacji w zapytaniach. 

Oto zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji oznaczone jako "Red": 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Możesz również łączyć Tagi, aby uzyskać bardziej skomplikowane zapytania. Oto zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji, które są okrągłe, a nie czerwony: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND is_defined(tags.round) 
```

## <a name="value-tags"></a>Tagi wartości 

**Tag wartości** jest parą klucz-wartość, która jest używana do nadawania każdemu tagowi wartości, takiej jak `"color": "blue"` lub `"color": "red"` . Po utworzeniu znacznika wartości można go również użyć jako znacznika znacznika, ignorując wartość znacznika. 

### <a name="add-value-tags-to-model"></a>Dodawanie tagów wartości do modelu 

Tagi wartości są modelowane jako mapa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) z elementu `string` do `string` . Zarówno, `mapKey` jak i `mapValue` są istotne. 

Poniżej znajduje się fragment modelu dwuosiowego implementujący tag wartości jako właściwość:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Dodawanie tagów wartości do Digital bliźniaczych reprezentacji

Podobnie jak w przypadku znaczników znacznika, można ustawić tag wartości w formie dwucyfrowej, ustawiając wartość tej `tags` właściwości z modelu. Aby użyć tagu wartości jako znacznika znacznika, można ustawić `tagValue` pole na wartość pustego ciągu ( `""` ). 

Oto przykład, który wypełnia wartość `tags` dla trzech bliźniaczych reprezentacji:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

Należy zauważyć, że `red` i `purple` są używane jako znaczniki znaczników w tym przykładzie.

### <a name="query-with-value-tags"></a>Zapytanie z tagami wartości

Podobnie jak w przypadku tagów znacznika, można użyć tagów wartości do filtrowania bliźniaczych reprezentacji w zapytaniach. Możesz również używać tagów wartości i znaczników znaczników jednocześnie.

Z powyższego przykładu `red` jest używany jako tag znacznika. Oto zapytanie, aby pobrać wszystkie bliźniaczych reprezentacji oznaczone jako "Red": 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Oto zapytanie umożliwiające pobieranie wszystkich jednostek, które są małe (tag wartości), a nie czerwonych: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND tags.size = 'small' 
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat projektowania modeli cyfrowych bliźniaczych i zarządzania nimi:
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)

Przeczytaj więcej na temat wykonywania zapytań na grafie bliźniaczym:
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)