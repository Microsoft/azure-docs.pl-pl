---
title: Dane wyjściowe w szablonach
description: Opisuje sposób definiowania wartości wyjściowych w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79460028"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Dane wyjściowe w szablonie Azure Resource Manager

W tym artykule opisano sposób definiowania wartości wyjściowych w szablonie Azure Resource Manager. Dane wyjściowe są używane, gdy trzeba zwrócić wartości ze wdrożonych zasobów.

## <a name="define-output-values"></a>Definiowanie wartości wyjściowych

Poniższy przykład pokazuje, jak zwrócić identyfikator zasobu dla publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Dane wyjściowe warunkowe

W sekcji dane wyjściowe można warunkowo zwrócić wartość. Zazwyczaj warunek jest używany w danych wyjściowych, gdy [warunkowo wdrożono](conditional-resource-deployment.md) zasób. Poniższy przykład pokazuje, jak warunkowo zwrócić identyfikator zasobu dla publicznego adresu IP na podstawie tego, czy został wdrożony nowy:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Aby uzyskać prosty przykład danych wyjściowych warunkowych, zobacz [warunkowy szablon danych wyjściowych](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamiczna liczba danych wyjściowych

W niektórych scenariuszach nie wiadomo, ile wystąpień wartości należy zwrócić podczas tworzenia szablonu. Można zwrócić zmienną liczbę wartości za pomocą elementu **copy** .

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

Aby uzyskać więcej informacji, zobacz [iteracja danych wyjściowych w szablonach Azure Resource Manager](copy-outputs.md).

## <a name="linked-templates"></a>Połączone szablony

Aby pobrać wartość wyjściową z połączonego szablonu, użyj funkcji [Reference](template-functions-resource.md#reference) w szablonie nadrzędnym. Składnia w szablonie nadrzędnym jest następująca:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Podczas pobierania właściwości wyjściowej z połączonego szablonu, nazwa właściwości nie może zawierać kreski.

Poniższy przykład pokazuje, jak ustawić adres IP dla modułu równoważenia obciążenia przez pobranie wartości z połączonego szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` funkcji w sekcji Output [szablonu zagnieżdżonego](linked-templates.md#nested-template). Aby zwrócić wartości wdrożonego zasobu w zagnieżdżonym szablonie, przekonwertuj zagnieżdżony szablon na połączony szablon.

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

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono scenariusze używania danych wyjściowych.

|Szablon  |Opis  |
|---------|---------|
|[Kopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy złożone zmienne i wyprowadza te wartości. Nie wdraża żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP i wyprowadza identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Linki do poprzedniego szablonu. Używa identyfikatora zasobu w danych wyjściowych podczas tworzenia modułu równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach danych wyjściowych, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
