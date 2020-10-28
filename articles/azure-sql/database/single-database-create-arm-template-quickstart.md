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
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: 6e56d6558b3a80707a9f845740980f8ebbc87e35
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791498"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database przy użyciu szablonu ARM

Tworzenie [pojedynczej bazy danych](single-database-overview.md) jest najszybszą i najprostszą opcją tworzenia bazy danych w Azure SQL Database. W tym przewodniku szybki start pokazano, jak utworzyć pojedynczą bazę danych przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Pojedyncza baza danych ma zdefiniowany zestaw zasobów obliczeniowych, pamięci, we/wy i magazynu przy użyciu jednego z dwóch [modeli zakupów](purchasing-models.md). Podczas tworzenia pojedynczej bazy danych należy również zdefiniować [serwer](logical-servers.md) , który będzie nim zarządzać, i umieścić go w [grupie zasobów platformy Azure](../../active-directory-b2c/overview.md) w określonym regionie.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-sql-database/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Te zasoby są zdefiniowane w szablonie:

- [**Microsoft. SQL/serwery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/serwery/bazy danych**](/azure/templates/microsoft.sql/servers/databases)

Więcej przykładów szablonów Azure SQL Database można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Wybierz opcję **Wypróbuj** z następującego bloku kodu programu PowerShell, aby otworzyć Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby wysłać zapytanie do bazy danych, zobacz [Query the Database](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do [następnych kroków](#next-steps), Zachowaj tę grupę zasobów, serwer i pojedynczą bazę danych. W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

- Utwórz regułę zapory na poziomie serwera w celu nawiązania połączenia z pojedynczą bazą danych za pomocą narzędzi lokalnych lub zdalnych. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](firewall-create-server-level-portal-quickstart.md).
- Po utworzeniu reguły zapory na poziomie serwera [nawiąż połączenie z bazą danych i wykonaj zapytania](connect-query-content-reference-guide.md) przy użyciu różnych narzędzi i języków.
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%252fazure%252fsql-database%252ftoc.json)
- Aby utworzyć pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md).
- Aby utworzyć pojedynczą bazę danych przy użyciu Azure PowerShell, zobacz [Azure PowerShell Samples](powershell-script-content-guide.md).
- Aby dowiedzieć się, jak tworzyć szablony ARM, zobacz [Tworzenie pierwszego szablonu](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).