---
title: Dane wyjściowe w szablonach
description: Opisuje sposób definiowania wartości wyjściowych w pliku szablonu Azure Resource Manager (szablon ARM) i Bicep.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 0371a5293b302a2eb0febb010fc16caa8355eb18
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653802"
---
# <a name="outputs-in-arm-templates"></a>Dane wyjściowe w szablonach ARM

W tym artykule opisano sposób definiowania wartości wyjściowych w szablonie Azure Resource Manager (szablon ARM) i pliku Bicep. Dane wyjściowe są używane, gdy trzeba zwrócić wartości ze wdrożonych zasobów.

Format każdej wartości wyjściowej musi być rozpoznawany jako jeden z [typów danych](template-syntax.md#data-types).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definiowanie wartości wyjściowych

Poniższy przykład pokazuje, jak zwrócić Właściwość ze wdrożonego zasobu.

# <a name="json"></a>[JSON](#tab/json)

Dla elementu JSON Dodaj `outputs` sekcję do szablonu. Wartość wyjściowa pobiera w pełni kwalifikowaną nazwę domeny dla publicznego adresu IP.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Jeśli musisz wyprowadzić właściwość, która zawiera Łącznik w nazwie, użyj nawiasów wokół nazwy zamiast notacji kropkowej. Na przykład użyj  `['property-name']` zamiast `.property-name` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Dla Bicep Użyj `output` słowa kluczowego.

W poniższym przykładzie `publicIP` jest to Nazwa symboliczna publicznego adresu IP wdrożonego w pliku Bicep. Wartość wyjściowa pobiera w pełni kwalifikowaną nazwę domeny dla publicznego adresu IP.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Jeśli musisz wyprowadzić właściwość, która zawiera Łącznik w nazwie, użyj nawiasów wokół nazwy zamiast notacji kropkowej. Na przykład użyj  `['property-name']` zamiast `.property-name` .

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Dane wyjściowe warunkowe

Można warunkowo zwrócić wartość. Zwykle dane wyjściowe warunkowe są używane podczas [warunkowego wdrożenia](conditional-resource-deployment.md) zasobu. Poniższy przykład pokazuje, jak warunkowo zwrócić identyfikator zasobu dla publicznego adresu IP na podstawie tego, czy został wdrożony nowy:

# <a name="json"></a>[JSON](#tab/json)

W formacie JSON Dodaj `condition` element, aby określić, czy dane wyjściowe są zwracane.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Dane wyjściowe warunkowe nie są obecnie dostępne dla Bicep.

Można jednak użyć `?` operatora, aby zwrócić jedną z dwóch wartości w zależności od warunku.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Aby uzyskać prosty przykład danych wyjściowych warunkowych, zobacz [warunkowy szablon danych wyjściowych](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamiczna liczba danych wyjściowych

W niektórych scenariuszach nie wiadomo, ile wystąpień wartości należy zwrócić podczas tworzenia szablonu. Można zwrócić zmienną liczbę wartości za pomocą iteracji iteracyjnej.

# <a name="json"></a>[JSON](#tab/json)

W formacie JSON Dodaj `copy` element, aby wykonać iterację danych wyjściowych.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Iteracyjne dane wyjściowe nie są obecnie dostępne dla Bicep.

---

Aby uzyskać więcej informacji, zobacz [iteracja danych wyjściowych w szablonach ARM](copy-outputs.md).

## <a name="linked-templates"></a>Połączone szablony

W szablonach JSON można wdrożyć powiązane szablony przy użyciu [połączonych szablonów](linked-templates.md). Aby pobrać wartość wyjściową z połączonego szablonu, użyj funkcji [Reference](template-functions-resource.md#reference) w szablonie nadrzędnym. Składnia w szablonie nadrzędnym jest następująca:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Poniższy przykład pokazuje, jak ustawić adres IP dla modułu równoważenia obciążenia przez pobranie wartości z połączonego szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Jeśli nazwa właściwości ma łącznik, użyj nawiasów wokół nazwy zamiast notacji kropkowej.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Nie można użyć `reference` funkcji w sekcji Output [szablonu zagnieżdżonego](linked-templates.md#nested-template). Aby zwrócić wartości wdrożonego zasobu w zagnieżdżonym szablonie, przekonwertuj zagnieżdżony szablon na połączony szablon.

[Szablon publicznego adresu IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) tworzy publiczny adres IP i wyświetla identyfikator zasobu. [Szablon modułu równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) łączy się z poprzednim szablonem. Używa identyfikatora zasobu w danych wyjściowych podczas tworzenia modułu równoważenia obciążenia.

## <a name="modules"></a>Moduły

W plikach Bicep można wdrożyć powiązane szablony przy użyciu modułów. Aby pobrać wartość wyjściową z modułu, należy użyć następującej składni:

```bicep
<module-name>.outputs.<property-name>
```

Poniższy przykład pokazuje, jak ustawić adres IP dla modułu równoważenia obciążenia przez pobranie wartości z modułu. Nazwa modułu to `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Przykładowy szablon

Następujący szablon nie wdraża żadnych zasobów. Pokazuje kilka sposobów zwracania danych wyjściowych różnych typów.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep obecnie nie obsługuje pętli.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Pobierz wartości wyjściowe

Po pomyślnym wdrożeniu wartości wyjściowe są automatycznie zwracane w wynikach wdrożenia.

Aby uzyskać wartości wyjściowe z historii wdrożenia, można użyć skryptu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach danych wyjściowych, zobacz [Opis struktury i składni szablonów usługi ARM](template-syntax.md).
