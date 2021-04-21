---
title: Przekazywanie wirtualnego dysku twardego na platformę Azure lub kopiowanie dysku między regionami — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać wirtualny dysk twardy na dysk zarządzany platformy Azure i skopiować dysk zarządzany między regionami przy użyciu interfejsu wiersza polecenia platformy Azure za pośrednictwem przekazywania bezpośredniego.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 285f0acd5097ce68cddee6f732b17944dffb0eba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762573"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Przekazywanie wirtualnego dysku twardego na platformę Azure lub kopiowanie dysku zarządzanego do innego regionu — interfejs wiersza polecenia platformy Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy w wersji 10.](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)
- [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
- Jeśli zamierzasz przekazać wirtualny dysk twardy ze środowisk lokalnych: dysk VHD o stałym rozmiarze, który został przygotowany dla platformy [Azure,](../windows/prepare-for-upload-vhd-image.md)przechowywany lokalnie.
- Lub dysk zarządzany na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="getting-started"></a>Wprowadzenie

Jeśli wolisz przekazywać dyski za pośrednictwem graficznego interfejsu użytkownika, możesz to zrobić przy użyciu Eksplorator usługi Azure Storage. Aby uzyskać szczegółowe informacje, zobacz Use Eksplorator usługi Azure Storage to manage Azure managed disks (Zarządzanie [dyskami zarządzanymi platformy Azure przy](../disks-use-storage-explorer-managed-disks.md) użyciu usługi Azure Managed Disks)

Aby przekazać wirtualny dysk twardy na platformę Azure, musisz utworzyć pusty dysk zarządzany skonfigurowany na potrzeby tego procesu przekazywania. Przed utworzeniem jednego z nich należy uzyskać dodatkowe informacje o tych dyskach.

Ten rodzaj dysku zarządzanego ma dwa unikatowe stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania, ale nie [wygenerowano](../../storage/common/storage-sas-overview.md) bezpiecznego podpisu dostępu (SAS).
- ActiveUpload, co oznacza, że dysk jest gotowy do odbierania przekazania i wygenerowano sygnaturę dostępu współdzielonego.

> [!NOTE]
> W jednym z tych stanów dysk zarządzany będzie rozliczany w standardowych cenach dysków [HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)niezależnie od rzeczywistego typu dysku. Na przykład element P10 będzie rozliczany jako S10. Będzie to miało wartość true, dopóki nie zostanie wywołana na dysku zarządzanym, co jest wymagane do dołączenia dysku `revoke-access` do maszyny wirtualnej.

## <a name="create-an-empty-managed-disk"></a>Tworzenie pustego dysku zarządzanego

Przed utworzeniem pustego standardowego dysku twardego do przekazania potrzebny będzie rozmiar pliku VHD, który chcesz przekazać, w bajtach. Aby to uzyskać, możesz użyć funkcji `wc -c <yourFileName>.vhd` lub `ls -al <yourFileName>.vhd` . Ta wartość jest używana podczas określania **parametru --upload-size-bytes.**

Utwórz pusty standardowy dysk twardy do przekazania, określając zarówno parametr **-–for-upload,** jak i **parametr --upload-size-bytes** w poleceniu cmdlet [create](/cli/azure/disk#az_disk_create) dysku:

Zastąp `<yourdiskname>` , `<yourresourcegroupname>` , `<yourregion>` wartościami. Parametr `--upload-size-bytes` zawiera przykładową wartość `34359738880` , zamień ją na wartość odpowiednią dla Ciebie.

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, dodaj element --hyper-v-generation <yourGeneration> do programu `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przekazać dysk SSD w warstwie Premium lub ssd w  warstwie Standardowa, zastąp standard_lrs **za** pomocą premium_LRS **lub standardssd_lrs**. Dyski w warstwie Ultra nie są obecnie obsługiwane.

Teraz, po utworzeniu pustego dysku zarządzanego skonfigurowanego dla procesu przekazywania, możesz przekazać do niego wirtualny dysk twardy. Aby przekazać dysk VHD na dysk, potrzebna będzie zapisywalna sygnatura dostępu współdzielonego, aby można było odwoływać się do niego jako miejsca docelowego przekazywania.

Aby wygenerować zapisywalny sygnaturę dostępu współdzielonego pustego dysku zarządzanego, zastąp elementy `<yourdiskname>` i , a następnie użyj `<yourresourcegroupname>` następującego polecenia:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Przykładowa zwrócona wartość:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego polecenia przekazywania.

Użyj narzędzia AzCopy w wersji 10, aby przekazać lokalny plik VHD na dysk zarządzany, określając wygenerowany przez Ciebie adres URI sygnatury dostępu współdzielonego.

To przekazanie ma taką samą przepływność jak odpowiednik standardowego [dysku twardego](../disks-types.md#standard-hdd). Jeśli na przykład masz rozmiar, który odpowiada S4, będziesz mieć przepływność do 60 MiB/s. Jeśli jednak masz rozmiar, który odpowiada S70, będziesz mieć przepływność do 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po zakończeniu przekazywania i nie trzeba już zapisywać żadnych danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwołanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

Zastąp `<yourdiskname>` elementy i , a następnie użyj `<yourresourcegroupname>` następującego polecenia, aby dysk był użyteczny:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Przekazywanie bezpośrednie upraszcza również proces kopiowania dysku zarządzanego. Możesz skopiować w tym samym regionie lub między regionami (do innego regionu).

Zrobi to za Ciebie za pomocą poniższego skryptu. Proces jest podobny do opisanych wcześniej kroków, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy udostępniasz rozmiar dysku w bajtach dysku zarządzanego z platformy Azure. Wynika to z tego, że platforma Azure pomija stopkę podczas zwracania rozmiaru dysku. Kopiowanie nie powiedzie się, jeśli tego nie zrobisz. Poniższy skrypt już to robi.

Zastąp wartości , , , i (przykład wartości lokalizacji to `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` uswest2) wartościami, a następnie uruchom następujący skrypt, aby skopiować `<yourTargetLocationHere>` dysk zarządzany.

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, dodaj element --hyper-v-generation <yourGeneration> do programu `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym przesłaniu wirtualnego dysku twardego na dysk [](add-disk.md) zarządzany, możesz dołączyć dysk jako dysk danych do istniejącej maszyny wirtualnej lub dołączyć dysk do maszyny wirtualnej jako dysk systemu operacyjnego [,](upload-vhd.md#create-the-vm)aby utworzyć nową maszynę wirtualną.