---
title: Samouczek — tworzenie maszyn wirtualnych z uruchomionym stosem SQL, IIS, .NET na platformie Azure
description: Z tego samouczka dowiesz się, jak zainstalować stos Azure SQL, IIS i.NET na maszynie wirtualnej z systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e67f4dfebc00897b7a4ed4d675159a848e4b2fdf
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455686"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Samouczek: instalowanie stosu SQL, IIS, .NET na maszynie wirtualnej z systemem Windows przy użyciu programu Azure PowerShell

W tym samouczku instalujemy stos SQL, IIS, .NET przy użyciu programu Azure PowerShell. Ten stos składa się z dwóch maszyn wirtualnych z systemem Windows Server 2016 — jednej z usługami IIS i platformą .NET oraz drugiej z programem SQL Server.

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej 
> * Instalacja usługi IIS i zestawu .NET Core SDK na maszynie wirtualnej
> * Tworzenie maszyny wirtualnej z programem SQL Server
> * Instalacja rozszerzenia programu SQL Server

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/powershell](https://shell.azure.com/powershell)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-an-iis-vm"></a>Tworzenie maszyny Wirtualnej iIS 

W tym przykładzie polecenie cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) w programie PowerShell w usłudze Cloud Shell jest używane w celu szybkiego utworzenia maszyny wirtualnej systemu Windows Server 2016, a następnie zainstalowania usług IIS i platformy .NET Framework. Maszyny wirtualne usług IIS i programu SQL współużytkują grupę zasobów i sieć wirtualną, dlatego tworzone są zmienne dla tych nazw.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Zainstaluj usługi IIS i platformę .NET przy użyciu niestandardowego rozszerzenia skryptu za pomocą polecenia cmdlet [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Tworzenie innej podsieci

Utwórz drugą podsieć dla maszyny wirtualnej programu SQL. Uzyskaj sieć wirtualną przy użyciu polecenia [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Utwórz konfigurację podsieci przy użyciu polecenia [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Zaktualizuj sieć wirtualną przy użyciu informacji o nowej podsieci za pomocą polecenia [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Maszyna wirtualna Azure SQL

Użyj wstępnie skonfigurowanego obrazu serwera SQL z platformy handlowej Azure, aby utworzyć maszynę wirtualną programu SQL. Należy najpierw utworzyć maszynę wirtualną, a następnie zainstalować rozszerzenie programu SQL Server na maszynie wirtualnej. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Użyj polecenia [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension), aby dodać [rozszerzenie programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) do maszyny wirtualnej SQL.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zainstalowano stos SQL&#92;IIS&#92;.NET przy użyciu programu Azure PowerShell. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej 
> * Instalacja usługi IIS i zestawu .NET Core SDK na maszynie wirtualnej
> * Tworzenie maszyny wirtualnej z programem SQL Server
> * Instalacja rozszerzenia programu SQL Server

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwer sieci Web usług IIS certyfikatami TLS/SSL.

> [!div class="nextstepaction"]
> [Bezpieczny serwer sieci Web usług IIS z certyfikatami TLS/SSL](tutorial-secure-web-server.md)

