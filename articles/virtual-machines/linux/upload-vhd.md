---
title: Przekazywanie lub kopiowanie niestandardowej maszyny Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure
description: Przekazywanie lub kopiowanie dostosowanej maszyny wirtualnej przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3306647078c46a7c66b3d7b257b213c7a48e690d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460430"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Tworzenie maszyny Wirtualnej systemu Linux na dysku niestandardowym za pomocą interfejsu wiersza polecenia platformy Azure

<!-- rename to create-vm-specialized -->

W tym artykule pokazano, jak przekazać dostosowany wirtualny dysk twardy (VHD) i jak skopiować istniejący dysk wirtualny na platformie Azure. Nowo utworzony dysk wirtualny jest następnie używany do tworzenia nowych maszyn wirtualnych systemu Linux (VM). Można zainstalować i skonfigurować dystrybucji systemu Linux do wymagań, a następnie użyć tego dysku wirtualnego do utworzenia nowej maszyny wirtualnej platformy Azure.

Aby utworzyć wiele maszyn wirtualnych z dostosowanego dysku, należy najpierw utworzyć obraz z maszyny wirtualnej lub wirtualnej rzeczywistości wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia.](tutorial-custom-images.md)

Dostępne są dwie opcje utworzenia dysku niestandardowego:
* Przekazywanie wirtualnego dysku twardego
* Kopiowanie istniejącej maszyny Wirtualnej platformy Azure


## <a name="requirements"></a>Wymagania
Aby wykonać następujące czynności, musisz:

- Maszyna wirtualna systemu Linux, która została przygotowana do użycia na platformie Azure. W sekcji [Przygotowanie maszyny Wirtualnej](#prepare-the-vm) w tym artykule opisano, jak znaleźć informacje specyficzne dla dystrybucji dotyczące instalowania agenta systemu Azure Linux (waagent), który jest potrzebny do nawiązania połączenia z maszyną wirtualną za pomocą SSH.
- Plik VHD z istniejącej [dystrybucji linuksa zatwierdzonej przez platformę Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub [zobacz informacje dotyczące nieobserwowanych dystrybucji) na](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)dysk wirtualny w formacie VHD. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i VHD:
  - Zainstaluj i skonfiguruj [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM,](https://www.linux-kvm.org/page/RunningKVM)dbając o użycie VHD jako formatu obrazu. W razie potrzeby można [przekonwertować obraz za](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocą pliku `qemu-img convert`.
  - Funkcji Hyper-V można również używać [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwany na platformie Azure. Podczas tworzenia maszyny Wirtualnej należy określić dysk VHD jako format. W razie potrzeby można przekonwertować dyski VHDX na VHD z [konwersją qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) lub poleceniem cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell. Platforma Azure nie obsługuje przekazywania dynamicznych dysków wirtualnych, więc przed przekazaniem należy przekonwertować takie dyski na statyczne dyski wirtualne. Za pomocą narzędzi, takich jak [narzędzia Azure VHD Utilities for GO,](https://github.com/Microsoft/azure-vhd-utils-for-go) można konwertować dyski dynamiczne podczas przekazywania ich na platformę Azure.
> 
> 


- Upewnij się, że masz zainstalowaną najnowszą [platformę Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się do konta platformy Azure za pomocą [logowania az.](/cli/azure/reference-index#az-login)

W poniższych przykładach zastąp przykładowe nazwy `myResourceGroup` `mystorageaccount`parametrów `mydisks`własnymi wartościami, takimi jak , i .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [Zatwierdzone dystrybucje).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) W poniższych artykułach opisano sposób przygotowania różnych dystrybucji systemu Linux, które są obsługiwane na platformie Azure:

* [dystrybucje oparte na systemie CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES i openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Inne: Dystrybucje niezdowolone](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zobacz również [uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać bardziej ogólne wskazówki dotyczące przygotowywania obrazów systemu Linux na platformę Azure.

> [!NOTE]
> Umowa [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jedna z zatwierdzonych dystrybucji jest używana ze szczegółami konfiguracji określonymi w sekcji "Obsługiwane wersje" w systemie Linux w [dystrybucjach zatwierdzonych przez platformę Azure.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opcja 1: Przesyłanie dysku VHD

Teraz można przesłać dysk VHD bezpośrednio na dysk zarządzany. Aby uzyskać instrukcje, zobacz [Przekazywanie dysku VHD na platformę Azure przy użyciu interfejsu wiersza polecenia platformy Azure](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opcja 2: Kopiowanie istniejącej maszyny Wirtualnej

Można również utworzyć dostosowaną maszynę wirtualną na platformie Azure, a następnie skopiować dysk systemu operacyjnego i dołączyć ją do nowej maszyny Wirtualnej, aby utworzyć inną kopię. Jest to dobrze dla testowania, ale jeśli chcesz użyć istniejącej maszyny Wirtualnej platformy Azure jako modelu dla wielu nowych maszyn wirtualnych, zamiast tego utwórz *obraz.* Aby uzyskać więcej informacji na temat tworzenia obrazu z istniejącej maszyny Wirtualnej platformy Azure, zobacz [Tworzenie niestandardowego obrazu maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia.](tutorial-custom-images.md)

Jeśli chcesz skopiować istniejącą maszynę wirtualną do innego regionu, możesz użyć azcopy do [utworzenia kopii dysku w innym regionie](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

W przeciwnym razie należy zrobić migawkę maszyny Wirtualnej, a następnie utworzyć nowy dysk wirtualny systemu operacyjnego z migawki.

### <a name="create-a-snapshot"></a>Tworzenie migawki

W tym przykładzie tworzy migawkę maszyny Wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup* i tworzy migawkę o nazwie *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Tworzenie dysku zarządzanego

Utwórz nowy dysk zarządzany z migawki.

Pobierz identyfikator migawki. W tym przykładzie migawka nosi nazwę *osDiskSnapshot* i znajduje się w grupie zasobów *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Utwórz dysk zarządzany. W tym przykładzie utworzymy dysk zarządzany o nazwie *myManagedDisk* z naszej migawki, gdzie dysk znajduje się w standardowej pamięci masowej i ma rozmiar 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [dysku zarządzanego az vm](/cli/azure/vm#az-vm-create) i dołącz (--attach-os-disk) jako dysk systemu operacyjnego. Poniższy przykład tworzy maszynę wirtualną o nazwie *myNewVM* przy użyciu dysku zarządzanego utworzonego na podstawie przekazanej sieci VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Powinno być możliwe SSH do maszyny Wirtualnej z poświadczeniami ze źródłowej maszyny Wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Po przygotowaniu i przesłaniu niestandardowego dysku wirtualnego można przeczytać więcej na temat [korzystania z Menedżera zasobów i szablonów](../../azure-resource-manager/management/overview.md). Można również [dodać dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli na maszynach wirtualnych są uruchomione aplikacje, do których musisz uzyskać dostęp, należy [otworzyć porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
