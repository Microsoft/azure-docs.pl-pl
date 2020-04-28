---
title: Funkcje zdefiniowane przez użytkownika w szablonach
description: Opisuje sposób definiowania i używania funkcji zdefiniowanych przez użytkownika w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943217"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funkcje zdefiniowane przez użytkownika w szablonie Azure Resource Manager

W ramach szablonu można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Funkcje zdefiniowane przez użytkownika są niezależne od [standardowych funkcji szablonów](template-functions.md) , które są automatycznie dostępne w ramach szablonu. Utwórz własne funkcje, jeśli masz skomplikowane wyrażenia, które są wielokrotnie używane w szablonie.

W tym artykule opisano sposób dodawania funkcji zdefiniowanych przez użytkownika w szablonie Azure Resource Manager.

## <a name="define-the-function"></a>Zdefiniuj funkcję

Funkcje wymagają wartości przestrzeni nazw, aby uniknąć konfliktów nazw z funkcjami szablonu. Poniższy przykład pokazuje funkcję, która zwraca unikatową nazwę:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Korzystanie z funkcji

Poniższy przykład pokazuje szablon, który zawiera funkcję zdefiniowaną przez użytkownika. Używa tej funkcji, aby uzyskać unikatową nazwę konta magazynu. Szablon zawiera parametr o nazwie **storageNamePrefix** , który jest przekazywany jako parametr do funkcji.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Ograniczenia

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Gdy używana jest funkcja [Parameters](template-functions-deployment.md#parameters) w funkcji zdefiniowanej przez użytkownika, są ograniczone do parametrów tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może używać funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) .
* Parametry funkcji nie mogą mieć wartości domyślnych.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach funkcji zdefiniowanych przez użytkownika, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
* Listę dostępnych funkcji szablonu można znaleźć w temacie [Azure Resource Manager Template Functions](template-functions.md).
