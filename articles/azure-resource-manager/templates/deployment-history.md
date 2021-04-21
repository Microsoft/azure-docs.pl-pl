---
title: Historia wdrożenia
description: Opisuje sposób wyświetlania operacji Azure Resource Manager za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781869"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetlanie historii wdrażania za pomocą Azure Resource Manager

Azure Resource Manager umożliwia wyświetlenie historii wdrażania. Można sprawdzić konkretne operacje w poprzednich wdrożeniach i zobaczyć, które zasoby zostały wdrożone. Ta historia zawiera informacje o wszelkich błędach.

Historia wdrożenia dla grupy zasobów jest ograniczona do 800 wdrożeń. Gdy zbliżasz się do tego limitu, wdrożenia są automatycznie usuwane z historii. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania.](deployment-history-deletions.md)

Aby uzyskać pomoc w rozwiązywaniu problemów z określonymi błędami wdrażania, zobacz Rozwiązywanie typowych błędów [podczas wdrażania](common-deployment-errors.md)zasobów na platformie Azure za pomocą Azure Resource Manager .

## <a name="get-deployments-and-correlation-id"></a>Uzyskiwanie wdrożeń i identyfikatora korelacji

Szczegółowe informacje o wdrożeniu można wyświetlić za pomocą interfejsu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Każde wdrożenie ma identyfikator korelacji, który jest używany do śledzenia powiązanych zdarzeń. Jeśli [utworzysz wniosek o pomoc techniczną platformy Azure,](../../azure-portal/supportability/how-to-create-azure-support-request.md)pomoc techniczna może poprosić o identyfikator korelacji. Obsługa używa identyfikatora korelacji do identyfikowania operacji dla wdrożenia, które zakończyło się niepowodzeniem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz grupę zasobów, którą chcesz zbadać.

1. Wybierz link w obszarze **Wdrożenia.**

   ![Wybieranie historii wdrażania](./media/deployment-history/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrożenia.

   ![Wybieranie wdrożenia](./media/deployment-history/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia, w tym identyfikator korelacji.

    ![Podsumowanie wdrożenia](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić listę wszystkich wdrożeń dla grupy zasobów, użyj polecenia [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Aby uzyskać konkretne wdrożenie z grupy zasobów, dodaj **parametr DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wdrożeń dla grupy zasobów, użyj [az deployment group list](/cli/azure/group/deployment#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Aby uzyskać konkretne wdrożenie, użyj [az deployment group show](/cli/azure/group/deployment#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Aby wyświetlić listę wdrożeń dla grupy zasobów, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Deployments - List By Resource Group (Wdrożenia — lista według grupy zasobów).](/rest/api/resources/deployments/listbyresourcegroup)

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Aby uzyskać określone wdrożenie. Użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Deployments - Get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Odpowiedź zawiera identyfikator korelacji.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Uzyskiwanie operacji wdrażania i komunikat o błędzie

Każde wdrożenie może obejmować wiele operacji. Aby wyświetlić więcej szczegółów dotyczących wdrożenia, wyświetl operacje wdrażania. W przypadku niepowodzenia wdrożenia operacje wdrażania zawierają komunikat o błędzie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W podsumowaniu wdrożenia wybierz pozycję **Szczegóły operacji.**

    ![Wybieranie szczegółów operacji](./media/deployment-history/get-operation-details.png)

1. Zobaczysz szczegóły tego kroku wdrożenia. Gdy wystąpi błąd, szczegóły obejmują komunikat o błędzie.

    ![Wyświetlanie szczegółów operacji](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić operacje wdrażania wdrożenia w grupie zasobów, użyj polecenia [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Aby wyświetlić operacje, które zakończyły się niepowodzeniem, przefiltruj operacje ze **stanem Niepowodzenie.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Aby uzyskać komunikat o stanie operacji, które zakończyły się niepowodzeniem, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj [polecenia az deployment operation group list.](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) Musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Aby wyświetlić operacje, które zakończyły się niepowodzeniem, przefiltruj operacje ze **stanem Niepowodzenie.**

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Aby uzyskać komunikat o stanie operacji, które zakończyły się niepowodzeniem, użyj następującego polecenia:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Aby uzyskać operacje wdrażania, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Deployment Operations - List](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Odpowiedź zawiera komunikat o błędzie.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pomoc w rozwiązywaniu problemów z konkretnymi błędami wdrażania, zobacz [Resolve common errors when deploying resources to Azure with Azure Resource Manager](common-deployment-errors.md)(Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure przy użyciu Azure Resource Manager).
* Aby dowiedzieć się więcej na temat zarządzania wdrożeniami w historii, zobacz [Automatyczne usuwanie z historii wdrażania.](deployment-history-deletions.md)
* Aby zweryfikować wdrożenie przed jego wykonaniem, zobacz Deploy a resource group with Azure Resource Manager template (Wdrażanie [grupy zasobów za pomocą Azure Resource Manager szablonu).](deploy-powershell.md)
