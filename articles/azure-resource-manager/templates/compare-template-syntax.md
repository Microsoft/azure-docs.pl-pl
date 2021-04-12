---
title: Porównanie składni Azure Resource Manager szablonów w formacie JSON i Bicep
description: Porównuje Azure Resource Manager szablony opracowane za pomocą notacji JSON i Bicep oraz pokazują, jak konwertować między językami.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461798"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Porównanie JSON i Bicep dla szablonów

W tym artykule porównano składnię Bicep z składnią JSON dla szablonów Azure Resource Manager (szablony ARM). W większości przypadków Bicep zawiera składnię, która jest mniej pełna niż odpowiednik w formacie JSON.

Jeśli masz doświadczenie w tworzeniu szablonów ARM przy użyciu formatu JSON, Skorzystaj z poniższych przykładów, aby dowiedzieć się więcej na temat odpowiedniej składni Bicep.

## <a name="expressions"></a>Wyrażenia

Aby utworzyć wyrażenie:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parametry

Aby zadeklarować parametr z wartością domyślną:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Aby uzyskać wartość parametru:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Zmienne

Aby zadeklarować zmienną:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Aby uzyskać wartość zmiennej:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Ciągi

Aby połączyć ciągi:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Operatory logiczne

Aby zwrócić wartość logiczną **i**:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Aby ustawić warunkowo wartość:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Zakres wdrożenia

Aby ustawić docelowy zakres wdrożenia:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Zasoby

Aby zadeklarować zasób:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Aby warunkowo wdrożyć zasób:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Aby ustawić właściwość zasobu:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Aby uzyskać identyfikator zasobu w szablonie:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Pętle

Aby wykonać iterację elementów w tablicy lub liczbie:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Zależności zasobów

Aby ustawić zależność między zasobami:

W przypadku usługi Bicep należy polegać na automatycznym wykryciu zależności lub ręcznie ustawić zależność.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Zasoby referencyjne

Aby uzyskać właściwość z zasobu w szablonie:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Aby uzyskać właściwość z istniejącego zasobu, który nie został wdrożony w szablonie:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Dane wyjściowe

Aby wyprowadzić właściwość z zasobu w szablonie:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Ponowne użycie kodu

Aby rozdzielić rozwiązanie na wiele plików:

* W przypadku Bicep Użyj [modułów](bicep-tutorial-add-modules.md).
* W przypadku formatu JSON Użyj [połączonych szablonów](linked-templates.md).

## <a name="recommendations"></a>Zalecenia

* Jeśli to możliwe, Unikaj używania funkcji [Reference](template-functions-resource.md#reference) i [ResourceID](template-functions-resource.md#resourceid) w pliku Bicep. W przypadku odwoływania się do zasobu w tym samym wdrożeniu Bicep zamiast tego użyj identyfikatora zasobu. Na przykład jeśli w pliku Bicep został wdrożony zasób z `stg` identyfikatorem zasobu, użyj składni podobnej do lub, `stg.id` `stg.properties.primaryEndpoints.blob` Aby uzyskać wartości właściwości. Korzystając z identyfikatora zasobu, można utworzyć niejawną zależność między zasobami. Nie musisz jawnie ustawiać zależności przy użyciu właściwości dependsOn.
* Jeśli zasób nie jest wdrożony w pliku Bicep, można nadal uzyskać odwołanie symboliczne do zasobu przy użyciu **istniejącego** słowa kluczowego.
* Użyj spójnej wielkości liter dla identyfikatorów. Jeśli nie masz pewności, jakiego typu użyta jest wielkość liter, wypróbuj notacji CamelCase wielkości liter. Na przykład `param myCamelCasedParameter string`.
* Dodaj opis do parametru tylko wtedy, gdy opis zawiera podstawowe informacje dla użytkowników. `//`Aby uzyskać pewne informacje, można użyć komentarzy.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat Bicep, zobacz [samouczek Bicep](./bicep-tutorial-create-first-bicep.md).
* Aby dowiedzieć się więcej o konwertowaniu szablonów między językami, zobacz [konwertowanie szablonów ARM między elementami JSON i Bicep](bicep-decompile.md).
