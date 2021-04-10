---
title: Historia wdrożenia
description: Opisuje sposób wyświetlania Azure Resource Manager operacji wdrażania przy użyciu portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure i usługi API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 898af2365dfbb0f61b6b87e7532c9256269d799a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732775"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetlanie historii wdrożenia za pomocą Azure Resource Manager

Azure Resource Manager umożliwia wyświetlenie historii wdrażania. Można sprawdzić konkretne operacje w poprzednich wdrożeniach i zobaczyć, które zasoby zostały wdrożone. Ta historia zawiera informacje o błędach.

Historia wdrożenia dla grupy zasobów jest ograniczona do 800 wdrożeń. Gdy zbliżasz się do limitu, wdrożenia są automatycznie usuwane z historii. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania](deployment-history-deletions.md).

Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Pobierz wdrożenia i identyfikator korelacji

Szczegółowe informacje o wdrożeniu można wyświetlić za pomocą programu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. Każde wdrożenie ma identyfikator korelacji, który jest używany do śledzenia powiązanych zdarzeń. Jeśli [utworzysz żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), pomoc techniczna może prosić o identyfikator korelacji. Obsługa używa identyfikatora korelacji do identyfikowania operacji dla niepowodzenia wdrożenia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz grupę zasobów, którą chcesz przeanalizować.

1. Wybierz link w obszarze **wdrożenia**.

   ![Wybierz historię wdrożenia](./media/deployment-history/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybieranie wdrożenia](./media/deployment-history/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia, w tym identyfikator korelacji.

    ![Podsumowanie wdrożenia](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić listę wszystkich wdrożeń dla grupy zasobów, użyj polecenia [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Aby uzyskać określone wdrożenie z grupy zasobów, Dodaj parametr **deploymentname** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wdrożenia dla grupy zasobów, użyj polecenie [AZ Deployment Group list](/cli/azure/group/deployment#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Aby uzyskać określone wdrożenie, użyj polecenie [AZ Deployment Group Show](/cli/azure/group/deployment#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Aby wyświetlić listę wdrożeń dla grupy zasobów, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz  [wdrożenia — lista według grupy zasobów](/rest/api/resources/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

W celu uzyskania określonego wdrożenia. Użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Deployments-Get](/rest/api/resources/resources/deployments/get).

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

## <a name="get-deployment-operations-and-error-message"></a>Pobierz operacje wdrażania i komunikat o błędzie

Każde wdrożenie może obejmować wiele operacji. Aby wyświetlić więcej szczegółów na temat wdrożenia, Wyświetl operacje wdrażania. W przypadku niepowodzenia wdrożenia operacje wdrażania obejmują komunikat o błędzie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na stronie Podsumowanie wdrożenia wybierz pozycję **szczegóły operacji**.

    ![Wybierz szczegóły operacji](./media/deployment-history/get-operation-details.png)

1. Zobaczysz szczegóły dotyczące tego kroku wdrożenia. W przypadku wystąpienia błędu szczegóły obejmują komunikat o błędzie.

    ![Pokaż szczegóły operacji](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj polecenia [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Aby wyświetlić operacje zakończone niepowodzeniem, Filtruj operacje z **niepowodzeniem** stanu.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Aby uzyskać komunikat o stanie zakończonych niepowodzeniem operacji, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj polecenia [AZ Deployment Operation Group list](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) . Musisz mieć interfejs wiersza polecenia platformy Azure 2.6.0 lub nowszy.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Aby wyświetlić operacje zakończone niepowodzeniem, Filtruj operacje z **niepowodzeniem** stanu.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Aby uzyskać komunikat o stanie zakończonych niepowodzeniem operacji, użyj następującego polecenia:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Aby pobrać operacje wdrażania, użyj następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Operations-list](/rest/api/resources/resources/deploymentoperations/list).

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

* Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](common-deployment-errors.md).
* Aby dowiedzieć się, jak są zarządzane wdrożenia w historii, zobacz [Automatyczne usuwanie z historii wdrażania](deployment-history-deletions.md).
* Aby sprawdzić poprawność wdrożenia przed jego wykonaniem, zobacz [wdrażanie grupy zasobów przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

