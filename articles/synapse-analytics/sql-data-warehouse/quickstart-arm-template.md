---
title: Tworzenie dedykowanej puli SQL (dawniej SQL DW) przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak utworzyć pulę SQL usługi Azure Synapse Analytics przy użyciu szablonu Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460737"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Szybki Start: Tworzenie dedykowanej puli SQL usługi Azure Synapse Analytics (dawniej SQL DW) przy użyciu szablonu usługi ARM

Ten szablon Azure Resource Manager (szablon ARM) utworzy dedykowaną pulę SQL (wcześniej SQL DW) z włączonym Transparent Data Encryption. Dedykowana Pula SQL (dawniej SQL DW) odnosi się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Szablon definiuje jeden zasób:

- [Microsoft. SQL/serwery](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon służy do tworzenia dedykowanej puli SQL (dawniej SQL DW).
   
   [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Wprowadź lub zaktualizuj następujące wartości:

   * **Subskrypcja**: wybierz subskrypcję platformy Azure.
   * **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź unikatową nazwę grupy zasobów, a następnie wybierz **przycisk OK**. Nowa grupa zasobów ułatwi czyszczenie zasobów.
   * **Region**: Wybierz region.  Na przykład **Środkowe stany USA**.
   * **Nazwa SQL Server**: Zaakceptuj nazwę domyślną lub wprowadź nazwę nazwy SQL Server.
   * **Logowanie administratora SQL**: Wprowadź nazwę użytkownika administratora dla SQL Server.
   * **Hasło administratora SQL**: wprowadź hasło administratora dla SQL Server.
   * **Nazwa magazynu danych**: Wprowadź nazwę dedykowanej puli SQL.
   * **Transparent Data Encryption**: Zaakceptuj wartość domyślną, włączono. 
   * **Cel poziomu usługi**: Zaakceptuj wartość domyślną, DW400c.
   * **Lokalizacja**: zaakceptuj domyślną lokalizację grupy zasobów.
   * **Przejrzyj i Utwórz**: Wybierz.
   * **Utwórz**: Wybierz.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal do sprawdzenia wdrożonych zasobów lub użyć interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić listę wdrożonych zasobów.

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

W tym przewodniku szybki start utworzono dedykowaną pulę SQL (dawniej SQL DW) przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o usłudze Azure Synapse Analytics i Azure Resource Manager, zobacz artykuły poniżej.

- Zapoznaj się [z omówieniem usługi Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Tworzenie i wdrażanie pierwszego szablonu ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
