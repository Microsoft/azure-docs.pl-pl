---
title: Przekazywanie lub kopiowanie niestandardowej maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przekazywanie lub kopiowanie dostosowanej maszyny wirtualnej przy użyciu modelu wdrażania Menedżer zasobów i interfejsu wiersza polecenia platformy Azure
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564615"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej z systemem Linux na podstawie dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure

<!-- rename to create-vm-specialized -->

W tym artykule pokazano, jak przekazać dostosowany wirtualny dysk twardy (VHD) oraz jak skopiować istniejący dysk VHD na platformie Azure. Nowo utworzony dysk VHD jest następnie używany do tworzenia nowych maszyn wirtualnych z systemem Linux. Aby utworzyć nową maszynę wirtualną platformy Azure, możesz zainstalować i skonfigurować dystrybucji systemu Linux.

Aby utworzyć wiele maszyn wirtualnych z niestandardowego dysku, należy najpierw utworzyć obraz na podstawie maszyny wirtualnej lub wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).

Dostępne są dwie opcje tworzenia dysku niestandardowego:
* Przekazywanie wirtualnego dysku twardego
* Kopiowanie istniejącej maszyny wirtualnej platformy Azure


## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

- Maszyna wirtualna z systemem Linux, która została przygotowana do użycia na platformie Azure. Sekcja [przygotowanie maszyny wirtualnej](#prepare-the-vm) w tym artykule zawiera informacje dotyczące sposobu znalezienia dystrybucji informacji dotyczących instalacji agenta systemu Linux (waagent), który jest wymagany do nawiązania połączenia z maszyną wirtualną przy użyciu protokołu SSH.
- Plik VHD z istniejącej [dystrybucji systemu Linux zaświadczonej przez platformę Azure](endorsed-distros.md) (lub Wyświetl [informacje dotyczące dystrybucji niepotwierdzonych](create-upload-generic.md)) na dysku wirtualnym w formacie VHD. Istnieje wiele narzędzi do utworzenia maszyny wirtualnej i wirtualnego dysku twardego:
  - Zainstaluj i skonfiguruj [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](https://www.linux-kvm.org/page/RunningKVM), pamiętając o użyciu dysku VHD jako formatu obrazu. W razie konieczności można [przekonwertować obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) za pomocą `qemu-img convert` .
  - Można również użyć funkcji Hyper-V [w systemie Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) lub [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwany na platformie Azure. Podczas tworzenia maszyny wirtualnej Określ plik VHD jako format. W razie potrzeby można skonwertować dyski VHDX na dysk VHD przy użyciu narzędzia [QEMU-IMG Convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub polecenia cmdlet [convert-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell. Platforma Azure nie obsługuje przekazywania dynamicznych dysków VHD, dlatego przed przekazaniem konieczne będzie przekonwertowanie takich dysków na statyczne wirtualne dyski twarde. Możesz użyć narzędzi, takich jak [Narzędzia wirtualnego dysku twardego platformy Azure](https://github.com/Microsoft/azure-vhd-utils-for-go) , aby przekonwertować dyski dynamiczne podczas procesu przekazywania ich do platformy Azure.
> 
> 


- Upewnij się, że masz zainstalowaną najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogujesz się do konta platformy Azure przy użyciu [AZ login](/cli/azure/reference-index#az-login).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami, takimi jak `myResourceGroup` , `mystorageaccount` , i `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [rozpowszechniane dystrybucje](endorsed-distros.md)). W poniższych artykułach opisano, jak przygotować różne dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* [dystrybucje oparte na systemie CentOS](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES i openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Inne: dystrybucje niezatwierdzone](create-upload-generic.md)

Zapoznaj się również z [informacjami o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) , aby uzyskać bardziej ogólne porady dotyczące przygotowywania obrazów z systemem Linux dla platformy Azure.

> [!NOTE]
> [Umowa SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jedna z wydawanych dystrybucji jest używana wraz ze szczegółami konfiguracji określonymi w sekcji "obsługiwane wersje" w [systemie Linux na Azure-Endorsed dystrybucji](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opcja 1. przekazywanie wirtualnego dysku twardego

Możesz teraz przekazać wirtualny dysk twardy bezpośrednio do dysku zarządzanego. Aby uzyskać instrukcje, zobacz [przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opcja 2: kopiowanie istniejącej maszyny wirtualnej

Możesz również utworzyć dostosowaną maszynę wirtualną na platformie Azure, a następnie skopiować dysk systemu operacyjnego i dołączyć go do nowej maszyny wirtualnej, aby utworzyć kolejną kopię. Jest to odpowiednie dla testowania, ale jeśli chcesz użyć istniejącej maszyny wirtualnej platformy Azure jako modelu dla wielu nowych maszyn wirtualnych, zamiast tego Utwórz *obraz* . Aby uzyskać więcej informacji na temat tworzenia obrazu z istniejącej maszyny wirtualnej platformy Azure, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).

Jeśli chcesz skopiować istniejącą maszynę wirtualną do innego regionu, możesz użyć AzCopy, aby utworzyć [kopię dysku w innym regionie](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

W przeciwnym razie należy wykonać migawkę maszyny wirtualnej, a następnie utworzyć nowy wirtualny dysk twardy systemu operacyjnego na podstawie migawki.

### <a name="create-a-snapshot"></a>Tworzenie migawki

W tym przykładzie tworzona jest migawka maszyny wirtualnej o nazwie *myVM* *w grupie zasobów* i tworzy migawkę o nazwie *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Tworzenie dysku zarządzanego

Utwórz nowy dysk zarządzany na podstawie migawki.

Pobierz identyfikator migawki. W tym przykładzie migawka ma nazwę *osDiskSnapshot* i znajduje się w *grupie zasobów zasobu* .

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Utwórz dysk zarządzany. W tym przykładzie utworzymy dysk zarządzany o nazwie *myManagedDisk* z naszej migawki, gdzie dysk jest w standardowym magazynie i ma rozmiar 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create) i attach (--Attach-OS-Disk) dysku zarządzanego jako dysk systemu operacyjnego. Poniższy przykład tworzy maszynę wirtualną o nazwie *myNewVM* przy użyciu dysku zarządzanego utworzonego na podstawie przekazanego wirtualnego dysku twardego:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Powinno być możliwe nawiązanie połączenia SSH z maszyną wirtualną z poświadczeniami ze źródłowej maszyny wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Po przygotowaniu i przekazaniu niestandardowego dysku wirtualnego możesz przeczytać więcej na temat [używania Menedżer zasobów i szablonów](../../azure-resource-manager/management/overview.md). Możesz również [dodać dysk z danymi](add-disk.md) do nowych maszyn wirtualnych. Jeśli masz aplikacje uruchomione na maszynach wirtualnych, do których musisz uzyskać dostęp, pamiętaj, aby [otworzyć porty i punkty końcowe](nsg-quickstart.md).
