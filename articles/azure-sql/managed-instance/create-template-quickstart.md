---
title: 'Azure Resource Manager: Tworzenie wystąpienia zarządzanego usługi Azure SQL'
description: Dowiedz się, jak utworzyć wystąpienie zarządzane Azure SQL przy użyciu szablonu Azure Resource Manager.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283347"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Szybki Start: Tworzenie wystąpienia zarządzanego usługi Azure SQL przy użyciu szablonu ARM

Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia wystąpienia zarządzanego i sieci wirtualnej usługi Azure SQL. [Wystąpienie zarządzane Azure SQL](sql-managed-instance-paas-overview.md) to inteligentna, w pełni zarządzana i skalowalna baza danych w chmurze, która zapewnia niemal 100% możliwości funkcji Dzięki aparatowi bazy danych SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Te zasoby są zdefiniowane w szablonie:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft. SQL/ManagedInstances**](/azure/templates/microsoft.sql/managedinstances)

Więcej przykładów szablonów można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz opcję **Wypróbuj** z następującego bloku kodu programu PowerShell, aby otworzyć Azure Cloud Shell.

> [!IMPORTANT]
> Wdrożenie wystąpienia zarządzanego jest długotrwałą operacją. Wdrożenie pierwszego wystąpienia w podsieci zwykle trwa znacznie dłużej niż wdrażanie w podsieci z istniejącymi wystąpieniami zarządzanymi. Aby uzyskać średni czas udostępniania, zobacz [operacje zarządzania wystąpieniami zarządzanymi przez program SQL Server](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Odwiedź [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i sprawdź, czy wystąpienie zarządzane znajduje się w wybranej grupie zasobów. Ze względu na to, że tworzenie wystąpienia zarządzanego może zająć trochę czasu, może być konieczne sprawdzenie linku **wdrożenia** na stronie **przeglądu** grupy zasobów.

- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym SQL z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia z maszyną wirtualną platformy Azure](connect-vm-instance-configure.md).
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak połączyć się z wystąpieniem zarządzanym SQL z lokalnego komputera klienckiego przy użyciu połączenia typu punkt-lokacja, zobacz [Konfigurowanie połączenia punkt-lokacja](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zachowaj wystąpienie zarządzane, jeśli chcesz przejść do [następnych kroków](#next-steps), ale Usuń wystąpienie zarządzane i powiązane zasoby po ukończeniu dodatkowych samouczków. Po usunięciu wystąpienia zarządzanego zapoznaj się z tematem [usuwanie podsieci po usunięciu wystąpienia zarządzanego](virtual-cluster-delete.md).


Aby usunąć grupę zasobów:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym usługi Azure SQL](connect-vm-instance-configure.md)
