---
title: Przekaż dysk VHD do platformy Azure lub skopiuj go między regionami — Azure PowerShell
description: Dowiedz się, jak przekazać dysk VHD do dysku zarządzanego platformy Azure i skopiować dysk zarządzany między regionami przy użyciu Azure PowerShell za pośrednictwem bezpośredniego przekazywania.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 1655c48eeb9227bf934c7fd9bb37610327b2b98c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736275"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Przekaż dysk VHD do platformy Azure lub skopiuj go do innego regionu — Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj moduł Azure PowerShell](/powershell/azure/install-Az-ps).
- Jeśli zamierzasz przekazać dysk VHD ze swojego miejsca lokalnego: dysk VHD o stałym rozmiarze [przygotowany dla platformy Azure](prepare-for-upload-vhd-image.md), przechowywany lokalnie.
- Lub dysk zarządzany na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="getting-started"></a>Wprowadzenie

Jeśli wolisz przekazywać dyski za pośrednictwem graficznego interfejsu użytkownika, możesz to zrobić za pomocą Eksplorator usługi Azure Storage. Aby uzyskać szczegółowe informacje, zobacz [Eksplorator usługi Azure Storage zarządzania usługą Azure Managed disks](../disks-use-storage-explorer-managed-disks.md) .

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z tych dysków należy zapoznać się z dodatkowymi informacjami.

Ten rodzaj dysku zarządzanego ma dwa unikatowe Stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odebrania przekazania, ale nie Wygenerowano [sygnatury bezpiecznego dostępu](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, co oznacza, że dysk jest gotowy do odebrania przekazywania i Wygenerowano sygnaturę dostępu współdzielonego.

> [!NOTE]
> W każdym z tych stanów dysk zarządzany jest rozliczany zgodnie ze [standardowymi cenami](https://azure.microsoft.com/pricing/details/managed-disks/)dysków twardych, niezależnie od rzeczywistego typu dysku. Na przykład P10 będzie rozliczany jako S10. Ta wartość będzie prawdziwa do momentu `revoke-access` wywołania na dysku zarządzanym, który jest wymagany w celu dołączenia dysku do maszyny wirtualnej.

## <a name="create-an-empty-managed-disk"></a>Utwórz pusty dysk zarządzany

Aby można było utworzyć pusty dysk twardy w celu przeładowania, potrzebny będzie rozmiar pliku wirtualnego dysku twardego, który ma zostać przekazany w bajtach. Przykładowy kod uzyskasz dla Ciebie, ale aby zrobić to samodzielnie, możesz użyć: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Ta wartość jest używana podczas określania parametru **-UploadSizeInBytes** .

Teraz w lokalnej powłoce Utwórz pusty standardowy dysk twardy do przekazania przez określenie ustawienia **przekazywania** w parametrze **-i** parametru, a także parametru **-UploadSizeInBytes** w poleceniu cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) . Następnie Wywołaj polecenie [New-AzDisk](/powershell/module/az.compute/new-azdisk) , aby utworzyć dysk.

Zastąp `<yourdiskname>` , `<yourresourcegroupname>` , a `<yourregion>` następnie uruchom następujące polecenia:

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, Dodaj HyperVGeneration " <yourGeneration> " do `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Jeśli chcesz przekazać dysk SSD w warstwie Premium lub dysk SSD w warstwie Standardowa, Zastąp **Standard_LRS** z **Premium_LRS** lub **StandardSSD_LRS**. Dyski Ultra nie są jeszcze obsługiwane.

Teraz, gdy został utworzony pusty dysk zarządzany skonfigurowany dla procesu przekazywania, można przekazać do niego dysk VHD. Aby przekazać dysk VHD do dysku, musisz mieć zapisywalne sygnatury dostępu współdzielonego, aby można było odwołać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalne SYGNATURy dostępu do pustego dysku zarządzanego, Zastąp `<yourdiskname>` je, a `<yourresourcegroupname>` następnie użyj następujących poleceń:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego dla polecenia przekazywania.

Za pomocą AzCopy v10 Przekaż swój lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu współdzielonego.

To przekazywanie ma taką samą przepływność jak odpowiednik [standardowego dysku twardego](../disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który jest równa S4, będziesz mieć przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który jest równa S70, będziesz mieć przepływność do 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po zakończeniu przekazywania i nie musisz już pisać więcej danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwoływanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

Zastąp `<yourdiskname>` `<yourresourcegroupname>` , a następnie uruchom następujące polecenie:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można wykonać kopię w tym samym regionie lub skopiować dysk zarządzany do innego regionu.

Wykonanie poniższej skryptu spowoduje to, że proces jest podobny do opisanego wcześniej procedury, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy podajesz rozmiar dysku w bajtach dysku zarządzanego na platformie Azure. Wynika to z faktu, że platforma Azure pomija stopkę podczas zwracania rozmiaru dysku. Kopia nie powiedzie się, jeśli to zrobisz. Poniższy skrypt już robi to za Ciebie.

Zastąp `<sourceResourceGroupHere>` wartości,, `<sourceDiskNameHere>` `<targetDiskNameHere>` , `<targetResourceGroupHere>` `<yourOSTypeHere>` i `<yourTargetLocationHere>` (przykładem wartości lokalizacji byłaby uswest2) wartościami, a następnie uruchom poniższy skrypt w celu skopiowania dysku zarządzanego.

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, Dodaj HyperVGeneration " <yourGeneration> " do `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wirtualny dysk twardy został pomyślnie przekazany do dysku zarządzanego, możesz dołączyć dysk do maszyny wirtualnej i zacząć z niego korzystać.

Aby dowiedzieć się, jak dołączyć dysk danych do maszyny wirtualnej, zapoznaj się z artykułem na temat: [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](attach-disk-ps.md). Aby użyć dysku jako dysku systemu operacyjnego, zobacz [Tworzenie maszyny wirtualnej z systemem Windows na podstawie wyspecjalizowanego dysku](create-vm-specialized.md#create-the-new-vm).
