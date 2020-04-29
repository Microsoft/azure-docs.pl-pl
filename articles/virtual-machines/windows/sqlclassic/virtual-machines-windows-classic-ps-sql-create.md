---
title: Tworzenie klasycznej maszyny wirtualnej SQL Server (PowerShell)
description: Zawiera instrukcje i skrypty programu PowerShell do tworzenia maszyny wirtualnej platformy Azure przy użyciu obrazów galerii maszyn wirtualnych SQL Server. W tym temacie jest stosowany klasyczny tryb wdrażania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77914793"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej SQL Server przy użyciu Azure PowerShell (klasyczny)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

W tym artykule przedstawiono procedurę tworzenia SQL Server maszyny wirtualnej na platformie Azure przy użyciu poleceń cmdlet programu PowerShell.

> [!NOTE] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Aby uzyskać Menedżer zasobów wersję tego tematu, zobacz [Inicjowanie obsługi SQL Server maszyny wirtualnej przy użyciu Azure PowerShell Menedżer zasobów](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalowanie i Konfigurowanie programu PowerShell:
1. Jeśli nie masz konta platformy Azure, odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Pobierz i zainstaluj najnowsze polecenia Azure PowerShell](/powershell/azure/overview).
3. Uruchom program Windows PowerShell, a następnie połącz go z subskrypcją platformy Azure za pomocą polecenia **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Określanie docelowego regionu platformy Azure

Maszyna wirtualna SQL Server będzie hostowana w usłudze w chmurze, która znajduje się w określonym regionie platformy Azure. Poniższe kroki ułatwiają określenie regionu, konta magazynu i usługi w chmurze, która będzie używana w pozostałej części tego samouczka.

1. Określ centrum danych, które ma być używane do hostowania SQL Server maszyny wirtualnej. Następujące polecenie programu PowerShell wyświetla listę dostępnych nazw regionów.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Po zidentyfikowaniu preferowanej lokalizacji Ustaw zmienną o nazwie **$dcLocation** w tym regionie. Na przykład następujące polecenie ustawia region na "Wschodnie stany USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ustawianie subskrypcji i konta magazynu

1. Określ subskrypcję platformy Azure, która będzie używana dla nowej maszyny wirtualnej.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Przypisz docelową subskrypcję platformy Azure do zmiennej **$subscr** . Następnie ustaw tę opcję jako bieżącą subskrypcję platformy Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Następnie wyszukaj istniejące konta magazynu. Poniższy skrypt przedstawia wszystkie konta magazynu istniejące w wybranym regionie:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Jeśli jest wymagane nowe konto magazynu, należy najpierw utworzyć nazwę konta magazynu o małych wielkościach liter przy użyciu polecenia New-AzureStorageAccount, jak w poniższym przykładzie:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Przypisz nazwę docelowego konta magazynu do **$staccount**. Następnie użyj **Set-AzureSubscription** , aby ustawić subskrypcję i bieżące konto magazynu.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Wybierz obraz maszyny wirtualnej SQL Server

1. Zapoznaj się z listą dostępnych obrazów maszyn wirtualnych SQL Server z galerii. Wszystkie te obrazy mają właściwość **ImageFamily** , która rozpoczyna się od "SQL". Poniższe zapytanie wyświetla rodzinę obrazów dostępną dla Ciebie SQL Server preinstalowany.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Po znalezieniu rodziny obrazów maszyn wirtualnych w tej rodzinie może istnieć wiele opublikowanych obrazów. Użyj poniższego skryptu, aby znaleźć najnowszą nazwę opublikowanego obrazu maszyny wirtualnej dla wybranej rodziny obrazów (na przykład **SQL Server 2016 RTM Enterprise w systemie Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Na koniec Utwórz maszynę wirtualną przy użyciu programu PowerShell:

1. Utwórz usługę w chmurze, która będzie hostować nową maszynę wirtualną. Należy zauważyć, że jest również możliwe użycie istniejącej usługi w chmurze. Utwórz nową zmienną **$svcname** z krótką nazwą usługi w chmurze.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Określ nazwę i rozmiar maszyny wirtualnej. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Określ konto i hasło administratora lokalnego.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Uruchom następujący skrypt, aby utworzyć maszynę wirtualną.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Dodatkowe wyjaśnienie i opcje konfiguracji znajdują się w sekcji **kompilacja zestawu poleceń** w temacie [Używanie Azure PowerShell do tworzenia i wstępnego konfigurowania Virtual Machines opartych na systemie Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Poniższy skrypt zawiera przykładowy kompletny skrypt, który tworzy **SQL Server 2016 RTM Enterprise na maszynie wirtualnej z systemem Windows Server 2012 R2** . W przypadku użycia tego skryptu należy dostosować początkowe zmienne zgodnie z poprzednimi krokami w tym temacie.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Nawiązywanie połączenia z pulpitem zdalnym

1. Utwórz pliki RDP w folderze dokumentu bieżącego użytkownika, aby uruchomić te maszyny wirtualne w celu ukończenia instalacji:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. W katalogu dokumenty Uruchom plik RDP. Połącz się przy użyciu nazwy użytkownika i hasła administratora podane wcześniej (na przykład jeśli Twoja nazwa użytkownika została VMAdmin, określ wartość "\VMAdmin" jako użytkownika i podaj hasło).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Ukończ konfigurację maszyny SQL Server na potrzeby dostępu zdalnego

Po zalogowaniu się na komputerze przy użyciu pulpitu zdalnego Skonfiguruj SQL Server na podstawie instrukcji w sekcji [kroki dotyczące konfigurowania łączności SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Następne kroki

Dodatkowe instrukcje dotyczące aprowizacji maszyn wirtualnych za pomocą programu PowerShell można znaleźć w [dokumentacji maszyn wirtualnych](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

W wielu przypadkach następnym krokiem jest Migrowanie baz danych do tej nowej maszyny wirtualnej SQL Server. Aby uzyskać wskazówki dotyczące migracji bazy danych, zobacz [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Jeśli interesuje Cię również korzystanie z Azure Portal do tworzenia Virtual Machines SQL, zobacz [Inicjowanie obsługi administracyjnej SQL Server maszyny wirtualnej na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Należy pamiętać, że samouczek, który przeprowadzi Cię przez portal, tworzy maszyny wirtualne przy użyciu zalecanego modelu Menedżer zasobów, a nie modelu klasycznego użytego w tym temacie programu PowerShell.

Oprócz tych zasobów zalecamy przejrzenie [innych tematów związanych z uruchamianiem SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
