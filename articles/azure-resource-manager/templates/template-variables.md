---
title: Zmienne w szablonach
description: Opisuje sposób definiowania zmiennych w szablonie Azure Resource Manager (szablon ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874438"
---
# <a name="variables-in-arm-template"></a>Zmienne w szablonie ARM

W tym artykule opisano sposób definiowania i używania zmiennych w szablonie Azure Resource Manager (szablon ARM). Do uproszczenia szablonu używane są zmienne. Zamiast powtarzać skomplikowane wyrażenia w całym szablonie, należy zdefiniować zmienną, która zawiera wyrażenie złożone. Następnie należy odwołać się do tej zmiennej w zależności od potrzeb w szablonie.

Menedżer zasobów rozpoznaje zmienne przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie w szablonie jest używana zmienna, usługa Resource Manager zastępuje ją rozpoznaną wartością.

## <a name="define-variable"></a>Zdefiniuj zmienną

Podczas definiowania zmiennej podaj wartość lub wyrażenie szablonu, które jest rozpoznawane jako [Typ danych](template-syntax.md#data-types). Podczas konstruowania zmiennej można użyć wartości z parametru lub innej zmiennej.

Możesz użyć [funkcji szablonu](template-functions.md) w deklaracji zmiennej, ale nie można użyć funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) . Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy zmienne są rozwiązane.

W poniższym przykładzie przedstawiono definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu do uzyskania wartości parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

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

## <a name="example-template"></a>Przykładowy szablon

Następujący szablon nie wdraża żadnych zasobów. Po prostu pokazuje pewne sposoby deklarowania zmiennych.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Zmienne konfiguracyjne

Można zdefiniować zmienne, które przechowują powiązane wartości w celu skonfigurowania środowiska. Zmienna jest definiowana jako obiekt z wartościami. Poniższy przykład pokazuje obiekt, który przechowuje wartości dla dwóch środowisk — **test** i **prod**. Podczas wdrażania należy przekazać jedną z tych wartości.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach zmiennych, zobacz [Opis struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia zmiennych, zobacz [najlepsze rozwiązania — zmienne](template-best-practices.md#variables).
* Aby zapoznać się z przykładowym szablonem przypisując reguły zabezpieczeń do sieciowej grupy zabezpieczeń, zobacz temat [reguły zabezpieczeń sieci](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
