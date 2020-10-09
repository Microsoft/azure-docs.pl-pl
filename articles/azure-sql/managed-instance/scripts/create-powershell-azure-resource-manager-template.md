---
title: Tworzenie wystąpienia zarządzanego (szablon ARM & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Ten Azure PowerShell przykładowy skrypt służy do tworzenia wystąpienia zarządzanego.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079561"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego przy użyciu programu PowerShell z szablonem Azure Resource Manager

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane można utworzyć przy użyciu biblioteki Azure PowerShell i Azure Resource Manager szablonów.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga Azure PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

Polecenia Azure PowerShell mogą rozpocząć wdrażanie przy użyciu wstępnie zdefiniowanego szablonu Azure Resource Manager. W szablonie można określić następujące właściwości:

- Nazwa wystąpienia zarządzanego
- Nazwa użytkownika i hasło administratora programu SQL
- Rozmiar wystąpienia (liczba rdzeni i maksymalny rozmiar magazynu)
- Sieć wirtualna i podsieć, w których zostaną umieszczone wystąpienia
- Sortowanie na poziomie serwera dla wystąpienia (wersja zapoznawcza).

Nie można później zmienić nazwy wystąpienia, nazwy użytkownika administratora SQL, sieci wirtualnej/podsieci ani sortowania. Inne właściwości wystąpienia można zmienić.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie przyjęto założenie, że [utworzono prawidłowe środowisko sieciowe](../virtual-network-subnet-create-arm-template.md) lub [zmodyfikowano istniejącą sieć wirtualną](../vnet-existing-add-subnet.md) dla wystąpienia zarządzanego. W razie potrzeby można przygotować środowisko sieciowe przy użyciu oddzielnego [szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment). 


W przykładzie używane są polecenia cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) i [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork), dlatego należy upewnić się, że zainstalowano następujące moduły programu PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager


Zapisz następujący skrypt w pliku JSON i zanotuj lokalizację pliku: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Zaktualizuj następujący skrypt programu PowerShell przy użyciu poprawnej ścieżki pliku pliku JSON, który został zapisany wcześniej, i Zmień nazwy obiektów w skrypcie:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Po zakończeniu działania skryptu można uzyskać dostęp do wystąpienia zarządzanego ze wszystkich usług platformy Azure i skonfigurowanego adresu IP.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/).

Dodatkowe przykłady skryptów programu PowerShell dla wystąpienia zarządzanego usługi Azure SQL można znaleźć w [skryptach programu PowerShell wystąpienia zarządzanego Azure SQL](../../database/powershell-script-content-guide.md).
