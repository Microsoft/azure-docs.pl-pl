---
title: Zmienne w szablonach
description: Opisuje sposób definiowania zmiennych w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 7758451747503cb8c42566dc081a91f84bafa375
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95916007"
---
# <a name="variables-in-azure-resource-manager-template"></a>Zmienne w szablonie usługi Azure Resource Manager

W tym artykule opisano sposób definiowania i używania zmiennych w szablonie Azure Resource Manager. Do uproszczenia szablonu używane są zmienne. Zamiast powtarzać skomplikowane wyrażenia w całym szablonie, należy zdefiniować zmienną, która zawiera wyrażenie złożone. Następnie należy odwołać się do tej zmiennej w zależności od potrzeb w szablonie.

Menedżer zasobów rozpoznaje zmienne przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie w szablonie jest używana zmienna, usługa Resource Manager zastępuje ją rozpoznaną wartością.

Format każdej zmiennej musi być zgodny z jednym z [typów danych](template-syntax.md#data-types).

## <a name="define-variable"></a>Zdefiniuj zmienną

W poniższym przykładzie przedstawiono definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu do uzyskania wartości parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Nie można użyć funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) w sekcji zmienne. Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy zmienne są rozwiązane.

## <a name="use-variable"></a>Użyj zmiennej

W szablonie należy odwołać się do wartości parametru za pomocą funkcji [zmienne](template-functions-deployment.md#variables) . Poniższy przykład pokazuje, jak używać zmiennej dla właściwości zasobu.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Zmienne konfiguracyjne

Można zdefiniować zmienne, które przechowują powiązane wartości w celu skonfigurowania środowiska. Zmienna jest definiowana jako obiekt z wartościami. Poniższy przykład pokazuje obiekt, który przechowuje wartości dla dwóch środowisk — **test** i **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

W parametrach można utworzyć wartość wskazującą, które wartości konfiguracji mają być używane.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Aby pobrać ustawienia dla określonego środowiska, użyj razem zmiennej i parametru.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach pokazano scenariusze dotyczące używania zmiennych.

|Template  |Opis  |
|---------|---------|
| [Definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Pokazuje różne typy zmiennych. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [Zmienna konfiguracyjna](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstruje użycie zmiennej, która definiuje wartości konfiguracyjne. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [reguły zabezpieczeń sieciowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Konstruuje tablicę w prawidłowym formacie do przypisywania reguł zabezpieczeń do sieciowej grupy zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach zmiennych, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia zmiennych, zobacz [najlepsze rozwiązania — zmienne](template-best-practices.md#variables).
