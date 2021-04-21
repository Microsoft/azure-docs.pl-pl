---
title: Przekazywanie lub kopiowanie niestandardowej maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przekazywanie lub kopiowanie dostosowanej maszyny wirtualnej przy użyciu Resource Manager wdrażania i interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759603"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej z systemem Linux na użyciu dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure

<!-- rename to create-vm-specialized -->

W tym artykule przedstawiono sposób przekazywania dostosowanego wirtualnego dysku twardego (VHD) i kopiowania istniejącego wirtualnego dysku twardego na platformie Azure. Nowo utworzony wirtualny dysk twardy jest następnie używany do tworzenia nowych maszyn wirtualnych z systemem Linux. Możesz zainstalować i skonfigurować dystrybucję systemu Linux do swoich wymagań, a następnie użyć tego wirtualnego dysku twardego do utworzenia nowej maszyny wirtualnej platformy Azure.

Aby utworzyć wiele maszyn wirtualnych z niestandardowego dysku, najpierw utwórz obraz z maszyny wirtualnej lub wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [Create a custom image of an Azure VM by using the CLI (Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia).](tutorial-custom-images.md)

Dostępne są dwie opcje tworzenia dysku niestandardowego:
* Przekazywanie wirtualnego dysku twardego
* Kopiowanie istniejącej maszyny wirtualnej platformy Azure


## <a name="requirements"></a>Wymagania
Do wykonania poniższych kroków potrzebne są następujące elementy:

- Maszyna wirtualna z systemem Linux, która została przygotowana do użycia na platformie Azure. W sekcji Przygotowywanie maszyny [wirtualnej](#prepare-the-vm) w tym artykule opisano, jak znaleźć informacje specyficzne dla dystrybucji dotyczące instalowania agenta systemu Linux na platformie Azure (waagent), które są potrzebne do nawiązania połączenia z maszyną wirtualną przy użyciu połączenia SSH.
- Plik VHD z istniejącej dystrybucji systemu [Linux](endorsed-distros.md) zatwierdzonej dla platformy Azure (lub zobacz informacje dotyczące niezaaprobowane dystrybucje [)](create-upload-generic.md)do dysku wirtualnego w formacie VHD. Istnieje wiele narzędzi do tworzenia maszyny wirtualnej i wirtualnego dysku twardego:
  - Zainstaluj i skonfiguruj [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM,](https://www.linux-kvm.org/page/RunningKVM)korzystając z wirtualnego dysku twardego jako formatu obrazu. W razie potrzeby można [przekonwertować obraz za pomocą](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) `qemu-img convert` .
  - Funkcji Hyper-V można również używać [Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) systemie [Windows Server 2012/2012 R2.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

> [!NOTE]
> Nowsze formaty VHDX nie są obsługiwane na platformie Azure. Podczas tworzenia maszyny wirtualnej określ wirtualny dysk twardy jako format. W razie potrzeby można przekonwertować dyski VHDX na dysk VHD za pomocą polecenia cmdlet programu PowerShell [Convert-VHD](/powershell/module/hyper-v/convert-vhd) lub [polecenia cmdlet qemu-img.](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) Platforma Azure nie obsługuje przekazywania dynamicznych dysków VHD, dlatego przed przekazaniem należy przekonwertować takie dyski na statyczne dyski VHD. Podczas przekazywania dysków dynamicznych na platformę Azure można użyć narzędzi, takich jak narzędzia [wirtualnego dysku](https://github.com/Microsoft/azure-vhd-utils-for-go) twardego platformy Azure dla go.
> 
> 


- Upewnij się, że masz zainstalowany najnowszy interfejs wiersza polecenia platformy [Azure](/cli/azure/install-az-cli2) i zalogowano się do konta platformy Azure za pomocą [polecenia az login](/cli/azure/reference-index#az_login).

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami, takimi jak `myResourceGroup` `mystorageaccount` , i `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [Zatwierdzone dystrybucje).](endorsed-distros.md) W poniższych artykułach opisano sposób przygotowywania różnych dystrybucji systemu Linux obsługiwanych na platformie Azure:

* [dystrybucje oparte na systemie CentOS](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES i openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Inne: nieobsługiwane dystrybucje](create-upload-generic.md)

Zobacz też uwagi [dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać bardziej ogólne porady dotyczące przygotowywania obrazów systemu Linux dla platformy Azure.

> [!NOTE]
> Umowa [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jest używana jedna z zatwierdzonych dystrybucji ze szczegółami konfiguracji określonymi w obszarze "Obsługiwane wersje" w systemie Linux w usłudze Azure-Endorsed [Distributions.](endorsed-distros.md)
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opcja 1. Przekazywanie wirtualnego dysku twardego

Teraz możesz przekazać wirtualny dysk twardy bezpośrednio na dysk zarządzany. Aby uzyskać instrukcje, [zobacz Przekazywanie wirtualnego dysku twardego na platformę Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](disks-upload-vhd-to-managed-disk-cli.md)

## <a name="option-2-copy-an-existing-vm"></a>Opcja 2. Kopiowanie istniejącej maszyny wirtualnej

Możesz również utworzyć niestandardową maszynę wirtualną na platformie Azure, a następnie skopiować dysk systemu operacyjnego i dołączyć go do nowej maszyny wirtualnej, aby utworzyć kolejną kopię. Jest to dobre narzędzie do testowania, ale jeśli chcesz użyć istniejącej maszyny wirtualnej platformy Azure jako modelu dla wielu nowych maszyn wirtualnych, zamiast tego *utwórz obraz.* Aby uzyskać więcej informacji na temat tworzenia obrazu z istniejącej maszyny wirtualnej platformy Azure, zobacz Create a custom image of an Azure VM by using the CLI (Tworzenie niestandardowego obrazu maszyny wirtualnej platformy [Azure przy użyciu interfejsu wiersza polecenia).](tutorial-custom-images.md)

Jeśli chcesz skopiować istniejącą maszynę wirtualną do innego regionu, możesz użyć narzędzia azcopy do tworzenia kopii dysku [w innym regionie.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

W przeciwnym razie należy utworzyć migawkę maszyny wirtualnej, a następnie utworzyć z migawki nowy dysk VHD systemu operacyjnego.

### <a name="create-a-snapshot"></a>Tworzenie migawki

Ten przykład tworzy migawkę maszyny wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup* i tworzy migawkę *osDiskSnapshot.*

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

Utwórz dysk zarządzany. W tym przykładzie utworzymy dysk zarządzany o nazwie *myManagedDisk* z migawki, gdzie dysk znajduje się w magazynie w standardowych rozmiarach i rozmiarze 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą narzędzia [az vm create](/cli/azure/vm#az_vm_create) i dołącz (--attach-os-disk) dysk zarządzany jako dysk systemu operacyjnego. Poniższy przykład umożliwia utworzenie maszyny wirtualnej *o nazwie myNewVM* przy użyciu dysku zarządzanego utworzonego na podstawie przekazanego wirtualnego dysku twardego:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Powinno być możliwe ssh do maszyny wirtualnej przy użyciu poświadczeń ze źródłowej maszyny wirtualnej. 

## <a name="next-steps"></a>Następne kroki
Po przygotowaniu i przesłaniu niestandardowego dysku wirtualnego możesz przeczytać więcej na temat korzystania z Resource Manager [i szablonów](../../azure-resource-manager/management/overview.md). Możesz również dodać [dysk danych do](add-disk.md) nowych maszyn wirtualnych. Jeśli masz aplikacje działające na twoich komputerach wirtualnych, do których musisz uzyskać dostęp, otwórz [porty i punkty końcowe](nsg-quickstart.md).
