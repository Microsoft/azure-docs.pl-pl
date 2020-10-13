---
title: Tworzenie maszyny wirtualnej i konta magazynu dla skalowalnej aplikacji na platformie Azure
description: Dowiedz się, w jaki sposób wdrożyć maszynę wirtualną, na której zostanie uruchomiona skalowalna aplikacja korzystająca z usługi Azure Blob Storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073254"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Tworzenie maszyny wirtualnej i konta magazynu dla skalowalnej aplikacji

Niniejszy samouczek jest pierwszą częścią serii. Ten samouczek przedstawia sposób wdrażania aplikacji, która przekazuje i pobiera dużą ilość danych losowych na koncie usługi Azure Storage. Po zakończeniu na maszynie wirtualnej będzie uruchomiona aplikacja konsolowa umożliwiająca przekazywanie dużej ilości danych na konto magazynu i pobieranie ich.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Tworzenie maszyny wirtualnej
> * Konfigurowanie rozszerzenia skryptu niestandardowego

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 0.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
 
Przykład użyty w tym samouczku przekazuje 50 dużych plików do kontenera obiektów blob na koncie usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw do przechowywania i umożliwiania dostępu do obiektów danych usługi Azure Storage. Utwórz konto magazynu w utworzonej grupie zasobów przy użyciu polecenia [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount).

W poniższym poleceniu w miejsce symbolu zastępczego `<blob_storage_account>` wstaw swoją własną unikatową w skali globalnej nazwę konta usługi Blob Storage.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz konfigurację maszyny wirtualnej. Ta konfiguracja zawiera ustawienia, które są używane podczas wdrażania maszyny wirtualnej, takie jak obraz maszyny wirtualnej, rozmiar i konfiguracja uwierzytelniania. Podczas wykonywania tego kroku jest wyświetlany monit o poświadczenia. Wprowadzane wartości są konfigurowane jako nazwa użytkownika i hasło dla maszyny wirtualnej.

Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Wdrażanie konfiguracji

W przypadku tego samouczka istnieją elementy wymagane wstępnie, które należy zainstalować na maszynie wirtualnej. Rozszerzenie skryptu niestandardowego jest używane do uruchomienia skryptu programu PowerShell, który wykonuje następujące zadania:

> [!div class="checklist"]
> * Instalowanie programu .NET Core 2.0
> * Instalowanie narzędzia chocolatey
> * Instalowanie oprogramowania GIT
> * Klonowanie repozytorium przykładowego
> * Przywracanie pakietów NuGet
> * Utworzenie 50 plików o rozmiarze 1 GB z danymi losowymi

Uruchom następujące polecenie cmdlet, aby zakończyć konfigurowanie maszyny wirtualnej. Może to zająć 5–15 minut.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Następne kroki

W części pierwszej serii opisano tworzenie konta magazynu, wdrażanie maszyny wirtualnej oraz konfigurowanie maszyny wirtualnej przy użyciu odpowiednich wstępnie wymaganych elementów, w tym następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Tworzenie maszyny wirtualnej
> * Konfigurowanie rozszerzenia skryptu niestandardowego

Przejdź do drugiej części serii, która opisuje przekazywanie dużych ilości danych na konto magazynu przy użyciu ponawiania wykładniczego i funkcji równoległości.

> [!div class="nextstepaction"]
> [Równoległe przekazywanie dużej ilości dużych plików na konto magazynu](storage-blob-scalable-app-upload-files.md)
