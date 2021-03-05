---
title: Zmienne w szablonach
description: Opisuje sposób definiowania zmiennych w szablonie Azure Resource Manager (szablon ARM) i pliku Bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 3ab14c9acfcc2d6c9edd23fb3bc4d876cd5ac756
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123381"
---
# <a name="variables-in-arm-templates"></a>Zmienne w szablonach ARM

W tym artykule opisano sposób definiowania i używania zmiennych w szablonie Azure Resource Manager (szablon ARM) lub pliku Bicep. Do uproszczenia szablonu używane są zmienne. Zamiast powtarzać skomplikowane wyrażenia w całym szablonie, należy zdefiniować zmienną, która zawiera wyrażenie złożone. Następnie użyj tej zmiennej w miarę potrzeb w całym szablonie.

Menedżer zasobów rozpoznaje zmienne przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie w szablonie jest używana zmienna, usługa Resource Manager zastępuje ją rozpoznaną wartością.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Zdefiniuj zmienną

Podczas definiowania zmiennej nie jest określany [Typ danych](data-types.md) dla zmiennej. Zamiast tego podaj wartość lub wyrażenie szablonu. Typ zmiennej jest wywnioskowany na podstawie rozwiązanej wartości. Poniższy przykład ustawia zmienną na ciąg.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

Podczas konstruowania zmiennej można użyć wartości z parametru lub innej zmiennej.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

Możesz użyć [funkcji szablonu](template-functions.md) do skonstruowania wartości zmiennej.

Poniższy przykład tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu do uzyskania wartości parametru i łączy ją z unikatowym ciągiem.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

W szablonach JSON nie można używać funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) w deklaracji zmiennej. Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy zmienne są rozwiązane.

W plikach Bicep funkcje odwołania i listy są prawidłowe podczas deklarowania zmiennej.

## <a name="use-variable"></a>Użyj zmiennej

Poniższy przykład pokazuje, jak używać zmiennej dla właściwości zasobu.

# <a name="json"></a>[JSON](#tab/json)

W szablonie JSON należy odwołać się do wartości zmiennej przy użyciu funkcji [zmienne](template-functions-deployment.md#variables) .

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

W pliku Bicep należy odwołać się do wartości zmiennej, podając nazwę zmiennej.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Przykładowy szablon

Następujący szablon nie wdraża żadnych zasobów. Przedstawia on niektóre sposoby deklarowania zmiennych różnych typów.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep obecnie nie obsługuje pętli.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Zmienne konfiguracyjne

Można zdefiniować zmienne, które przechowują powiązane wartości w celu skonfigurowania środowiska. Zmienna jest definiowana jako obiekt z wartościami. Poniższy przykład pokazuje obiekt, który przechowuje wartości dla dwóch środowisk — **test** i **prod**. Podczas wdrażania Przekaż jedną z tych wartości.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach zmiennych, zobacz [Opis struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia zmiennych, zobacz [najlepsze rozwiązania — zmienne](template-best-practices.md#variables).
* Aby zapoznać się z przykładowym szablonem przypisując reguły zabezpieczeń do sieciowej grupy zabezpieczeń, zobacz temat [reguły zabezpieczeń sieci](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
