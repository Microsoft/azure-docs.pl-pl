---
title: Przekroczono limit przydziału wdrożenia
description: Opisuje sposób rozwiązywania problemów z więcej niż 800 wdrożeniami w historii grupy zasobów.
ms.topic: troubleshooting
ms.date: 06/25/2020
ms.openlocfilehash: 1b0c3de6007964b487a13e71cd43bd984cd970f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391183"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Usuń błąd, gdy liczba wdrożeń przekracza 800

Każda grupa zasobów jest ograniczona do 800 wdrożeń w swojej historii wdrażania. W tym artykule opisano błąd pojawiający się w przypadku niepowodzenia wdrożenia, ponieważ spowodowałoby to przekroczenie dozwolonych wdrożeń 800. Aby rozwiązać ten problem, Usuń wdrożenia z historii grupy zasobów. Usunięcie wdrożenia z historii nie ma wpływu na żadne wdrożone zasoby.

> [!NOTE]
> Azure Resource Manager wkrótce rozpocznie automatyczne usuwanie wdrożeń z historii, jak zbliżasz się do limitu. Ten błąd może nadal pojawić się, jeśli wybrałeś opcję automatycznego usuwania. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania](deployment-history-deletions.md).

## <a name="symptom"></a>Objaw

Podczas wdrażania zostanie wyświetlony komunikat o błędzie informujący o tym, że bieżące wdrożenie przekroczy przydział 800 wdrożeń.

## <a name="solution"></a>Rozwiązanie

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [AZ Deployment Group Delete](/cli/azure/group/deployment) , aby usunąć wdrożenia z historii.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, użyj:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Bieżącą liczbę można uzyskać w historii wdrożenia za pomocą następującego polecenia:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) , aby usunąć wdrożenia z historii.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Aby usunąć wszystkie wdrożenia starsze niż pięć dni, użyj:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Bieżącą liczbę można uzyskać w historii wdrożenia za pomocą następującego polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Rozwiązania innych firm

Poniższe scenariusze dotyczące adresów zewnętrznych:

* [Rozwiązania Azure Logic Apps i programu PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Rozszerzenie AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
