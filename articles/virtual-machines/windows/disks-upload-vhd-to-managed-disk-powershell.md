---
title: Przekazywanie dysku VHD na platformę Azure lub kopiowanie dysku w różnych regionach — usługa Azure PowerShell
description: Dowiedz się, jak przekazać dysk VHD na dysk zarządzany platformy Azure i skopiować dysk zarządzany w różnych regionach za pomocą programu Azure PowerShell za pośrednictwem bezpośredniego przekazywania.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 7c66507989357569828d4ef933cfdca735f71570
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085415"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Przekazywanie dysku VHD na platformę Azure lub kopiowanie dysku zarządzanego do innego regionu — Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj moduł programu Azure PowerShell](/powershell/azure/install-Az-ps).
- Jeśli zamierzasz przekazać dysk VHD z lokalnego: dysk VHD o stałym rozmiarze, który [został przygotowany dla platformy Azure,](prepare-for-upload-vhd-image.md)przechowywany lokalnie.
- Lub dysku zarządzanego na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="getting-started"></a>Wprowadzenie

Jeśli wolisz przekazać dyski za pośrednictwem interfejsu użytkownika, możesz to zrobić za pomocą Eksploratora usługi Azure Storage. Aby uzyskać szczegółowe informacje, zobacz: [Zarządzanie dyskami zarządzanymi platformy Azure za pomocą Eksploratora usługi Azure](disks-use-storage-explorer-managed-disks.md)

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany, który jest skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z nich, istnieje kilka dodatkowych informacji, które należy wiedzieć o tych dyskach.

Ten rodzaj dysku zarządzanego ma dwa unikatowe stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania, ale nie [zabezpieczono podpis dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) został wygenerowany.
- ActiveUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania i sygnatury dostępu Współdzielonego został wygenerowany.

> [!NOTE]
> W każdym z tych stanów dysk zarządzany będzie rozliczany według [standardowych cen dysku twardego](https://azure.microsoft.com/pricing/details/managed-disks/), niezależnie od rzeczywistego typu dysku. Na przykład P10 będą rozliczane jako S10. Będzie to prawdą, dopóki nie `revoke-access` zostanie wywołana na dysku zarządzanym, który jest wymagany do podłączenia dysku do maszyny Wirtualnej.

## <a name="create-an-empty-managed-disk"></a>Tworzenie pustego dysku zarządzanego

Zanim będzie można utworzyć pusty standardowy dysk twardy do przesyłania, będziesz potrzebować rozmiaru pliku VHD, który chcesz przekazać, w bajtach. Przykładowy kod dostanie to za Ciebie, ale aby `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`zrobić to sam, możesz użyć: . Ta wartość jest używana podczas określania **parametru -UploadSizeInBytes.**

Teraz na lokalnej powłoce utwórz pusty standardowy dysk twardy do przekazywania, określając ustawienie **Przekazywanie** w parametrze **-CreateOption,** a także parametr **-UploadSizeInBytes** w poleceniu [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Następnie zadzwoń do [new-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) aby utworzyć dysk.

Zamień `<yourdiskname>`, `<yourresourcegroupname>`a `<yourregion>` następnie uruchom następujące polecenia:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Jeśli chcesz przesłać ssd premium lub standardowy SSD, zastąp **Standard_LRS** **Premium_LRS** lub **StandardSSD_LRS**. Dyski ultra nie są jeszcze obsługiwane.

Po utworzeniu pustego dysku zarządzanego skonfigurowanego dla procesu przekazywania można przekazać do niego dysk wirtualny. Aby przekazać dysk VHD na dysk, musisz zapisywać sas, dzięki czemu można odwoływać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalny sygnatura dostępu Współdzielonego pustego dysku zarządzanego, zastąp `<yourdiskname>`i `<yourresourcegroupname>`użyj następujących poleceń:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnatury dostępu Współdzielonego dla pustego dysku zarządzanego, można go użyć do skonfigurowania dysku zarządzanego jako miejsca docelowego polecenia przekazywania.

Użyj AzCopy v10, aby przekazać lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu Współdzielonego.

To przesyłanie ma taką samą przepustowość jak równoważny [standardowy dysk twardy](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który odpowiada S4, będziesz miał przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który odpowiada S70, będziesz miał przepustowość do 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po zakończeniu przekazywania i nie trzeba już zapisywać więcej danych na dysku, odwołać sygnaturę dostępu Współdzielonego. Odwołanie sygnatury dostępu Współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny Wirtualnej.

Zamień `<yourdiskname>`i `<yourresourcegroupname>`, a następnie uruchom następujące polecenie:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można skopiować w tym samym regionie lub skopiować dysk zarządzany do innego regionu.

Skrypt obserwowany zrobi to za Ciebie, proces jest podobny do kroków opisanych wcześniej, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy udostępniasz rozmiar dysku w bajtach dysku zarządzanego z platformy Azure. Dzieje się tak, ponieważ platforma Azure pomija stopki podczas zwracania rozmiaru dysku. Kopia zakończy się niepowodzeniem, jeśli tego nie zrobisz. Poniższy skrypt już to robi za Ciebie.

`<sourceResourceGroupHere>`Zastąp `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourOSTypeHere>` `<sourceDiskNameHere>` `<yourTargetLocationHere>` , , i (przykładem wartości lokalizacji będzie uswest2) wartościami, a następnie uruchom następujący skrypt w celu skopiowania dysku zarządzanego.

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

Po pomyślnym przekazaniu dysku wirtualnego na dysk zarządzany można dołączyć dysk do maszyny Wirtualnej i rozpocząć jej używanie.

Aby dowiedzieć się, jak dołączyć dysk danych do maszyny Wirtualnej, zobacz nasz artykuł na ten temat: [Dołączanie dysku z danymi do maszyny Wirtualnej systemu Windows za pomocą programu PowerShell](attach-disk-ps.md). Aby użyć dysku jako dysku systemu operacyjnego, zobacz [Tworzenie maszyny Wirtualnej systemu Windows z dysku specjalistycznego](create-vm-specialized.md#create-the-new-vm).
