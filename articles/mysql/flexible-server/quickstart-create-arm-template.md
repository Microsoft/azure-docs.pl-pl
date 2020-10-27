---
title: 'Szybki Start: Tworzenie szablonu Azure DB for MySQL — elastyczny szablon serwera — ARM'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć serwer Azure Database for MySQL-elastyczny przy użyciu szablonu usługi ARM.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 3f32d3d7cc498126d0fbdb709aaf0424d335793f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534128"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Szybki Start: Użyj szablonu ARM, aby utworzyć serwer Azure Database for MySQL-elastyczny (wersja zapoznawcza)

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza) to usługa zarządzana, która umożliwia uruchamianie i skalowanie baz danych MySQL o wysokiej dostępności w chmurze oraz zarządzanie nimi. Szablonu Azure Resource Manager (szablonu ARM) można użyć do aprowizacji elastycznego serwera do wdrożenia wielu serwerów lub wielu baz danych na serwerze.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Azure Database for MySQL elastyczny serwer jest zasobem nadrzędnym dla co najmniej jednej bazy danych w regionie. Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych: logowania, zapory, użytkowników, ról, konfiguracji.

Utwórz _mysql-flexible-server-template.jsw_ pliku i skopiuj do niego ten skrypt JSON.

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
    "vCores": {
      "type": "Int"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "standbyCount": {
      "type": "Int"
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
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"vnetId\": \"\", \"vnetName\": \"\", \"vnetResourceGroup\": \"\", \"subnetName\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"DelegatedVnetID\": \"', variables('vnetDataSet').vnetId, '\", \"DelegatedVnetName\": \"', variables('vnetDataSet').vnetName, '\", \"DelegatedVnetResourceGroup\": \"', variables('vnetDataSet').vnetResourceGroup, '\", \"DelegatedSubnetName\": \"', variables('vnetDataSet').subnetName, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]",
        "capacity": "[parameters('vCores')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "VnetInjArgs": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "standbyCount": "[parameters('standbyCount')]",
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
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
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

- Microsoft. DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Wybierz opcję **Wypróbuj** z następującego bloku kodu programu PowerShell, aby otworzyć [Azure Cloud Shell](../../cloud-shell/overview.md).

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Wykonaj następujące kroki, aby sprawdzić, czy serwer został utworzony na platformie Azure.

### <a name="azure-portal"></a>Azure Portal

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **serwery Azure Database for MySQL** .
1. Na liście baza danych wybierz nowy serwer. Zostanie wyświetlona strona **Przegląd** dla nowego serwera Azure Database for MySQL.

### <a name="powershell"></a>PowerShell

Musisz wprowadzić nazwę nowego serwera, aby wyświetlić szczegółowe informacje o Azure Database for MySQL serwerze elastycznym.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Interfejs wiersza polecenia

Musisz wprowadzić nazwę i grupę zasobów nowego serwera, aby wyświetlić szczegółowe informacje o Azure Database for MySQL serwerze elastycznym.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz przejść do [następnych kroków](#next-steps), Zachowaj tę grupę zasobów, serwer i pojedynczą bazę danych. W następnych krokach pokazano, jak różnymi metodami łączyć się z bazą danych i wykonywać w niej zapytania.

Aby usunąć grupę zasobów:

### <a name="azure-portal"></a>Azure Portal

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów** .
1. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
1. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów** .
1. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń** .

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Interfejs wiersza polecenia

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu usługi ARM, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Aby zapoznać się z samouczkiem krok po kroku w celu utworzenia aplikacji z App Service przy użyciu programu MySQL, zobacz:

> [!div class="nextstepaction"]
>[Tworzenie aplikacji sieci Web PHP (platformy laravel) za pomocą programu MySQL](tutorial-php-database-app.md)
