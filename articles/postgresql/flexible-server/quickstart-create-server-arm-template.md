---
title: 'Szybki Start: Tworzenie usługi Azure DB dla PostgresSQL elastyczny serwer — szablon ARM'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć elastyczny serwer usługi Azure Database for PostgresSQL przy użyciu szablonu ARM.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 2/11/2021
ms.openlocfilehash: 1f6abb62086bf92be8ae2fe50abbfa5300185fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384269"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Szybki Start: Użyj szablonu ARM, aby utworzyć serwer elastyczny Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Elastyczny serwer to zarządzana usługa, która umożliwia uruchamianie i skalowanie baz danych PostgreSQL o wysokiej dostępności w chmurze oraz zarządzanie nimi. Za pomocą szablonu Azure Resource Manager (szablonu ARM) można zainicjować serwer elastyczny PostgreSQL, aby wdrożyć wiele serwerów lub wiele baz danych na serwerze.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów na koncie platformy Azure. Za pomocą funkcji zarządzania, takich jak kontrola dostępu, blokady i tagi, można zabezpieczyć oraz zorganizować zasoby po wdrożeniu. Aby dowiedzieć się więcej o szablonach Azure Resource Manager, zobacz [Template Deployment Omówienie](../../azure-resource-manager/templates/overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Serwer usługi Azure Database for PostgresSQL jest zasobem nadrzędnym co najmniej jednej bazy danych w regionie. Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych: logowania, zapory, użytkowników, ról i konfiguracji.

Utwórz _postgres-flexible-server-template.jsw_ pliku i skopiuj do niego następujący skrypt JSON.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Te zasoby są zdefiniowane w szablonie:

- Microsoft. DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz opcję **Wypróbuj** z następującego bloku kodu programu PowerShell, aby otworzyć Azure Cloud Shell.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Wykonaj następujące kroki, aby sprawdzić, czy serwer został utworzony na platformie Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **Azure Database for PostgreSQL elastyczne serwery (wersja zapoznawcza)**.
1. Na liście baza danych wybierz nowy serwer, aby wyświetlić stronę **przeglądu** , aby zarządzać serwerem.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Musisz wprowadzić nazwę nowego serwera, aby wyświetlić szczegółowe informacje o Azure Database for PostgreSQL serwerze elastycznym.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Musisz wprowadzić nazwę i grupę zasobów nowego serwera, aby wyświetlić szczegółowe informacje o Azure Database for PostgreSQL serwerze elastycznym.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do [następnych kroków](#next-steps), Zachowaj tę grupę zasobów, serwer i pojedynczą bazę danych. W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów:

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [portalu](https://portal.azure.com)wybierz grupę zasobów, którą chcesz usunąć.

1. Wybierz pozycję **Usuń grupę zasobów**.
1. Aby potwierdzić usunięcie, wpisz nazwę grupy zasobów

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu zrzutów i przywracania](../howto-migrate-using-dump-and-restore.md)
