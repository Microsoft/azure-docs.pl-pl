---
title: Przekaż dysk VHD do platformy Azure lub skopiuj go między regionami — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać dysk VHD do dysku zarządzanego platformy Azure i skopiować dysk zarządzany między regionami przy użyciu interfejsu wiersza polecenia platformy Azure za pośrednictwem bezpośredniego przekazywania.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978989"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Przekazywanie dysku VHD na platformę Azure lub kopiowanie dysku zarządzanego do innego regionu — interfejs wiersza polecenia platformy Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Jeśli zamierzasz przekazać dysk VHD ze swojego miejsca lokalnego: dysk VHD o stałym rozmiarze [przygotowany dla platformy Azure](../windows/prepare-for-upload-vhd-image.md), przechowywany lokalnie.
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

Aby można było utworzyć pusty dysk twardy w celu przeładowania, potrzebny będzie rozmiar pliku wirtualnego dysku twardego, który ma zostać przekazany w bajtach. Aby to zrobić, możesz użyć albo `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd` . Ta wartość jest używana podczas określania parametru **--upload-size-Bytes** .

Tworzenie pustego standardowego dysku twardego do przekazywania przez określenie zarówno parametru **--for-upload** , jak i parametru **--upload-size-Bytes** w poleceniu cmdlet [Create Disk](/cli/azure/disk#az-disk-create) :

Zamień na wybrane `<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` wartości. `--upload-size-bytes`Parametr zawiera przykładową wartość `34359738880` , zastępuje ją odpowiednią wartością.

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, Dodaj funkcję--Hyper-v-Generation <yourGeneration> do programu `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przekazać dysk SSD w warstwie Premium lub dysk SSD w warstwie Standardowa, Zastąp **standard_lrs** z **premium_LRS** lub **standardssd_lrs**. Dyski Ultra nie są obecnie obsługiwane.

Teraz, gdy został utworzony pusty dysk zarządzany skonfigurowany dla procesu przekazywania, można przekazać do niego dysk VHD. Aby przekazać dysk VHD do dysku, musisz mieć zapisywalne sygnatury dostępu współdzielonego, aby można było odwołać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalne SYGNATURy dostępu do pustego dysku zarządzanego, Zastąp `<yourdiskname>` je, a `<yourresourcegroupname>` następnie użyj następującego polecenia:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Przykładowa zwracana wartość:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego dla polecenia przekazywania.

Za pomocą AzCopy v10 Przekaż swój lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu współdzielonego.

To przekazywanie ma taką samą przepływność jak odpowiednik [standardowego dysku twardego](../disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który jest równa S4, będziesz mieć przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który jest równa S70, będziesz mieć przepływność do 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po zakończeniu przekazywania i nie musisz już pisać więcej danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwoływanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

Zamień `<yourdiskname>` i `<yourresourcegroupname>` , a następnie użyj następującego polecenia, aby użyć dysku:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można wykonać kopię w tym samym regionie lub w różnych regionach (do innego regionu).

Wykonanie poniższej skryptu spowoduje to, że proces jest podobny do opisanego wcześniej procedury, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy podajesz rozmiar dysku w bajtach dysku zarządzanego na platformie Azure. Wynika to z faktu, że platforma Azure pomija stopkę podczas zwracania rozmiaru dysku. Kopia nie powiedzie się, jeśli to zrobisz. Poniższy skrypt już robi to za Ciebie.

Zastąp `<sourceResourceGroupHere>` wartości,, `<sourceDiskNameHere>` `<targetDiskNameHere>` , `<targetResourceGroupHere>` i `<yourTargetLocationHere>` (przykładem wartości lokalizacji byłaby uswest2) wartościami, a następnie uruchom poniższy skrypt w celu skopiowania dysku zarządzanego.

> [!TIP]
> Jeśli tworzysz dysk systemu operacyjnego, Dodaj funkcję--Hyper-v-Generation <yourGeneration> do programu `az disk create` .

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

Teraz, gdy wirtualny dysk twardy został pomyślnie przekazany do dysku zarządzanego, możesz dołączyć dysk jako [dysk danych do istniejącej maszyny wirtualnej](add-disk.md) lub [dołączyć dysk do maszyny wirtualnej jako dysk systemu operacyjnego](upload-vhd.md#create-the-vm), aby utworzyć nową maszynę wirtualną.