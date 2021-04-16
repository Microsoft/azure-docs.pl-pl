---
title: Tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure Resource Manager szablonu
description: Dowiedz się, jak utworzyć pulę Azure Synapse Analytics SQL przy użyciu Azure Resource Manager szablonu.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.author: jrasnick
ms.date: 06/09/2020
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 73f4baf1e48b5495d513fbabe66763538c8cca57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568475"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Szybki start: tworzenie dedykowanej Azure Synapse Analytics SQL (dawniej SQL DW) przy użyciu szablonu usługi ARM

Ten Azure Resource Manager usługi (arm) utworzy dedykowaną pulę SQL (wcześniej SQL DW) z włączoną Transparent Data Encryption usługą . Dedykowana pula SQL (wcześniej SQL DW) odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Szablon definiuje jeden zasób:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon tworzy dedykowaną pulę SQL (dawniej SQL DW).
   
   [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Wprowadź lub zaktualizuj następujące wartości:

   * **Subskrypcja:** wybierz subskrypcję platformy Azure.
   * **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK.** Nowa grupa zasobów ułatwi oczyszczanie zasobów.
   * **Region:** Wybierz region.  Na przykład **Środkowe stany USA**.
   * **SQL Server:** zaakceptuj nazwę domyślną lub wprowadź nazwę SQL Server nazwę.
   * **Identyfikator logowania administratora SQL:** wprowadź nazwę użytkownika administratora SQL Server.
   * **Hasło administratora SQL:** wprowadź hasło administratora SQL Server.
   * **Data Warehouse nazwa:** wprowadź dedykowaną nazwę puli SQL.
   * **Transparent Data Encryption:** zaakceptuj ustawienie domyślne włączone. 
   * **Cel poziomu usługi:** zaakceptuj wartość domyślną DW400c.
   * **Lokalizacja:** zaakceptuj domyślną lokalizację grupy zasobów.
   * **Przejrzyj i utwórz:** Wybierz.
   * **Utwórz:** Wybierz.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Możesz użyć polecenia Azure Portal, aby sprawdzić wdrożone zasoby, albo użyć interfejsu wiersza polecenia platformy Azure Azure PowerShell skryptu, aby wyświetlić listę wdrożonych zasobów.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

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

W tym przewodniku Szybki start utworzono dedykowaną pulę SQL (dawniej SQL DW) przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o Azure Synapse Analytics i Azure Resource Manager, zobacz poniższe artykuły.

- Przeczytaj omówienie [Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Tworzenie i wdrażanie pierwszego szablonu usługi ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
