---
title: Funkcje szablonu — dowolne
description: Opisuje każdą funkcję, która jest dostępna w Bicep do konwersji typów.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746917"
---
# <a name="any-function-for-bicep"></a>Dowolna funkcja dla Bicep

Bicep obsługuje funkcję wywołana `any()` do rozwiązywania błędów typu w systemie typu Bicep. Ta funkcja jest używana, gdy format wartości, którą podano, nie jest zgodny z oczekiwanym typem systemu. Na przykład, jeśli właściwość wymaga liczby, ale musisz podać ją jako ciąg, np `'0.5'` .. Użyj `any()` funkcji, aby pominąć błąd raportowany przez system typów.

Ta funkcja nie istnieje w środowisku uruchomieniowym szablonu Azure Resource Manager. Jest on używany tylko przez Bicep i nie jest emitowany w formacie JSON dla skompilowanego szablonu.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>dowolny

`any(value)`

Zwraca wartość, która jest zgodna z dowolnym typem danych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| wartość | Tak | wszystkie typy | Wartość do przekonwertowania na typ zgodny. |

### <a name="return-value"></a>Wartość zwracana

Wartość w formularzu, która jest zgodna z dowolnym typem danych.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje, jak używać funkcji, `any()` Aby podać wartości liczbowe jako ciągi.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

Funkcja działa na dowolnej przypisanej wartości w Bicep. Poniższy przykład używa `any()` z wyrażeniem Trzyelementowy jako argumentem.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Następne kroki

Bardziej skomplikowane zastosowania `any()` funkcji można znaleźć w następujących przykładach:

* [Zasoby podrzędne, które wymagają określonych nazw](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Nie zdefiniowano właściwości zasobu w typie zasobu, nawet jeśli istnieje](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

