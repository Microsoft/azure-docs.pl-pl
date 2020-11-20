---
title: Utwórz wystąpienie elementu DMS (Azure Resource Manager Template)
description: Dowiedz się, jak utworzyć Database Migration Service przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963219"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Szybki Start: Tworzenie wystąpienia Azure Database Migration Service przy użyciu szablonu ARM

Ten szablon Azure Resource Manager (szablon ARM) służy do wdrażania wystąpienia Azure Database Migration Service. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Szablon Azure Database Migration Service ARM wymaga następujących czynności: 

- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i/lub [programu PowerShell](/powershell/scripting/install/installing-powershell). 
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/).

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

Trzy zasoby platformy Azure są zdefiniowane w szablonie: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): tworzy sieć wirtualną. 
- [Microsoft. Network/virtualNetworks/podsieci](/azure/templates/microsoft.network/virtualnetworks/subnets): tworzy podsieć. 
- [Microsoft. datamigration/Services](/azure/templates/microsoft.datamigration/services): wdraża wystąpienie Azure Database Migration Service. 

Więcej szablonów usług Azure Database Migration Services można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy wystąpienie Azure Database Migration Service. 

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. 
    * **Region**: lokalizacja, w której zostaną wdrożone zasoby.
    * **Nazwa usługi**: Nazwa nowej usługi migracji.
    * **Lokalizacja**: Lokalizacja grupy zasobów, pozostaw wartość domyślną `[resourceGroup().location]` .
    * **Nazwa** sieci wirtualnej: Nazwa nowej nazwy.
    * **Nazwa podsieci**: Nazwa nowej podsieci skojarzonej z siecią wirtualną.



3. Wybierz pozycję **Przejrzyj i utwórz**. Po pomyślnym wdrożeniu wystąpienia Azure Database Migration Service otrzymasz powiadomienie. 


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
> [ Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Aby poznać inne sposoby wdrażania Azure Database Migration Service, zobacz: 
- [Witryna Azure Portal](quickstart-create-data-migration-service-portal.md)

Aby dowiedzieć się więcej, zobacz [omówienie Azure Database Migration Service](dms-overview.md)