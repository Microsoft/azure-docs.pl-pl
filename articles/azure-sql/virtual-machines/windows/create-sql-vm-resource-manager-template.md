---
title: Tworzenie maszyny wirtualnej SQL Server przy użyciu szablonu ARM
description: Dowiedz się, jak utworzyć SQL Server na maszynie wirtualnej platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359646"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Szybki Start: Tworzenie maszyny wirtualnej SQL Server przy użyciu szablonu ARM

Ten szablon Azure Resource Manager (szablon ARM) służy do wdrażania SQL Server na maszynie wirtualnej platformy Azure. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Szablon SQL Server VM ARM wymaga następujących czynności:

- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i/lub [programu PowerShell](/powershell/scripting/install/installing-powershell). 
- Wstępnie skonfigurowana [Grupa zasobów](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) z przygotowaną [siecią wirtualną](../../../virtual-network/quick-create-portal.md) i [podsiecią](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

W szablonie zdefiniowano pięć zasobów platformy Azure: 

- [Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses): tworzy publiczny adres IP. 
- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): tworzy sieciową grupę zabezpieczeń. 
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): konfiguruje interfejs sieciowy. 
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): tworzy maszynę wirtualną na platformie Azure. 
- [Microsoft. SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): rejestruje maszynę wirtualną przy użyciu rozszerzenia SQL IaaS Agent. 

Więcej SQL Server na temat szablonów maszyn wirtualnych platformy Azure można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy maszynę wirtualną z zainstalowaną wersją zamierzoną SQL Server i zarejestrowano ją przy użyciu rozszerzenia agenta SQL IaaS. 

   [![Wdrażanie na platformie Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: przygotowana Grupa zasobów dla maszyny wirtualnej SQL Server. 
    * **Region**: Wybierz region.  Na przykład **Środkowe stany USA**.
    * **Nazwa maszyny wirtualnej**: Wprowadź nazwę maszyny wirtualnej SQL Server. 
    * **Rozmiar maszyny wirtualnej**: wybierz odpowiedni rozmiar maszyny wirtualnej z listy rozwijanej.
    * **Istniejąca nazwa Virtual Network**: Wprowadź nazwę przygotowanej sieci wirtualnej dla maszyny wirtualnej SQL Server. 
    * **Istniejąca Grupa zasobów sieci wirtualnej**: wprowadź grupę zasobów, w której została przygotowana Sieć wirtualna. 
    * **Istniejąca nazwa podsieci**: Nazwa przygotowanej podsieci. 
    * **Oferta obrazu**: wybierz obraz SQL Server i systemu Windows Server, który najlepiej odpowiada potrzebom biznesowym. 
    * **Jednostka SKU SQL**: wybierz wersję SQL Server SKU, która najlepiej odpowiada potrzebom biznesowym. 
    * **Nazwa użytkownika administratora**: Nazwa użytkownika administratora maszyny wirtualnej. 
    * **Hasło administratora**: hasło używane przez konto administratora maszyny wirtualnej. 
    * **Typ obciążenia magazynu**: typ magazynu dla obciążenia, które najlepiej pasuje do Twojej firmy. 
    * **Liczba dysków z danymi SQL**: liczba dysków SQL Server używanych dla plików danych.  
    * **Ścieżka danych**: ścieżka do plików danych SQL Server. 
    * **Liczba dysków dziennika SQL**: liczba dysków SQL Server używanych dla plików dziennika. 
    * **Ścieżka dziennika**: ścieżka do SQL Server plików dziennika. 
    * **Lokalizacja**: lokalizacja dla wszystkich zasobów, ta wartość powinna pozostać wartością domyślną `[resourceGroup().location]` . 

3. Wybierz pozycję **Przeglądanie + tworzenie**. Po pomyślnym wdrożeniu maszyny wirtualnej SQL Server otrzymujesz powiadomienie.

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również używać Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../../../azure-resource-manager/templates/deploy-powershell.md).

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
> [ Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Aby poznać inne sposoby wdrażania SQL Server maszyny wirtualnej, zobacz: 
- [Witryna Azure Portal](create-sql-vm-portal.md)
- [Program PowerShell](create-sql-vm-powershell.md)

Aby dowiedzieć się więcej, zobacz [omówienie SQL Server na maszynach wirtualnych platformy Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).