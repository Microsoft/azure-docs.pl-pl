---
title: Utwórz wystąpienie elementu DMS (Azure Resource Manager Template)
description: Dowiedz się, jak utworzyć Database Migration Service przy użyciu szablonu Azure Resource Manager.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: 9e0d537a948e960c90737cc3f367940a0ffca74c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852522"
---
# <a name="create-instance-of-azure-database-migration-service-azure-resource-manager-template"></a>Utwórz wystąpienie Azure Database Migration Service (szablon Azure Resource Manager)

Ten szablon Azure Resource Manager służy do wdrażania wystąpienia Azure Database Migration Service. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Szablon Azure Database Migration Service ARM wymaga następujących czynności: 

- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) i/lub [programu PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7). 
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json" highlight="33-75":::

Trzy zasoby platformy Azure są zdefiniowane w szablonie: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): tworzy sieć wirtualną. 
- [Microsoft. Network/virtualNetworks/podsieci](/azure/templates/microsoft.network/virtualnetworks/subnets): tworzy podsieć. 
- [Microsoft. datamigration/Services](/azure/templates/microsoft.datamigration/services): wdraża wystąpienie Azure Database Migration Service. 

Więcej szablonów usług Azure Database Migration Services można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration).


## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy wystąpienie Azure Database Migration Service. 

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. 
    * **Region**: lokalizacja, w której zostaną wdrożone zasoby.
    * **Nazwa usługi**: Nazwa nowej usługi migracji.
    * **Lokalizacja**: Lokalizacja grupy zasobów, pozostaw wartość domyślną `[resourceGroup().location]` .
    * **Nazwa**sieci wirtualnej: Nazwa nowej nazwy.
    * **Nazwa podsieci**: Nazwa nowej podsieci skojarzonej z siecią wirtualną.



3. Wybierz pozycję **Przegląd + utwórz**. Po pomyślnym wdrożeniu wystąpienia Azure Database Migration Service otrzymasz powiadomienie. 


Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby sprawdzić wdrożone zasoby. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie jest już konieczna, usuń ją za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Aby poznać inne sposoby wdrażania Azure Database Migration Service, zobacz: 
- [Azure Portal](quickstart-create-data-migration-service-portal.md)

Aby dowiedzieć się więcej, zobacz [omówienie Azure Database Migration Service](dms-overview.md)