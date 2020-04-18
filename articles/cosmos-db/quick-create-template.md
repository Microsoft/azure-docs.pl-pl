---
title: Szybki start — tworzenie bazy danych usługi Azure Cosmos i kontenera przy użyciu szablonu usługi Azure Resource Manager
description: Szybki start przedstawiający sposób korzystania z bazy danych usługi Azure Cosmos i kontenera przy użyciu szablonu usługi Azure Resource Manager
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: f524a1e1db426b9b9dafb2fb95d77538a34b04ec
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605465"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Szybki start: tworzenie bazy danych usługi Azure Cosmos i kontenera przy użyciu szablonu usługi Azure Resource Manager

Azure Cosmos DB to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft. Usługi Azure Cosmos DB można używać do szybkiego tworzenia i wykonywania zapytań o bazy danych klucz/wartość, bazy danych dokumentów i bazy danych wykresów. Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu Usługi Resource Manager w celu utworzenia bazy danych usługi Azure Cosmos i kontenera w tej bazie danych. Później można przechowywać dane w tym kontenerze.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne konto próbne usługi Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Trzy zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft.DocumentDB/databaseKonta:](/azure/templates/microsoft.documentdb/databaseaccounts)Utwórz konto usługi Azure Cosmos.

* [Microsoft.DocumentDB/databaseKonta/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Tworzenie bazy danych usługi Azure Cosmos.

* [Microsoft.DocumentDB/databaseKonta/sqlDatabases/containers:](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers)Tworzenie kontenera usługi Azure Cosmos.

Więcej przykładów szablonów usługi Azure Cosmos DB można znaleźć w [galerii szablonów przewodnika Szybki start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto usługi Azure Cosmos, bazy danych i kontenera.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

   ![Szablon Usługi Resource Manager, integracja z usługą Azure Cosmos DB, wdrażanie portalu](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    O ile nie zostanie określony, użyj wartości domyślnych do utworzenia zasobów usługi Azure Cosmos.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz **pozycję Utwórz nowy**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    * **Lokalizacja**: wybierz lokalizację.  Na przykład **Środkowe stany USA**.
    * **Nazwa konta:** wprowadź nazwę konta usługi Azure Cosmos. Musi być unikatowa globalnie.
    * **Lokalizacja:** wprowadź lokalizację, w której chcesz utworzyć konto usługi Azure Cosmos. Konto usługi Azure Cosmos może znajdować się w tej samej lokalizacji co grupa zasobów.
    * **Region podstawowy:** Podstawowy region repliki dla konta usługi Azure Cosmos.
    * **Region pomocniczy:** pomocniczy region repliki dla konta usługi Azure Cosmos.
    * **Nazwa bazy danych:** Nazwa bazy danych usługi Azure Cosmos.
    * **Nazwa kontenera:** Nazwa kontenera usługi Azure Cosmos.
    * **Przepływność:** Przepływność kontenera, minimalna wartość przepływności wynosi 400 RU/s.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu konta usługi Azure Cosmos otrzymasz powiadomienie:

   ![Szablon Menedżera zasobów, integracja usługi Cosmos DB, powiadomienie o wdrażaniu portalu](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Witryna Azure portal służy do wdrażania szablonu. Oprócz witryny Azure portal można również użyć interfejsu API programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Za pomocą witryny Azure Portal można sprawdzić konto usługi Azure Cosmos, bazę danych i kontener lub użyć następującego skryptu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell do wyświetlenia listy utworzonego klucza tajnego.

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

Jeśli planujesz kontynuować pracę z kolejnymi i samouczkami, możesz pozostawić te zasoby na swoim miejscu.
Gdy nie jest już potrzebne, usuń grupę zasobów, która usuwa konto usługi Azure Cosmos i powiązanych zasobów. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure Powershell:

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

W tym przewodniku Szybki start utworzono konto usługi Azure Cosmos, bazę danych i kontener przy użyciu szablonu usługi Azure Resource Manager i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o usłudze Azure Cosmos DB i usłudze Azure Resource Manager, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Cosmos DB](introduction.md)
- Dowiedz się więcej o [usłudze Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Pobierz inne [szablony usługi Azure Cosmos DB Resource Manager](resource-manager-samples.md)