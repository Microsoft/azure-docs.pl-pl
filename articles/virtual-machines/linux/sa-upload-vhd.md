---
title: Przekazywanie niestandardowego dysku systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure
description: Tworzenie i przekazywanie wirtualnego dysku twardego (VHD) na platformę Azure przy użyciu modelu wdrażania usługi Resource Manager i interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758570"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Przekazywanie i tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak przekazać wirtualny dysk twardy (VHD) do konta magazynu platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure i utworzyć maszyny wirtualne z systemem Linux z tego dysku niestandardowego. Ta funkcja umożliwia zainstalowanie i skonfigurowanie dystrybucji systemu Linux zgodnie z wymaganiami, a następnie użycie tej pamięci VHD do szybkiego tworzenia maszyn wirtualnych platformy Azure.This functionality allows you to install and configure a Linux distro to your requirements and then use that VHD to quickly create Azure virtual machines (VMs).

W tym temacie użyto kont magazynu dla końcowych dysków VHD, ale można również wykonać te kroki przy użyciu [dysków zarządzanych](upload-vhd.md). 

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli musisz szybko wykonać zadanie, w poniższej sekcji szczegóły poleceń podstawowych do przekazania dysku VHD na platformę Azure. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć resztę dokumentu, [zaczynając tutaj](#requirements).

Upewnij się, że masz zainstalowaną i zalogowaną najnowszą [platformę Azure CLI](/cli/azure/install-az-cli2) przy użyciu [logowania az.](/cli/azure/reference-index)

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy `myResourceGroup`parametrów zawarte , `mystorageaccount`i `mydisks`.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Utwórz konto magazynu, aby pomieścić dyski wirtualne przy [tworzeniu konta magazynu az](/cli/azure/storage/account). Poniższy przykład tworzy konto `mystorageaccount`magazynu o nazwie:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Wyświetl listę kluczy dostępu do konta magazynu z [listą kluczy konta magazynu az](/cli/azure/storage/account/keys). `key1`Zanotuj:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Utwórz kontener na koncie magazynu przy użyciu klucza magazynu uzyskanego za pomocą [kontenera magazynu AZ create](/cli/azure/storage/container). Poniższy przykład tworzy `mydisks` kontener o nazwie `key1`przy użyciu wartości klucza magazynu z:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Na koniec prześlij dysk VHD do kontenera utworzonego za pomocą [az storage blob upload](/cli/azure/storage/blob). Określ ścieżkę lokalną do `/path/to/disk/mydisk.vhd`dysku VHD w obszarze:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Określ identyfikator URI`--image`na dysku ( ) za pomocą [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` przy użyciu wcześniej przekazanego dysku wirtualnego:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Docelowe konto magazynu musi być takie samo, jak w przypadku przekazania dysku wirtualnego. Należy również określić lub odpowiedzieć na monity, wszystkie dodatkowe parametry wymagane przez **az vm utworzyć** polecenia, takie jak sieć wirtualna, publiczny adres IP, nazwa użytkownika i klucze SSH. Więcej informacji na temat [dostępnych parametrów klasycznego menedżera zasobów interfejsu wiersza polecenia](../azure-cli-arm-commands.md#virtual-machines)można znaleźć.

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, należy:

* **System operacyjny Linux zainstalowany w pliku vhd** - Zainstaluj [dystrybucję Linuksa zatwierdzoną przez platformę Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zobacz [informacje dotyczące nieobserwowanych dystrybucji) na](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)dysku wirtualnym w formacie VHD. Istnieje wiele narzędzi do tworzenia maszyny Wirtualnej i VHD:
  * Zainstaluj i skonfiguruj [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM,](https://www.linux-kvm.org/page/RunningKVM)dbając o użycie VHD jako formatu obrazu. W razie potrzeby można [przekonwertować obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) za pomocą programu `qemu-img convert`.
  * Funkcji Hyper-V można również używać [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwany na platformie Azure. Podczas tworzenia maszyny Wirtualnej należy określić dysk VHD jako format. W razie potrzeby można przekonwertować dyski [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) VHDX na dyski VHD za pomocą polecenia cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) programu PowerShell. Ponadto platforma Azure nie obsługuje przekazywania dynamicznych dysków wirtualnych, więc przed przekazaniem należy przekonwertować takie dyski na statyczne dyski wirtualne. Za pomocą narzędzi, takich jak [narzędzia Azure VHD Utilities for GO,](https://github.com/Microsoft/azure-vhd-utils-for-go) można konwertować dyski dynamiczne podczas procesu przekazywania na platformę Azure.
> 
> 

* Maszyny wirtualne utworzone na dysku niestandardowym muszą znajdować się na tym samym koncie magazynu co sam dysk
  * Tworzenie konta magazynu i kontenera do przechowywania zarówno dysku niestandardowego, jak i utworzonych maszyn wirtualnych
  * Po utworzeniu wszystkich maszyn wirtualnych można bezpiecznie usunąć dysk

Upewnij się, że masz zainstalowaną i zalogowaną najnowszą [platformę Azure CLI](/cli/azure/install-az-cli2) przy użyciu [logowania az.](/cli/azure/reference-index)

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy `myResourceGroup`parametrów zawarte , `mystorageaccount`i `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Przygotowanie dysku do przesłania
Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [Zatwierdzone dystrybucje).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Poniższe artykuły prowadzą użytkownika przez sposób przygotowania różnych dystrybucji systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje oparte na centos](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Inne - Dystrybucje niezdowolone](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zobacz również **[uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes)** aby uzyskać bardziej ogólne wskazówki dotyczące przygotowywania obrazów systemu Linux na platformę Azure.

> [!NOTE]
> Umowa [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jedna z zatwierdzonych dystrybucji jest używana ze szczegółami konfiguracji określonymi w sekcji "Obsługiwane wersje" w systemie Linux w [dystrybucjach zatwierdzonych przez platformę Azure.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupy zasobów logicznie łączą wszystkie zasoby platformy Azure w celu obsługi maszyn wirtualnych, takich jak sieci wirtualne i magazynu. Aby uzyskać więcej informacji o grupach zasobów, zobacz [omówienie grup zasobów](../../azure-resource-manager/management/overview.md). Przed przesłaniem dysku niestandardowego i utworzeniem maszyn wirtualnych należy najpierw utworzyć grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu dla dysku niestandardowego i maszyn wirtualnych z [kontem magazynu az utworzyć](/cli/azure/storage/account). Wszystkie maszyny wirtualne z dyskami niezarządzanymi utworzonymi z dysku niestandardowego muszą znajdować się na tym samym koncie magazynu co ten dysk. 

Poniższy przykład tworzy konto `mystorageaccount` magazynu o nazwie w grupie zasobów wcześniej utworzonej:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista kluczy konta magazynu
Platforma Azure generuje dwa 512-bitowe klucze dostępu dla każdego konta magazynu. Te klucze dostępu są używane podczas uwierzytelniania na koncie magazynu, na przykład do wykonywania operacji zapisu. Aby uzyskać więcej informacji na temat kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md). Można wyświetlić klucze dostępu z [listy kluczy konta magazynu az](/cli/azure/storage/account/keys).

Służy do wyświetlania kluczy dostępu dla utworzonego konta magazynu:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Dane wyjściowe są podobne do następujących:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Zanotuj, `key1` jak będzie go używać do interakcji z kontem magazynu w następnych krokach.

## <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
W ten sam sposób, w jaki tworzysz różne katalogi, aby logicznie zorganizować lokalny system plików, należy utworzyć kontenery w ramach konta magazynu w celu zorganizowania dysków. Konto magazynu może zawierać dowolną liczbę kontenerów. Utwórz kontener z [utworzonym kontenerem magazynu az](/cli/azure/storage/container).

Poniższy przykład tworzy `mydisks`kontener o nazwie:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Prześlij VHD
Teraz prześlij swój dysk niestandardowy z [az storage blob upload](/cli/azure/storage/blob). Przekaż i zapisz dysk niestandardowy jako obiekt blob strony.

Określ klucz dostępu, kontener utworzony w poprzednim kroku, a następnie ścieżkę do dysku niestandardowego na komputerze lokalnym:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Aby utworzyć maszynę wirtualną z dyskami niezarządzanymi,`--image`określ identyfikator URI na dysku ( ) za pomocą [programu az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` przy użyciu wcześniej przekazanego dysku wirtualnego:

Należy określić parametr z `--image` [az vm utworzyć,](/cli/azure/vm) aby wskazać na dysku niestandardowym. Upewnij `--storage-account` się, że pasuje do konta magazynu, na którym jest przechowywany dysk niestandardowy. Nie trzeba używać tego samego kontenera co dysk niestandardowy do przechowywania maszyn wirtualnych. Upewnij się, że wszystkie dodatkowe kontenery w taki sam sposób, jak wcześniejsze kroki przed przekazaniem dysku niestandardowego.

Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` z dysku niestandardowego:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Nadal należy określić lub odpowiedzieć na monity, wszystkie dodatkowe parametry wymagane przez **az vm utworzyć** polecenia, takie jak nazwa użytkownika i klucze SSH.


## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony usługi Azure Resource Manager to pliki notacji obiektów JavaScript (JSON), które definiują środowisko, które chcesz utworzyć. Szablony są podzielone na różnych dostawców zasobów, takich jak obliczeń lub sieci. Można użyć istniejących szablonów lub napisać własne. Dowiedz się więcej o [korzystaniu z Menedżera zasobów i szablonów](../../azure-resource-manager/management/overview.md).

W `Microsoft.Compute/virtualMachines` ramach dostawcy szablonu masz `storageProfile` węzeł, który zawiera szczegóły konfiguracji maszyny Wirtualnej. Dwa główne parametry do `image` edycji są i `vhd` identyfikatory URI, które wskazują na dysk niestandardowy i nowy dysk wirtualny maszyny Wirtualnej. Poniżej przedstawiono przykład JSON do korzystania z dysku niestandardowego:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Za pomocą [tego istniejącego szablonu można utworzyć maszynę wirtualną na podstawie niestandardowego obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) lub przeczytać o tworzeniu własnych [szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). 

Po skonfigurowaniu szablonu użyj [tworzenia wdrożenia grupy AZ,](/cli/azure/group/deployment) aby utworzyć maszyny wirtualne. Określ identyfikator URI szablonu `--template-uri` JSON za pomocą parametru:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Jeśli plik JSON jest przechowywany lokalnie na komputerze, `--template-file` możesz użyć tego parametru:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Następne kroki
Po przygotowaniu i przesłaniu niestandardowego dysku wirtualnego można przeczytać więcej na temat [korzystania z Menedżera zasobów i szablonów](../../azure-resource-manager/management/overview.md). Można również [dodać dysk danych](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli na maszynach wirtualnych są uruchomione aplikacje, do których musisz uzyskać dostęp, należy [otworzyć porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

