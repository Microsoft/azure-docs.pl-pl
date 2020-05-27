---
title: Korzystanie z usługi Azure Premium Storage z usługą SQL Server | Microsoft Docs
description: W tym artykule użyto zasobów utworzonych przy użyciu klasycznego modelu wdrażania i przedstawiono wskazówki dotyczące korzystania z usługi Azure Premium Storage z usługą SQL Server działającą na platformie Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07e8d2b6bd22029a4b6556ada62985167807eb77
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833935"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Korzystanie z usługi Azure Premium Storage z programem SQL Server na maszynach wirtualnych

## <a name="overview"></a>Omówienie

[Azure Premium dysków SSD](../disks-types.md) to Kolejna generacja magazynu, która zapewnia małe opóźnienia i wysoką przepływność we/wy. Najlepiej sprawdza się w przypadku obciążeń intensywnie korzystających z operacji we/wy, takich jak SQL Server na [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Ten artykuł zawiera informacje o planowaniu i wskazówkach dotyczących migracji maszyny wirtualnej z systemem SQL Server w celu użycia Premium Storage. Obejmuje to tworzenie infrastruktury platformy Azure (sieci, magazynu) i maszyn wirtualnych z systemem Windows gościa. Przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) przedstawia pełną kompleksową migrację sposobu przenoszenia większych maszyn wirtualnych w celu wykorzystania ulepszonego lokalnego magazynu SSD przy użyciu programu PowerShell.

Ważne jest, aby zrozumieć kompleksowy proces wykorzystywania Premium Storage platformy Azure z SQL Server na maszynach wirtualnych IAAS. Obejmuje to:

* Identyfikacja wymagań wstępnych, które mają być używane Premium Storage.
* Przykłady wdrażania SQL Server w programie IaaS, aby Premium Storage w przypadku nowych wdrożeń.
* Przykłady migracji istniejących wdrożeń, zarówno serwerów autonomicznych, jak i wdrożeń przy użyciu zawsze dostępnych grup dostępności SQL.
* Możliwe podejścia migracji.
* Pełny przykład do końca przedstawiający platformę Azure, Windows i SQL Server kroki migracji istniejącej zawsze włączonej implementacji.

Aby uzyskać więcej informacji na temat SQL Server na platformie Azure Virtual Machines, zobacz [SQL Server na platformie azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel peruwiański **Technical rewidenci:** Luis Carlos Vargas, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Wymagania wstępne dotyczące Premium Storage

Istnieje kilka wymagań wstępnych dotyczących korzystania z Premium Storage.

### <a name="machine-size"></a>Rozmiar maszyny

W przypadku korzystania z Premium Storage należy użyć Virtual Machines serii DS (VM). Jeśli wcześniej nie korzystano z maszyn z serii DS w usłudze w chmurze, należy usunąć istniejącą maszynę wirtualną, zachować dołączone dyski, a następnie utworzyć nową usługę w chmurze przed ponownym utworzeniem maszyny wirtualnej jako jej rozmiaru. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Usługi w chmurze

W przypadku tworzenia w nowej usłudze w chmurze można używać tylko maszyn wirtualnych DS * z Premium Storage. Jeśli używasz programu SQL Server zawsze włączony na platformie Azure, odbiornik "Always On" odnosi się do wewnętrznego lub zewnętrznego Load Balancer adresu IP platformy Azure skojarzonego z usługą w chmurze. W tym artykule opisano sposób przeprowadzania migracji przy zachowaniu dostępności w tym scenariuszu.

> [!NOTE]
> Seria DS * musi być pierwszą maszyną wirtualną wdrożoną w nowej usłudze w chmurze.
>
>

### <a name="regional-vnets"></a>SIECI wirtualnych regionalny

W przypadku maszyn wirtualnych usług DS * należy skonfigurować Virtual Network hostowanie maszyn wirtualnych w taki sposób, aby były regionalne. Ta "rozszerza" sieć wirtualną umożliwia zainicjowanie większych maszyn wirtualnych w innych klastrach i umożliwienie komunikacji między nimi. Na poniższym zrzucie ekranu wyróżniona lokalizacja pokazuje regionalne sieci wirtualnych, podczas gdy pierwszy wynik pokazuje sieć wirtualną "wąskie".

![RegionalVNET][1]

Możesz zgłosić bilet pomocy technicznej firmy Microsoft w celu przeprowadzenia migracji do regionalnej sieci wirtualnej. Następnie firma Microsoft wprowadza zmianę. Aby ukończyć migrację do sieci wirtualnych regionalnej, Zmień właściwość AffinityGroup w konfiguracji sieci. Najpierw wyeksportuj konfigurację sieci w programie PowerShell, a następnie zastąp Właściwość **AffinityGroup** w elemencie **VirtualNetworkSite** elementem z właściwością **Location** . Określ `Location = XXXX` `XXXX` , gdzie jest regionem świadczenia usługi Azure. Następnie zaimportuj nową konfigurację.

Rozważmy na przykład następującą konfigurację sieci wirtualnej:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Aby przenieść ją do regionalnej sieci wirtualnej w regionie Europa Zachodnia, Zmień konfigurację na następującą:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Konta magazynu

Należy utworzyć nowe konto magazynu skonfigurowane dla Premium Storage. Należy pamiętać, że użycie Premium Storage jest ustawione na koncie magazynu, a nie na poszczególnych dyskach VHD, jednak w przypadku korzystania z maszyny wirtualnej z serii DS * można dołączyć dysk VHD z kont magazynu w warstwie Premium i standardowa. Można to wziąć pod uwagę, jeśli nie chcesz umieszczać wirtualnego dysku twardego systemu operacyjnego na koncie Premium Storage.

Następujące polecenie **New-AzureStorageAccountPowerShell** z **typem** "Premium_LRS" tworzy konto Premium Storage:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Ustawienia pamięci podręcznej VHD

Główną różnicą między tworzeniem dysków, które są częścią konta Premium Storage jest ustawienie pamięci podręcznej dysku. W przypadku dysków woluminu danych SQL Server zaleca się użycie elementu "**buforowanie odczytu**". W przypadku woluminów dziennika transakcji ustawienie pamięci podręcznej dysku powinno mieć wartość "**Brak**". Różni się to od zaleceń dotyczących kont magazynu w warstwie Standardowa.

Po dołączeniu dysków VHD nie można zmienić ustawienia pamięci podręcznej. Konieczne będzie odłączenie i ponowne dołączenie dysku VHD ze zaktualizowanym ustawieniem pamięci podręcznej.

### <a name="windows-storage-spaces"></a>Miejsca do magazynowania systemu Windows

Możesz użyć funkcji [miejsca do magazynowania systemu Windows](https://technet.microsoft.com/library/hh831739.aspx) , jak w przypadku poprzedniego magazynu w warstwie Standardowa, dzięki czemu można migrować maszynę wirtualną, która już korzysta z funkcji miejsca do magazynowania. Przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (krok 9 i do przodu) pokazuje kod programu PowerShell służący do wyodrębniania i importowania maszyny wirtualnej z wieloma dołączonymi wirtualnymi dyskami wirtualnymi.

Pule magazynów były używane z kontem usługi Azure Storage w warstwie Standardowa w celu zwiększenia przepływności i skrócenia opóźnień. Możesz znaleźć wartość w temacie Testowanie pul magazynów z Premium Storageami dla nowych wdrożeń, ale w przypadku konfigurowania magazynu są dodawane dodatkowe złożoności.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Jak ustalić, które dyski wirtualne platformy Azure są mapowane na Pule magazynów

Ponieważ istnieją różne zalecenia dotyczące ustawienia pamięci podręcznej dla dołączonych wirtualnych dysków twardych, możesz zdecydować się na skopiowanie dysków VHD do konta Premium Storage. Jednak po ponownym dołączeniu ich do nowej maszyny wirtualnej z serii DS może zajść potrzeba zmiany ustawień pamięci podręcznej. Zastosowanie Premium Storage zalecanych ustawień pamięci podręcznej może być prostsze w przypadku oddzielnych dysków VHD dla plików danych i plików dziennika SQL (a nie jednego wirtualnego dysku twardego, który zawiera obie wersje).

> [!NOTE]
> W przypadku SQL Server danych i plików dziennika na tym samym woluminie wybrana opcja buforowania zależy od wzorców dostępu we/wy dla obciążeń związanych z bazą danych. Tylko testowanie może zademonstrować, która opcja buforowania jest Najlepsza dla tego scenariusza.
>
>

Jednak w przypadku korzystania z funkcji miejsca do magazynowania systemu Windows, które składają się z wielu wirtualnych dysków twardych, należy zapoznać się z oryginalnymi skryptami, aby określić, które dołączone dyski VHD znajdują się w określonej puli, więc można ustawić odpowiednie ustawienia pamięci podręcznej dla każdego dysku.

Jeśli nie masz oryginalnego skryptu, aby pokazać, które dyski VHD są mapowane do puli magazynów, możesz wykonać poniższe kroki, aby określić mapowanie puli dysków/magazynu.

Dla każdego dysku wykonaj następujące czynności:

1. Pobierz listę dysków dołączonych do maszyny wirtualnej za pomocą polecenia **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Zanotuj wartość diskname i numer LUN.

    ![DisknameAndLUN][2]
1. Pulpit zdalny do maszyny wirtualnej. Następnie przejdź do pozycji **Zarządzanie komputerem**  |  **Menedżer urządzeń**  |  **stacje dysków**. Przyjrzyj się właściwościom wszystkich dysków wirtualnych firmy Microsoft

    ![VirtualDiskProperties][3]
1. Numer LUN w tym miejscu jest odwołaniem do numeru LUN wskazanego podczas dołączania wirtualnego dysku twardego do maszyny wirtualnej.
1. W polu "dysk wirtualny Microsoft" przejdź do karty **szczegóły** , a następnie na liście **Właściwości** przejdź do pozycji **klucz sterownika**. W polu **wartość**Zwróć uwagę na **przesunięcie**, czyli 0,002 na poniższym zrzucie ekranu. W odniesieniu do PhysicalDisk2 odwołuje się do puli magazynów.

    ![VirtualDiskPropertyDetails][4]
1. Dla każdej puli magazynów wykonaj zrzuty skojarzonych dysków:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Teraz można użyć tych informacji do kojarzenia dołączonych dysków VHD z dyskami fizycznymi w pulach magazynu.

Po zmapowaniu wirtualnych dysków twardych na dyski fizyczne w pulach magazynu można odłączyć i skopiować je do konta Premium Storage, a następnie dołączyć je przy użyciu poprawnego ustawienia pamięci podręcznej. Zobacz przykład w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), kroki od 8 do 12. W tych krokach pokazano, jak wyodrębnić konfigurację dysku VHD dołączonej do maszyny wirtualnej do pliku CSV, skopiować dyski VHD, zmienić ustawienia pamięci podręcznej konfiguracji dysku, a następnie ponownie wdrożyć maszynę wirtualną jako maszynę wirtualną z serii DS przy użyciu wszystkich dołączonych dysków.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Przepustowość magazynu maszyny wirtualnej i przepływność magazynu wirtualnego dysku twardego

Wielkość wydajności magazynu zależy od określonego rozmiaru maszyny wirtualnej * i rozmiarów dysków VHD. Maszyny wirtualne mają różne przydziały dla liczby wirtualnych dysków twardych, które mogą być dołączane, oraz maksymalną przepustowość, która obsługuje (MB/s). Aby uzyskać informacje o określonych numerach przepustowości, zobacz [rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zwiększono liczbę operacji we/wy na większe rozmiary dysków. Należy wziąć pod uwagę to, kiedy myślisz o ścieżce migracji. Aby uzyskać szczegółowe informacje, [Zobacz tabelę dla operacji IOPS i typów dysków](../disks-types.md#premium-ssd).

Na koniec należy wziąć pod uwagę, że maszyny wirtualne mają różne maksymalne przepustowości dysków, które obsługują dla wszystkich dołączonych dysków. W przypadku dużego obciążenia można zmienić nasycenie maksymalnej przepustowości dysku dostępnej dla danego rozmiaru roli maszyny wirtualnej. Na przykład Standard_DS14 obsługuje do 512 MB/s; w związku z tym trzy dyski P30 umożliwiają Nasycenie przepustowości dysku maszyny wirtualnej. Jednak w tym przykładzie można przekroczyć limit przepływności w zależności od kombinacji systemu IOs odczytu i zapisu.

## <a name="new-deployments"></a>Nowe wdrożenia

W następnych dwóch sekcjach pokazano, jak można wdrożyć maszyny wirtualne SQL Server w Premium Storage. Jak wspomniano wcześniej, nie trzeba umieszczać dysku systemu operacyjnego w usłudze Premium Storage. Możesz to zrobić, jeśli zamierzasz umieścić duże obciążenia operacji we/wy na wirtualnym dysku twardym systemu operacyjnego.

Pierwszy przykład pokazuje wykorzystanie istniejących obrazów galerii platformy Azure. Drugi przykład pokazuje, jak korzystać z niestandardowego obrazu maszyny wirtualnej, który znajduje się w istniejącym standardowym koncie magazynu.

> [!NOTE]
> W tych przykładach założono, że utworzono już regionalną sieć wirtualną.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Tworzenie nowej maszyny wirtualnej przy użyciu Premium Storage z obrazem galerii

W poniższym przykładzie przedstawiono sposób umieszczania dysku VHD systemu operacyjnego w usłudze Premium Storage i dołączanie Premium Storage dysków VHD. Można jednak również umieścić dysk systemu operacyjnego na koncie magazynu w warstwie Standardowa, a następnie dołączyć dyski VHD, które znajdują się na koncie Premium Storage. Przedstawiono oba scenariusze.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Krok 1. Tworzenie konta Premium Storage

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Krok 2. Tworzenie nowej usługi w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Krok 3. Zarezerwuj adres VIP usługi w chmurze (opcjonalnie)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Krok 4. Tworzenie kontenera maszyny wirtualnej

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Krok 5. Umieszczanie wirtualnego dysku twardego systemu operacyjnego w wersji Standard lub Premium Storage

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Krok 6. Tworzenie maszyny wirtualnej

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified '-DiskSizeInGB 1023', this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Utwórz nową maszynę wirtualną, aby użyć Premium Storage z obrazem niestandardowym

W tym scenariuszu pokazano, gdzie masz istniejące dostosowane obrazy, które znajdują się na koncie magazynu w warstwie Standardowa. Jak wspomniano, jeśli chcesz umieścić wirtualny dysk twardy systemu operacyjnego na Premium Storage należy skopiować obraz, który istnieje na koncie magazynu w warstwie Standardowa, i przenieść go do Premium Storage, zanim będzie można go użyć. Jeśli masz obraz lokalnie, możesz również użyć tej metody do skopiowania bezpośrednio do konta Premium Storage.

#### <a name="step-1-create-storage-account"></a>Krok 1. Tworzenie konta magazynu

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Krok 2. Tworzenie usługi w chmurze

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Krok 3. Korzystanie z istniejącego obrazu

Możesz użyć istniejącego obrazu. Możesz też [wykonać obraz istniejącej maszyny](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Należy pamiętać, że komputer nie musi być maszyną DS *. Po utworzeniu obrazu w poniższych krokach pokazano, jak skopiować go na konto Premium Storage przy użyciu programu PowerShell **Start-AzureStorageBlobCopy** .

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Krok 4. Kopiowanie obiektu BLOB między kontami magazynu

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Krok 5. regularne sprawdzanie stanu kopiowania:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Krok 6. Dodawanie dysku obrazu do repozytorium dysku platformy Azure w ramach subskrypcji

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Może się okazać, że mimo że stan jest zgłaszany jako powodzenie, nadal można uzyskać błąd dzierżawy dysku. W takim przypadku odczekaj około 10 minut.

#### <a name="step-7--build-the-vm"></a>Krok 7. Tworzenie maszyny wirtualnej

Tutaj tworzysz maszynę wirtualną z obrazu i dołączasz dwa Premium Storage VHD:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Istniejące wdrożenia, które nie używają zawsze dostępnych grup dostępności

> [!NOTE]
> W przypadku istniejących wdrożeń należy najpierw zapoznać się z sekcją [wymagania wstępne](#prerequisites-for-premium-storage) w tym artykule.

Istnieją różne zagadnienia dotyczące wdrożeń SQL Server, które nie korzystają z zawsze zainstalowanych grup dostępności i tych, które to są. Jeśli nie używasz opcji zawsze włączone i masz istniejące autonomiczne SQL Server, możesz przeprowadzić uaktualnienie do Premium Storage przy użyciu nowej usługi w chmurze i konta magazynu. Należy wziąć pod uwagę następujące opcje:

* **Utwórz nową maszynę wirtualną SQL Server**. Można utworzyć nową maszynę wirtualną SQL Server, która używa konta Premium Storage, zgodnie z opisem w nowych wdrożeniach. Następnie wykonaj kopię zapasową i Przywróć konfigurację SQL Server i bazy danych użytkowników. Aplikacja musi zostać zaktualizowana, aby można było odwoływać się do nowego SQL Server, jeśli dostęp do niego odbywa się wewnętrznie lub zewnętrznie. Należy skopiować wszystkie obiekty "poza bazą danych" tak, jakby była wykonywana w ramach migracji SQL Serverej obok siebie (SxS). Obejmuje to takie obiekty jak logowania, certyfikaty i połączone serwery.
* **Migrowanie istniejącej maszyny wirtualnej SQL Server**. Wymaga to przełączenia SQL Server maszyny wirtualnej w tryb offline, a następnie przeniesienia jej do nowej usługi w chmurze, która obejmuje skopiowanie wszystkich dołączonych dysków VHD na konto Premium Storage. Gdy maszyna wirtualna przejdzie w tryb online, aplikacja odwołuje się do nazwy hosta serwera tak jak wcześniej. Należy pamiętać, że rozmiar istniejącego dysku wpływa na charakterystykę wydajności. Na przykład dysk 400 GB jest zaokrąglany do P20. Jeśli wiesz, że nie wymagasz wydajności dysku, możesz ponownie utworzyć maszynę wirtualną jako maszynę wirtualną z serii DS i dołączyć Premium Storage dysków VHD o wymaganej wielkości/specyfikacji wydajności. Następnie można odłączyć i ponownie dołączyć pliki bazy danych SQL.

> [!NOTE]
> Podczas kopiowania dysków VHD należy pamiętać o rozmiarze, w zależności od wielkości oznacza, jaki typ dysku Premium Storage, w którym się znajdują, określa specyfikację wydajności dysku. Platforma Azure zaokrągla do najbliższego rozmiaru dysku, więc jeśli masz dysk 400 GB, to jest zaokrąglany do P20. W zależności od istniejących wymagań we/wy wirtualnego dysku twardego systemu operacyjnego może nie być konieczne Migrowanie tego programu do konta Premium Storage.

Jeśli SQL Server jest dostępny zewnętrznie, adres VIP usługi w chmurze zostanie zmieniony. Konieczne jest również zaktualizowanie punktów końcowych, list ACL i ustawień DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Istniejące wdrożenia korzystające z zawsze dostępnych grup dostępności

> [!NOTE]
> W przypadku istniejących wdrożeń należy najpierw zapoznać się z sekcją [wymagania wstępne](#prerequisites-for-premium-storage) w tym artykule.

Wcześniej w tej sekcji opisano, jak zawsze współdziała z siecią platformy Azure. Następnie przeprowadzimy migrację w do dwóch scenariuszy: migracji, w których można tolerować trochę przestoju i przeprowadzić migrację, gdzie trzeba uzyskać minimalny Przestój.

Lokalne SQL Server zawsze włączone grupy dostępności używają odbiornika lokalnego, który rejestruje wirtualną nazwę DNS wraz z adresem IP współdzielonym przez jeden lub więcej serwerów SQL. Gdy klienci nawiązują połączenie, są kierowani przez adres IP odbiornika do podstawowego SQL Server. Jest to serwer, który jest właścicielem zawsze włączony zasób IP w tym czasie.

![DeploymentsUseAlways][6]

W Microsoft Azure można mieć tylko jeden adres IP przypisany do karty sieciowej na maszynie wirtualnej, więc w celu osiągnięcia tej samej warstwy abstrakcji jako lokalnej platforma Azure korzysta z adresu IP przypisanego do wewnętrznych/zewnętrznych modułów równoważenia obciążenia (ILB/ELB). Zasób IP współużytkowany przez serwery jest ustawiony na ten sam adres IP, co ILB/ELB. Jest on publikowany w systemie DNS, a ruch klienta jest przesyłany przez ILB/ELB do podstawowej repliki SQL Server. ILB/ELB wie, który SQL Server jest podstawowy, ponieważ używa sondy do sondowania zawsze włączony zasób IP. W poprzednim przykładzie sonduje każdy węzeł, który ma punkt końcowy, do którego odwołuje się ELB/ILB, w zależności od tego, co odpowiada, jest podstawową SQL Server.

> [!NOTE]
> ILB i ELB są przypisane do określonej usługi w chmurze platformy Azure, w związku z czym każda migracja w chmurze na platformie Azure najprawdopodobniej oznacza, że Load Balancer adres IP ulegnie zmianie.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrowanie zawsze włączone wdrożenia, które mogą pozwolić na przestoje

Istnieją dwie strategie, które umożliwiają Migrowanie zawsze na wdrożeniach, które są dostępne dla pewnego przestoju:

1. **Dodaj więcej replik pomocniczych do istniejącego klastra zawsze włączone**
2. **Migrowanie do nowego klastra zawsze włączone**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Dodaj więcej replik pomocniczych do istniejącego klastra zawsze włączone.

Jedną z strategii jest dodanie większej liczby serwerów pomocniczych do grupy dostępności zawsze włączone. Należy dodać je do nowej usługi w chmurze i zaktualizować odbiornik przy użyciu nowego adresu IP modułu równoważenia obciążenia.

##### <a name="points-of-downtime"></a>Punkty przestoju:

* Sprawdzanie poprawności klastra.
* Testowanie zawsze w trybie failover dla nowych serwerów pomocniczych.

Jeśli używasz pul magazynu systemu Windows w ramach maszyny wirtualnej w celu zwiększenia przepływności we/wy, są one przełączane do trybu offline podczas pełnej weryfikacji klastra. Test weryfikacyjny jest wymagany w przypadku dodawania węzłów do klastra. Czas potrzebny na uruchomienie testu może się różnić, dlatego należy go przetestować w reprezentatywnym środowisku testowym, aby uzyskać przybliżony czas trwania.

Należy przewidzieć czas, w którym można przeprowadzić ręczne przełączenie w tryb failover i testy chaos na nowo dodanych węzłach, aby zapewnić wysoką dostępność funkcji wysokiej dostępności zgodnie z oczekiwaniami.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Należy zatrzymać wszystkie wystąpienia SQL Server, w których pule magazynu są używane przed uruchomieniem walidacji.
>
> ##### <a name="high-level-steps"></a>Kroki ogólne
>

1. Utwórz dwa nowe serwery SQL w nowej usłudze w chmurze z dołączonymi Premium Storage.
2. Kopiuj przez pełne kopie zapasowe i Przywróć z opcją **NORECOVERY**.
3. Kopiuj poza obiekty zależne od użytkownika, takie jak logowania itp.
4. Utwórz nowy Load Balancer wewnętrzny (ILB) lub Użyj zewnętrznego Load Balancer (ELB), a następnie skonfiguruj punkty końcowe ze zrównoważonym obciążeniem na obu nowych węzłach.

   > [!NOTE]
   > Przed kontynuowaniem sprawdź, czy wszystkie węzły mają poprawną konfigurację punktu końcowego
   >
   >
5. Zatrzymaj dostęp użytkownika/aplikacji do SQL Server (jeśli są używane Pule magazynów).
6. Zatrzymaj usługi SQL Server Engine na wszystkich węzłach (jeśli są używane Pule magazynów).
7. Dodaj nowe węzły do klastra i uruchom pełną weryfikację.
8. Po pomyślnym zakończeniu walidacji Uruchom wszystkie usługi SQL Server.
9. Utwórz kopię zapasową dzienników transakcji i Przywróć bazy danych użytkowników.
10. Dodaj nowe węzły do grupy dostępności zawsze włączone i umieść replikację **synchronicznie**.
11. Dodaj zasób adres IP nowej usługi w chmurze ILB/ELB za pomocą programu PowerShell, aby był zawsze włączony na podstawie przykładu z wiele lokacji w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). W obszarze klastrowanie systemu Windows Ustaw **możliwych właścicieli** zasobu **adresu IP** na nowe węzły. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Przełączenie w tryb failover do jednego z nowych węzłów.
13. Ustaw nowych węzłów jako partnerów trybu failover i przetestuj pracę w trybie failover.
14. Usuń pierwotne węzły z grupy dostępności.

##### <a name="advantages"></a>Zalety

* Nowe serwery SQL można testować (SQL Server i aplikacji), zanim zostaną dodane do zawsze włączone.
* Można zmienić rozmiar maszyny wirtualnej i dostosować magazyn do swoich dokładnych wymagań. Jednak byłoby korzystne, aby wszystkie ścieżki plików SQL były takie same.
* Można kontrolować, kiedy jest uruchamiany transfer kopii zapasowych bazy danych do replik pomocniczych. Różni się to od używania usługi Azure **Start-AzureStorageBlobCopy** polecenia cmdlet do kopiowania dysków VHD, ponieważ jest to kopia asynchroniczna.

##### <a name="disadvantages"></a>Wady

* W przypadku korzystania z pul magazynu systemu Windows istnieje przestój klastra podczas pełnej weryfikacji klastra dla nowych węzłów dodatkowych.
* W zależności od wersji SQL Server i istniejącej liczby replik pomocniczych mogą nie być możliwe dodawanie dodatkowych replik pomocniczych bez usuwania istniejących serwerów pomocniczych.
* Podczas konfigurowania serwerów pomocniczych może być długi czas transferu danych SQL.
* W trakcie migracji są dostępne dodatkowe koszty.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Przeprowadź migrację do nowego klastra zawsze włączone

Inną strategią jest utworzenie zupełnie nowego klastra o nowej usłudze w chmurze, a następnie przekierowanie klientów do korzystania z nich.

##### <a name="points-of-downtime"></a>Punkty przestoju

W przypadku przenoszenia aplikacji i użytkowników do nowego odbiornika zawsze włączone występują przestoje. Przestój zależy od następujących:

* Czas potrzebny do przywrócenia kopii zapasowych dziennika ostatniej transakcji do baz danych na nowych serwerach.
* Czas potrzebny do zaktualizowania aplikacji klienckich w taki sposób, aby korzystał z nowego odbiornika zawsze włączony.

##### <a name="advantages"></a>Zalety

* Można testować rzeczywiste środowisko produkcyjne, SQL Server i zmiany kompilacji systemu operacyjnego.
* Istnieje możliwość dostosowania magazynu i ewentualnego zmniejszenia rozmiaru maszyny wirtualnej. Może to spowodować zmniejszenie kosztów.
* Możesz zaktualizować SQL Server kompilację lub wersję w trakcie tego procesu. Możesz również uaktualnić system operacyjny.
* Poprzedni klaster zawsze włączony może pełnić funkcję stałego miejsca docelowego wycofywania.

##### <a name="disadvantages"></a>Wady

* Należy zmienić nazwę DNS odbiornika, jeśli chcesz, aby oba klastry były uruchamiane jednocześnie. Powoduje to dodanie kosztów administracyjnych podczas migracji, ponieważ ciągi aplikacji klienckich muszą odzwierciedlać nazwę nowego odbiornika.
* Aby zminimalizować wymagania dotyczące synchronizacji przed migracją, należy zaimplementować mechanizm synchronizacji między tymi dwoma środowiskami.
* Podczas migracji występuje koszt, który jest już uruchomiony.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrowanie zawsze włączonych wdrożeń w celu zapewnienia minimalnego przestoju

Istnieją dwie strategie migracji zawsze na wdrożenia w celu zapewnienia minimalnego przestoju:

1. **Korzystanie z istniejącej pomocniczej: pojedynczej lokacji**
2. **Użyj istniejących replik pomocniczych: wiele lokacji**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Użyj istniejącej pomocniczej: pojedynczej lokacji

Jedną z strategii minimalnej przestoju jest skorzystanie z istniejącej pomocniczej chmury i usunięcie jej z bieżącej usługi w chmurze. Następnie skopiuj wirtualne dyski twarde do nowego konta Premium Storage i Utwórz maszynę wirtualną w nowej usłudze w chmurze. Następnie należy zaktualizować odbiornik w obszarze klastrowanie i tryb failover.

##### <a name="points-of-downtime"></a>Punkty przestoju

* Podczas aktualizowania węzła końcowego za pomocą punktu końcowego ze zrównoważonym obciążeniem występuje Przestój.
* Ponowne połączenie klienta może być opóźnione w zależności od konfiguracji klienta/systemu DNS.
* W przypadku wybrania opcji przełączenia grupy klastrów do trybu offline w celu wymiany adresów IP występuje dodatkowy Przestój. Można uniknąć tego przy użyciu zależności i możliwych właścicieli dla dodanego zasobu adresu IP. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Jeśli chcesz, aby dodany węzeł partake jako zawsze włączony partner trybu failover, musisz dodać punkt końcowy platformy Azure z odwołaniem do zestawu o zrównoważonym obciążeniu. Po uruchomieniu polecenia **Add-AzureEndpoint** w celu wykonania tych czynności bieżące połączenia pozostają otwarte, ale nie można nawiązać nowych połączeń z odbiornikiem do momentu zaktualizowania modułu równoważenia obciążenia. W teście były widoczne jako ostatnie 90-120seconds, należy je przetestować.

##### <a name="advantages"></a>Zalety

* Podczas migracji nie są naliczane żadne dodatkowe koszty.
* Migracja jeden do jednego.
* Zmniejszona złożoność.
* Umożliwia zwiększenie liczby operacji we/wy z Premium Storage jednostek SKU. Gdy dyski są odłączone od maszyny wirtualnej i kopiowane do nowej usługi w chmurze, można użyć narzędzia innej firmy w celu zwiększenia rozmiaru wirtualnego dysku twardego, który zapewnia wyższą przepływność. Aby zwiększyć rozmiary wirtualnego dysku twardego, zobacz tę [dyskusję na forum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Wady

* Podczas migracji istnieje tymczasowa utrata HA i DR.
* W przypadku migracji do 1:1 należy użyć minimalnego rozmiaru maszyny wirtualnej obsługującego liczbę wirtualnych dysków twardych, aby nie było możliwe Downsize maszyn wirtualnych.
* W tym scenariuszu można użyć usługi Azure **Start-AzureStorageBlobCopy** polecenia cmdlet, która jest asynchroniczna. Nie ma umowy SLA dotyczącej ukończenia kopiowania. Czas wykonywania kopii jest różny, podczas gdy jest to zależne od oczekiwania w kolejce, zależy również od ilości danych do przesłania. Czas kopiowania zwiększa się, jeśli transfer przechodzi do innego centrum danych platformy Azure, które obsługuje Premium Storage w innym regionie. Jeśli masz tylko 2 węzły, weź pod uwagę możliwe środki zaradcze w przypadku, gdy kopiowanie trwa dłużej niż w testowaniu. Może to obejmować następujące pomysły.
  * Dodaj tymczasowy węzeł trzeciego SQL Server dla HA przed migracją z uzgodnionym przestojem.
  * Uruchom migrację poza zaplanowaną konserwacją platformy Azure.
  * Upewnij się, że kworum klastra zostało prawidłowo skonfigurowane.  

##### <a name="high-level-steps"></a>Kroki ogólne

Ten dokument nie pokazuje kompletnego kompleksowego przykładu, ale [dodatek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) zawiera szczegółowe informacje, które mogą zostać użyte do wykonania tej czynności.

![MinimalDowntime][8]

* Zbierz konfigurację dysków i Usuń węzeł (nie usuwaj dołączonych wirtualnych dysków twardych).
* Utwórz konto Premium Storage i skopiuj dyski VHD z konta magazynu w warstwie Standardowa
* Utwórz nową usługę w chmurze i Wdróż ponownie maszynę wirtualną SQL2 w tej usłudze w chmurze. Utwórz maszynę wirtualną przy użyciu skopiowanego oryginalnego wirtualnego dysku twardego systemu operacyjnego i dołączając skopiowane wirtualne dyski twarde.
* Skonfiguruj ILB/ELB i Dodaj punkty końcowe.
* Aktualizuj odbiornik przez:
  * Przełączenie grupy zawsze włączone do trybu offline i zaktualizowanie odbiornika Always On z nowym adresem IP ILB/ELB.
  * Lub dodając zasób adres IP nowej usługi w chmurze ILB/ELB za pomocą programu PowerShell do klastrowania systemu Windows. Następnie ustaw możliwych właścicieli zasobu adresu IP na zmigrowany węzeł i SQL2, a następnie ustaw jako lub zależność w polu Nazwa sieci. Zobacz sekcję "Dodawanie zasobu adresu IP w tej samej podsieci" w [dodatku](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Sprawdź na klientach konfigurację/propagację DNS.
* Migruj maszynę wirtualną SQL1 i wykonaj kroki 2 – 4.
* Jeśli używasz kroków 5ii, Dodaj SQL1 jako możliwego właściciela dla dodanego zasobu adresu IP
* Testowanie pracy w trybie failover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Użyj istniejących replik pomocniczych: wiele lokacji

Jeśli masz węzły w więcej niż jednym centrum danych platformy Azure lub jeśli masz środowisko hybrydowe, możesz użyć konfiguracji Always On w tym środowisku, aby zminimalizować przestoje.

Podejście polega na zmianie synchronizacji zawsze włączone na synchroniczny dla lokalnego lub pomocniczego kontrolera domeny platformy Azure, a następnie przełączenia w tryb failover do tego SQL Server. Następnie skopiuj wirtualne dyski twarde do konta Premium Storage i ponownie Wdróż maszynę w nowej usłudze w chmurze. Zaktualizuj odbiornik, a następnie powrót po awarii.

##### <a name="points-of-downtime"></a>Punkty przestoju

Przestój obejmuje czas przejścia w tryb failover na alternatywny kontroler domeny i z powrotem. Zależy to również od konfiguracji klienta/systemu DNS, a ponowne połączenie klienta może zostać opóźnione.
Rozważmy następujący przykład konfiguracji hybrydowej:

![MultiSite1][9]

##### <a name="advantages"></a>Zalety

* Można wykorzystać istniejącą infrastrukturę.
* Istnieje możliwość wstępnego uaktualnienia usługi Azure Storage na kontrolerze domeny odzyskiwania po awarii.
* Można ponownie skonfigurować magazyn usługi odzyskiwania po awarii na platformie Azure.
* Podczas migracji istnieją co najmniej dwie przełączenia w tryb failover, z wyłączeniem testowania pracy w trybie failover.
* Nie ma potrzeby przenoszenia danych SQL Server przy użyciu kopii zapasowych i przywracania.

##### <a name="disadvantages"></a>Wady

* W zależności od dostępu klienta do SQL Server może wystąpić zwiększenie opóźnienia, gdy SQL Server jest uruchomiony w alternatywnym kontrolerze domeny dla aplikacji.
* Czas kopiowania dysków VHD do magazynu w warstwie Premium może być długi. Może to mieć wpływ na decyzję, czy zachować węzeł w grupie dostępności. Należy wziąć pod uwagę, że podczas migracji jest wymagane ładowanie zadań intensywnie korzystających z dzienników, ponieważ węzeł podstawowy musi utrzymywać niezreplikowane transakcje w dzienniku transakcji. W związku z tym może znacznie wzrosnąć.
* W tym scenariuszu można użyć usługi Azure **Start-AzureStorageBlobCopy** polecenia cmdlet, która jest asynchroniczna. Nie ma umowy SLA po zakończeniu. Czas kopiowania jest różny, podczas gdy jest to zależne od oczekiwania w kolejce, zależy również od ilości danych do przesłania. W związku z tym, że masz tylko jeden węzeł w jednym centrum danych, należy podjąć czynności zaradcze w przypadku, gdy kopiowanie trwa dłużej niż w przypadku testowania. Te czynności zaradcze obejmują następujące pomysły:
  * Dodaj tymczasowy drugi węzeł SQL dla HA przed migracją z uzgodnionym przestojem.
  * Uruchom migrację poza zaplanowaną konserwacją platformy Azure.
  * Upewnij się, że kworum klastra zostało prawidłowo skonfigurowane.

W tym scenariuszu przyjęto założenie, że zawarto udokumentowaną instalację i dowiesz się, jak magazyn jest zamapowany, aby wprowadzić zmiany w celu zapewnienia optymalnego ustawienia pamięci podręcznej.

##### <a name="high-level-steps"></a>Kroki ogólne

![Multisite2][10]

* Utwórz lokalny/alternatywny kontroler domeny platformy Azure SQL Server podstawowy i uczyń go innym partnerem trybu failover.
* Zbierz informacje o konfiguracji dysku z SQL2 i Usuń węzeł (nie usuwaj dołączonych wirtualnych dysków twardych).
* Utwórz konto Premium Storage i skopiuj dyski VHD z konta magazynu w warstwie Standardowa.
* Utwórz nową usługę w chmurze i Utwórz maszynę wirtualną SQL2 z dołączonymi dyskami magazynu Premium.
* Skonfiguruj ILB/ELB i Dodaj punkty końcowe.
* Zaktualizuj odbiornik zawsze włączony przy użyciu nowego adresu IP ILB/ELB i test pracy w trybie failover.
* Sprawdź konfigurację DNS.
* Zmień wartość AFP na SQL2, a następnie dokonaj migracji SQL1 i wykonaj kroki 2 – 5.
* Testowanie pracy w trybie failover.
* Przełączenie AFP z powrotem do SQL1 i SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Dodatek: Migrowanie klastra z obsługą wielu lokacji do Premium Storage

Pozostała część tego artykułu zawiera szczegółowy przykład konwersji wielolokacjowego klastra do magazynu w warstwie Premium. Konwertuje również odbiornik z używania zewnętrznego modułu równoważenia obciążenia (ELB) do wewnętrznego modułu równoważenia obciążenia (ILB).

### <a name="environment"></a>Środowisko

* 2k12 systemu Windows/SQL 2k12
* 1 pliki bazy danych w usłudze SP
* 2 x Pule magazynów na węzeł

![Appendix1][11]

### <a name="vm"></a>MASZYN

W tym przykładzie przedstawimy przechodzenie z ELB do ILB. ELB był dostępny przed ILB, więc pokazuje, jak przełączyć się do ILB podczas migracji.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Kroki wstępne: łączenie z subskrypcją

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Krok 1. Tworzenie nowego konta magazynu i usługi w chmurze

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Krok 2: zwiększenie dozwolonych błędów dla zasobów \< opcjonalnych>

W przypadku niektórych zasobów należących do grupy dostępności zawsze włączone istnieją limity dotyczące liczby błędów, które mogą wystąpić w danym okresie, w których usługa klastra próbuje ponownie uruchomić grupę zasobów. Zaleca się zwiększyć ten proces podczas przechodzenia przez tę procedurę, ponieważ w przypadku nieręcznego przełączenia w tryb failover i wyzwolenia trybu failover w celu zamknięcia maszyn można przejść do tego limitu.

Rozsądne jest podwójne zwiększenie liczby niepowodzeń, aby zrobić to w Menedżer klastra trybu failover, przejdź do właściwości zawsze włączone grupy zasobów:

![Appendix3][13]

Zmień maksymalną liczbę błędów na 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Krok 3. Dodawanie zasobu adresu IP dla \< opcjonalnej> grupy klastrów

Jeśli istnieje tylko jeden adres IP dla grupy klastra i jest on wyrównany do podsieci chmury, uważaj, jeśli przypadkowo przejdziesz do trybu offline wszystkie węzły klastra w chmurze w tej sieci, nie będzie można przełączyć zasobu adresu IP klastra i sieci klastra do trybu online. W takiej sytuacji uniemożliwia ona aktualizowanie innych zasobów klastra.

#### <a name="step-4-dns-configuration"></a>Krok 4. Konfiguracja DNS

Implementacja płynnego przejścia zależy od sposobu, w jaki system DNS jest używany i aktualizowany.
Gdy program jest zawsze włączony, tworzy grupę zasobów klastra systemu Windows, jeśli otwarto Menedżer klastra trybu failover, zobaczysz, że co najmniej ma trzy zasoby, do których odwołuje się dokument:

* Nazwa Virtual Network (VNN) — nazwa DNS, z którą łączą się klienci, gdy chcesz połączyć się z serwerami SQL Server przy użyciu funkcji zawsze włączone.
* Zasób adresu IP — adres IP, który został skojarzony z VNN, może mieć więcej niż jeden, a w konfiguracji w wielu lokacjach istnieje adres IP dla każdej lokacji/podsieci.

Podczas nawiązywania połączenia z SQL Server, sterownik klienta SQL Server pobiera rekordy DNS skojarzone z odbiornikiem i próbuje połączyć się z każdym zawsze przy skojarzonym adresie IP. Następnie omawiamy niektóre czynniki, które mogą mieć wpływ na to.

Liczba współbieżnych rekordów DNS, które są skojarzone z nazwą odbiornika, zależy nie tylko od liczby skojarzonych adresów IP, ale RegisterAllIpProviders'setting w klastrze trybu failover dla zasobu zawsze włączone VNN.

Po wdrożeniu zawsze włączone na platformie Azure należy wykonać różne kroki, aby utworzyć odbiornik i adresy IP, trzeba ręcznie skonfigurować "RegisterAllIpProviders" do 1, ale jest to inne dla lokalnego wdrożenia, które ma już ustawioną wartość 1.

Jeśli wartość "RegisterAllIpProviders" to 0, zobaczysz tylko jeden rekord DNS w systemie DNS skojarzonym z odbiornikiem:

![Appendix4][14]

Jeśli "RegisterAllIpProviders" to 1:

![Appendix5][15]

Poniższy kod zrzuca ustawienia VNN i ustawia go dla Ciebie. Aby zmiana zaczęła obowiązywać, należy przełączyć VNN w tryb offline i włączyć ją z powrotem w tryb online. Spowoduje to przejście odbiornika w tryb offline, powodując zakłócenia łączności klienta.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

W późniejszym kroku migracji należy zaktualizować odbiornik zawsze włączony przy użyciu zaktualizowanego adresu IP, który odwołuje się do modułu równoważenia obciążenia, co obejmuje usunięcie i dodanie zasobu adresu IP. Po aktualizacji IP należy upewnić się, że nowy adres IP został zaktualizowany w strefie DNS i że klienci aktualizują lokalną pamięć podręczną DNS.

Jeśli klienci znajdują się w innym segmencie sieci i odwołują się do innego serwera DNS, należy wziąć pod uwagę, co się dzieje z transferem strefy DNS podczas migracji, ponieważ czas ponownego połączenia aplikacji jest ograniczony przez co najmniej godzinę transferu strefy dla każdego nowego adresu IP odbiornika. Jeśli w tym miejscu jest używane ograniczenie czasowe, należy omówić i testować przyrostowy transfer stref z zespołami Windows, a także wprowadzić rekord hosta DNS na czas wygaśnięcia (TTL), aby zaktualizować klientów. Aby uzyskać więcej informacji, zobacz [Przyrostowe transfery stref](https://technet.microsoft.com/library/cc958973.aspx) i [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Domyślnie czas wygaśnięcia rekordu DNS, który jest skojarzony z odbiornikiem w przypadku, gdy jest on zawsze włączony na platformie Azure, wynosi 1200 sekund. Możesz zmniejszyć ten limit, jeśli w trakcie migracji używasz ograniczenia czasowego, aby upewnić się, że klienci zaktualizują system DNS przy użyciu zaktualizowanego adresu IP dla odbiornika. Konfigurację można zobaczyć i zmodyfikować przez zatopienie konfiguracji VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Im mniejsza jest wartość "HostRecordTTL", tym większa jest liczba ruchu DNS.

##### <a name="client-application-settings"></a>Ustawienia aplikacji klienta

Jeśli aplikacja kliencka SQL obsługuje program .NET 4,5 SQLClient, można użyć słowa kluczowego "MULTISUBNETFAILOVER = TRUE". To słowo kluczowe powinno być stosowane, ponieważ umożliwia szybsze nawiązywanie połączenia z zawsze włączonymi grupami dostępności programu SQL podczas pracy w trybie failover. Jest on wyliczany przez wszystkie adresy IP skojarzone z zawsze włączonym odbiornikiem, a jednocześnie wykonuje bardziej agresywne ponowienie połączenia TCP podczas pracy w trybie failover.

Aby uzyskać więcej informacji na temat poprzednich ustawień, zobacz [słowo kluczowe MultiSubnetFailover i powiązane funkcje](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Zapoznaj [się również z obsługą klienta w celu zapewnienia wysokiej dostępności, odzyskiwania po awarii](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Krok 5. ustawienia kworum klastra

W miarę jak należy wykonać co najmniej jedną SQL Server w tym samym czasie, należy zmodyfikować ustawienie kworum klastra, jeśli używany jest monitor udziału plików (FSW) z dwoma węzłami, należy ustawić kworum tak, aby zezwalało na większość węzłów i korzystać z dynamicznego głosu, co pozwala na stałe pozostawanie w jednym węźle.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Aby uzyskać więcej informacji na temat zarządzania kworum klastra i konfigurowania go, zobacz [Konfigurowanie kworum i zarządzanie nim w klastrze trybu failover systemu Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Krok 6. wyodrębnienie istniejących punktów końcowych i list ACL

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Zapisz ten tekst do pliku.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Krok 7. zmiana trybu failover partnerów i trybów replikacji

Jeśli masz więcej niż dwa serwery SQL, należy zmienić tryb failover innej pomocniczej w innym kontrolerze domeny lub lokalnie na "synchroniczny" i uczynić go automatycznym partnerem trybu failover (AFP), dzięki czemu można zachować HA podczas wprowadzania zmian. Można to zrobić za pośrednictwem TSQL modyfikacji, chociaż narzędzie SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Krok 8. Usuwanie pomocniczej maszyny wirtualnej z usługi w chmurze

Należy najpierw zaplanować migrację węzła pomocniczego w chmurze. Jeśli ten węzeł jest obecnie podstawowy, należy zainicjować ręczną pracę awaryjną.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 9. zmiana ustawień buforowania dysku w pliku CSV i zapisywanie

W przypadku woluminów danych należy ustawić wartość tylko do odczytu.

Dla woluminów TLOG należy ustawić wartość NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Krok 10. kopiowanie dysków VHD

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Stan kopiowania wirtualnych dysków twardych można sprawdzić na koncie Premium Storage:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Zaczekaj, aż wszystkie te dane zostaną zarejestrowane jako powodzenie.

Aby uzyskać informacje dotyczące pojedynczych obiektów blob:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Krok 11. rejestrowanie dysku systemu operacyjnego

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Krok 12. Importowanie dodatkowych do nowej usługi w chmurze

W poniższym kodzie użyto również opcji dodanej w tym miejscu możesz zaimportować maszynę i użyć możliwego do przechowania adresu VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Krok 13. Tworzenie ILB w nowej chmurze SVC, dodawanie punktów końcowych ze zrównoważonym obciążeniem i list ACL

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Krok 14. aktualizowanie zawsze włączone

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Teraz usuń stary adres IP usługi w chmurze.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Krok 15: sprawdzenie aktualizacji DNS

Należy teraz sprawdzić serwery DNS w SQL Server sieci klienta i upewnić się, że klaster dodał dodatkowy rekord hosta dla dodanego adresu IP. Jeśli te serwery DNS nie zostały zaktualizowane, należy rozważyć wymuszenie przeniesienia strefy DNS i upewnić się, że klienci w podsieci będą mogli rozpoznać zarówno adresy IP, jak i w ten sposób, że nie trzeba czekać na automatyczną replikację DNS.

#### <a name="step-16-reconfigure-always-on"></a>Krok 16. ponowne konfigurowanie zawsze włączone

W tym momencie poczekasz na pomocniczy węzeł, który został poddany migracji, aby w pełni ponownie synchronizować się z węzłem lokalnym i przełączyć się na węzeł replikacji synchronicznej i udostępnić go w ramach platformy AFP.  

#### <a name="step-17-migrate-second-node"></a>Krok 17. Migrowanie drugiego węzła

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Krok 18. zmiana ustawień buforowania dysku w pliku CSV i zapisywanie

W przypadku woluminów danych ustawienia pamięci podręcznej powinny mieć wartość tylko do odczytu.

W przypadku woluminów TLOG ustawienia pamięci podręcznej powinny mieć wartość NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Krok 19. Tworzenie nowego niezależnego konta magazynu dla węzła pomocniczego

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Krok 20. kopiowanie dysków VHD

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Stan kopiowania wirtualnego dysku twardego można sprawdzić dla wszystkich dysków VHD:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Zaczekaj, aż wszystkie te dane zostaną zarejestrowane jako powodzenie.

Aby uzyskać informacje dotyczące pojedynczych obiektów blob:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Krok 21. rejestrowanie dysku systemu operacyjnego

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Krok 22: Dodawanie punktów końcowych ze zrównoważonym obciążeniem i list ACL

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Krok 23. test pracy w trybie failover

Zaczekaj, aż migrowany węzeł zostanie zsynchronizowany z lokalnym węzłem zawsze włączonym. Umieść go w trybie replikacji synchronicznej i poczekaj na zsynchronizowanie. Następnie przejście w tryb failover z lokalizacji lokalnej do pierwszego migrowanego węzła, który jest AFP. Po zakończeniu pracy, Zmień ostatni zmigrowany węzeł na AFP.

Należy przetestować tryb failover między wszystkimi węzłami i uruchamiać testy chaos, aby zapewnić pracę w trybie failover zgodnie z oczekiwaniami i w Manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Krok 24. przełączenie ustawień kworum klastra/czas wygaśnięcia DNS/Pntrs trybu failover/synchronizacja

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Dodawanie zasobu adresu IP w tej samej podsieci

Jeśli masz tylko dwa serwery SQL i chcesz migrować je do nowej usługi w chmurze, ale chcesz zachować je w tej samej podsieci, możesz uniknąć przełączenia odbiornika do trybu offline, aby usunąć oryginalny adres IP, a następnie dodać nowy adres IP. W przypadku migrowania maszyn wirtualnych do innej podsieci nie trzeba tego robić, ponieważ istnieje dodatkowa sieć klastrów, która odwołuje się do tej podsieci.

Po przeprowadzeniu migracji pomocniczej i dodaniu jej do nowego zasobu adresu IP dla nowej usługi w chmurze przed przejściem do trybu failover istniejącego elementu podstawowego należy wykonać następujące kroki w Menedżer trybu failover klastra:

Aby dodać adres IP, zobacz dodatek, Krok 14.

1. W przypadku bieżącego zasobu adresu IP Zmień możliwego właściciela na "istniejący SQL Server podstawowy", w przykładzie "dansqlams4":

    ![Appendix13][23]
2. W przypadku nowego zasobu adresu IP Zmień możliwego właściciela na "zmigrowany SQL Server pomocniczy", w przykładzie "dansqlams5":

    ![Appendix14][24]
3. Po wybraniu tej opcji można przełączyć się do trybu failover, a po przeprowadzeniu migracji ostatniego węzła należy edytować możliwych właścicieli, aby ten węzeł został dodany jako możliwy właściciel:

    ![Appendix15][25]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Premium Storage platformy Azure](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server na platformie Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
