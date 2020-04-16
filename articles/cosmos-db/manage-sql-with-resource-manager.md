---
title: Tworzenie usługi Azure Cosmos DB i zarządzanie nią za pomocą szablonów Usługi Resource Manager
description: Tworzenie i konfigurowanie usługi Azure Cosmos DB dla interfejsu API SQL (Core) za pomocą szablonów usługi Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390887"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Zarządzanie zasobami interfejsu API usługi Azure Cosmos DB SQL (Core) za pomocą szablonów usługi Azure Resource Manager

W tym artykule dowiesz się, jak używać szablonów usługi Resource Manager, aby ułatwić automatyzację zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB.

W tym artykule przedstawiono tylko przykłady szablonów usługi Azure Resource Manager dla kont interfejsu API SQL. Można również znaleźć przykłady szablonów dla interfejsów API [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)i [Table.](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Tworzenie konta, bazy danych i kontenera usługi Azure Cosmos

Następujący szablon usługi Azure Resource Manager tworzy konto usługi Azure Cosmos z:

* Dwa kontenery, które współużytkują przepływność 400 żądanych jednostek na sekundę (RU/s) na poziomie bazy danych.
* Jeden kontener z dedykowaną przepustowością 400 RU/s.

Aby utworzyć zasoby usługi Azure Cosmos DB, skopiuj poniższy przykładowy szablon i wdrożyć go zgodnie z opisem za pośrednictwem [programu PowerShell](#deploy-via-powershell) lub [interfejsu wiersza polecenia platformy Azure.](#deploy-via-azure-cli)

* Opcjonalnie można odwiedzić [Galerię Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) i wdrożyć szablon z witryny Azure portal.
* Można również pobrać szablon na komputer lokalny lub utworzyć nowy szablon `--template-file` i określić ścieżkę lokalną z parametrem.

> [!IMPORTANT]
>
> * Po dodaniu lub usunięciu lokalizacji do konta usługi Azure Cosmos nie można jednocześnie modyfikować innych właściwości. Operacje te muszą być wykonywane oddzielnie.
> * Nazwy kont są ograniczone do 44 znaków, wszystkie małe litery.
> * Aby zmienić wartości przepływności, prześlij ponownie szablon ze zaktualizowanymi plikami RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Aby utworzyć kontener z kluczem partycji dużych, `"version":2` zmodyfikuj poprzedni szablon, aby uwzględnić właściwość w `partitionKey` obiekcie.

### <a name="deploy-via-powershell"></a>Wdrażanie za pośrednictwem programu PowerShell

Aby wdrożyć szablon usługi Azure Resource Manager za pomocą programu PowerShell:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć usługę Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy okno usługi Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Można wdrożyć szablon z lokalnie zainstalowaną wersją programu PowerShell zamiast usługi Azure Cloud Shell. Musisz [zainstalować moduł programu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom, `Get-Module -ListAvailable Az` aby znaleźć wymaganą wersję.

### <a name="deploy-via-azure-cli"></a>Wdrażanie za pośrednictwem interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia platformy Azure:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć usługę Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy okno usługi Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Polecenie `az cosmosdb show` pokazuje nowo utworzone konto usługi Azure Cosmos po jego aprowizowania. Można wdrożyć szablon z lokalnie zainstalowaną wersją interfejsu wiersza polecenia platformy Azure zamiast usługi Azure Cloud Shell. Aby uzyskać więcej informacji, zobacz artykuł [interfejsu wiersza polecenia platformy Azure (CLI).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Tworzenie kontenera usługi Azure Cosmos DB z funkcją po stronie serwera

Za pomocą szablonu usługi Azure Resource Manager można utworzyć kontener usługi Azure Cosmos DB z procedurą składowaną, wyzwalaczem i funkcją zdefiniowaną przez użytkownika.

Skopiuj poniższy przykładowy szablon i wyeks wydrążej go zgodnie z opisem za pomocą [programu PowerShell](#deploy-with-powershell) lub [interfejsu wiersza polecenia platformy Azure.](#deploy-with-azure-cli)

* Opcjonalnie można odwiedzić [Galerię Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) i wdrożyć szablon z witryny Azure portal.
* Można również pobrać szablon na komputer lokalny lub utworzyć nowy szablon `--template-file` i określić ścieżkę lokalną z parametrem.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Aby wdrożyć szablon usługi Azure Resource Manager za pomocą programu PowerShell:

1. **Skopiuj** skrypt.
1. Wybierz pozycję **Wypróbuj,** aby otworzyć usługę Azure Cloud Shell.
1. Kliknij prawym przyciskiem myszy okno Usługi Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Można wdrożyć szablon z lokalnie zainstalowaną wersją programu PowerShell zamiast usługi Azure Cloud Shell. Musisz [zainstalować moduł programu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom, `Get-Module -ListAvailable Az` aby znaleźć wymaganą wersję.

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia platformy Azure:

1. **Skopiuj** skrypt.
2. Wybierz pozycję **Wypróbuj,** aby otworzyć usługę Azure Cloud Shell.
3. Kliknij prawym przyciskiem myszy okno usługi Azure Cloud Shell, a następnie wybierz polecenie **Wklej**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Następne kroki

Oto kilka dodatkowych zasobów:

* [Dokumentacja usługi Azure Resource Manager](/azure/azure-resource-manager/)
* [Schemat dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Szablony szybkiego startu usługi Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Rozwiązywanie typowych błędów wdrażania usługi Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
