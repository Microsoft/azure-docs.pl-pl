---
title: Szybki Start — tworzenie Azure Cosmos DB i kontenera przy użyciu szablonu Azure Resource Manager
description: Przewodnik Szybki Start przedstawiający sposób tworzenia bazy danych usługi Azure Cosmos i kontenera przy użyciu szablonu Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: f524a1e1db426b9b9dafb2fb95d77538a34b04ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605465"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Szybki Start: Tworzenie Azure Cosmos DB i kontenera przy użyciu szablonu Azure Resource Manager

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Azure Cosmos DB umożliwia szybkie tworzenie i wykonywanie zapytań dotyczących baz danych kluczy/wartości, baz danych dokumentów i baz danych grafów. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Menedżer zasobów w celu utworzenia bazy danych usługi Azure Cosmos i kontenera w tej bazie danych. Później można przechowywać dane w tym kontenerze.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne Azure Cosmos DB konto wersji próbnej

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Trzy zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft. DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Tworzenie konta Azure Cosmos.

* [Microsoft. DocumentDB/databaseAccounts/Sqldatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Tworzenie bazy danych Azure Cosmos.

* [Microsoft. DocumentDB/databaseAccounts/Sqldatabases/Containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Tworzenie kontenera usługi Azure Cosmos.

Więcej przykładów szablonów Azure Cosmos DB można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto usługi Azure Cosmos, bazę danych i kontener.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

   ![Szablon Menedżer zasobów, integracja Azure Cosmos DB, wdrażanie portalu](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    O ile nie zostanie on określony, użyj wartości domyślnych, aby utworzyć zasoby usługi Azure Cosmos.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa konta**: Wprowadź nazwę dla konta usługi Azure Cosmos. Musi być globalnie unikatowa.
    * **Lokalizacja**: wprowadź lokalizację, w której chcesz utworzyć konto usługi Azure Cosmos. Konto usługi Azure Cosmos może znajdować się w tej samej lokalizacji co grupa zasobów.
    * **Region podstawowy**: podstawowy region repliki dla konta usługi Azure Cosmos.
    * **Region pomocniczy**: pomocniczy region repliki dla konta usługi Azure Cosmos.
    * **Nazwa bazy danych**: Nazwa bazy danych usługi Azure Cosmos.
    * **Nazwa kontenera**: nazwa kontenera usługi Azure Cosmos.
    * **Przepływność**: przepływność dla kontenera, minimalna wartość przepływności to 400 ru/s.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu konta usługi Azure Cosmos otrzymasz powiadomienie:

   ![Menedżer zasobów szablon, integracja Cosmos DB, wdrażanie powiadomienia portalu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal jest używany do wdrożenia szablonu. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

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

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, możesz pozostawić te zasoby na miejscu.
Gdy grupa zasobów nie będzie już konieczna, usuń ją, usuwając konto usługi Azure Cosmos i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto usługi Azure Cosmos, bazę danych i kontener przy użyciu szablonu Azure Resource Manager i zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Azure Cosmos DB i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Cosmos DB](introduction.md)
- Dowiedz się więcej o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Pobierz inne [szablony Menedżer zasobów Azure Cosmos DB](resource-manager-samples.md)