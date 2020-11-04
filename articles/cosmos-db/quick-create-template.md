---
title: Szybki Start — tworzenie Azure Cosmos DB i kontenera przy użyciu szablonu Azure Resource Manager
description: Przewodnik Szybki Start przedstawiający sposób tworzenia bazy danych usługi Azure Cosmos i kontenera przy użyciu szablonu Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339669"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Szybki Start: Tworzenie Azure Cosmos DB i kontenera przy użyciu szablonu ARM
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB to Microsoft Fast NoSQL Database z otwartymi interfejsami API dla dowolnej skali. Azure Cosmos DB umożliwia szybkie tworzenie i wykonywanie zapytań dotyczących baz danych kluczy/wartości, baz danych dokumentów i baz danych grafów. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia bazy danych usługi Azure Cosmos i kontenera w tej bazie danych. Później można przechowywać dane w tym kontenerze.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne Azure Cosmos DB konto wersji próbnej

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Trzy zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Utwórz konto platformy Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/Sqldatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Tworzenie bazy danych Azure Cosmos.

* [Microsoft.DocumentDB/databaseAccounts/Sqldatabases/Containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Tworzenie kontenera usługi Azure Cosmos.

Więcej przykładów szablonów Azure Cosmos DB można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto usługi Azure Cosmos, bazę danych i kontener.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="Szablon ARM, integracja Azure Cosmos DB, wdrażanie portalu":::

    O ile nie zostanie on określony, użyj wartości domyślnych, aby utworzyć zasoby usługi Azure Cosmos.

    * **Subskrypcja** : wybierz subskrypcję platformy Azure.
    * **Grupa zasobów** : wybierz pozycję **Utwórz nową** , wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    * **Lokalizacja** : wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa konta** : Wprowadź nazwę dla konta usługi Azure Cosmos. Musi być globalnie unikatowa.
    * **Lokalizacja** : wprowadź lokalizację, w której chcesz utworzyć konto usługi Azure Cosmos. Konto usługi Azure Cosmos może znajdować się w tej samej lokalizacji co grupa zasobów.
    * **Region podstawowy** : podstawowy region repliki dla konta usługi Azure Cosmos.
    * **Region pomocniczy** : pomocniczy region repliki dla konta usługi Azure Cosmos.
    * **Domyślny poziom spójności** : domyślny poziom spójności dla konta usługi Azure Cosmos.
    * **Maksymalny prefiks nieodświeżony** : Maksymalna liczba starych żądań. Wymagane dla BoundedStaleness.
    * **Maksymalny interwał w sekundach** : maksymalny czas opóźnienia. Wymagane dla BoundedStaleness.
    * **Nazwa bazy danych** : Nazwa bazy danych usługi Azure Cosmos.
    * **Nazwa kontenera** : nazwa kontenera usługi Azure Cosmos.
    * **Przepływność** : przepływność dla kontenera, minimalna wartość przepływności to 400 ru/s.
    * **Wyrażam zgodę na powyższe warunki i postanowienia** : Zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu konta usługi Azure Cosmos otrzymasz powiadomienie:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="Szablon ARM, integracja Cosmos DB, wdrażanie powiadomienia portalu":::

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Możesz użyć Azure Portal, aby sprawdzić konto usługi Azure Cosmos, bazę danych i kontener albo użyć następującego interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby utworzyć listę utworzonego wpisu tajnego.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić te zasoby na miejscu.
Gdy grupa zasobów nie będzie już konieczna, usuń ją, usuwając konto usługi Azure Cosmos i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

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

W tym przewodniku szybki start utworzono konto usługi Azure Cosmos, bazę danych i kontener przy użyciu szablonu ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Azure Cosmos DB i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Cosmos DB](introduction.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Pobierz inne [szablony Menedżer zasobów Azure Cosmos DB](./templates-samples-sql.md)