---
title: Tworzenie odwołań do definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Portal, które odwołują się do innych obiektów.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098175"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition funkcje odwołujące

Funkcje, które mają być używane podczas odwoływania się do danych wyjściowych z właściwości lub kontekstu pliku CreateUiDefinition.

## <a name="basics"></a>nazwie

Zwraca wartości wyjściowe elementu, który jest zdefiniowany w kroku [podstawowe](create-uidefinition-overview.md#basics) . Przekaż nazwę elementu jako parametr do tej funkcji.

Aby uzyskać wartości wyjściowe elementów w innych krokach, użyj funkcji [kroki ()](#steps) .

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `clusterName` w kroku podstawowe:

```json
"[basics('clusterName')]"
```

Zwracane wartości różnią się w zależności od typu pobieranego elementu.

## <a name="location"></a>location

Zwraca lokalizację wybraną w kroku podstawowe lub w bieżącym kontekście.

Poniższy przykład zwraca wartość taką jak `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Zwraca szczegółowe informacje o liście zasobów wybranych w kroku podstawowe lub w bieżącym kontekście.

Poniższy przykład:

```json
"[resourceGroup()]"
```

Zwraca następujące właściwości:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Każdą konkretną wartość można uzyskać za pomocą notacji kropkowej.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>kroki

Zwraca elementy w określonym kroku. Przekaż nazwę kroku jako parametr do tej funkcji. Z zwróconych elementów można uzyskać określone wartości właściwości.

Aby uzyskać wartości wyjściowe elementów w kroku podstawowe, użyj funkcji [podstawowe ()](#basics) .

Poniższy przykład zwraca krok o nazwie `vmParameters` . W tym kroku jest elementem o nazwie `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subskrypcja

Zwraca właściwości subskrypcji wybranej w kroku podstawowe lub w bieżącym kontekście.

Poniższy przykład:

```json
"[subscription()]"
```

Zwraca następujące właściwości:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia interfejsu portalu, zobacz [CreateUiDefinition.jsna potrzeby tworzenia środowiska aplikacji zarządzanej przez platformę Azure](create-uidefinition-overview.md).
