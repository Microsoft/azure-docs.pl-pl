---
title: 'Azure Resource Manager: Tworzenie pojedynczej bazy danych'
description: Utwórz pojedynczą bazę danych w Azure SQL Database przy użyciu szablonu Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 03d598e0c0756cd304248ab19ab28a25836c57ab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054335"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database przy użyciu szablonu Azure Resource Manager
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tworzenie [pojedynczej bazy danych](single-database-overview.md) jest najszybszą i najprostszą opcją tworzenia bazy danych w Azure SQL Database. W tym przewodniku szybki start pokazano, jak utworzyć pojedynczą bazę danych przy użyciu szablonu Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Pojedyncza baza danych ma zdefiniowany zestaw zasobów obliczeniowych, pamięci, we/wy i magazynu przy użyciu jednego z dwóch [modeli zakupów](purchasing-models.md). Podczas tworzenia pojedynczej bazy danych należy również zdefiniować [serwer](logical-servers.md) , który będzie nim zarządzać, i umieścić go w [grupie zasobów platformy Azure](../../active-directory-b2c/overview.md) w określonym regionie.

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Te zasoby są zdefiniowane w szablonie:

- [**Microsoft. SQL/serwery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/serwery/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft. SQL/serwery/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft. SQL/serwery/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft. SQL/serwery/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Storage/storageAccounts/dostawcy/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

Więcej przykładów szablonów Azure SQL Database można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz opcję **Wypróbuj** z następującego bloku kodu programu PowerShell, aby otworzyć Azure Cloud Shell.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby wysłać zapytanie do bazy danych, zobacz [Query the Database](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do [następnych kroków](#next-steps), Zachowaj tę grupę zasobów, serwer i pojedynczą bazę danych. W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

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

* * *

## <a name="next-steps"></a>Następne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](firewall-create-server-level-portal-quickstart.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](connect-query-content-reference-guide.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby utworzyć pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md).
- Aby utworzyć pojedynczą bazę danych przy użyciu Azure PowerShell, zobacz [Azure PowerShell Samples](powershell-script-content-guide.md).
- Aby dowiedzieć się, jak tworzyć szablony Menedżer zasobów, zobacz [Tworzenie pierwszego szablonu](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
