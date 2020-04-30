---
title: Przekazywanie niestandardowego dysku z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Tworzenie i przekazywanie wirtualnego dysku twardego (VHD) do platformy Azure przy użyciu modelu wdrażania Menedżer zasobów i interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758570"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Przekazywanie i Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób przekazywania wirtualnego dysku twardego (VHD) do konta usługi Azure Storage za pomocą interfejsu wiersza polecenia platformy Azure i tworzenia maszyn wirtualnych z systemem Linux na podstawie tego dysku niestandardowego. Ta funkcja umożliwia zainstalowanie i skonfigurowanie dystrybucji systemu Linux na potrzeby wymagań, a następnie użycie tego wirtualnego dysku twardego do szybkiego tworzenia maszyn wirtualnych platformy Azure.

W tym temacie są używane konta magazynu dla końcowych dysków VHD, ale te kroki można również wykonać przy użyciu usługi [Managed disks](upload-vhd.md). 

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli musisz szybko wykonać zadanie, w poniższej sekcji przedstawiono podstawowe polecenia umożliwiające przekazanie dysku VHD do platformy Azure. Więcej szczegółowych informacji i kontekstu dla każdego kroku można znaleźć w pozostałej części dokumentu, [Zaczynając od tego miejsca](#requirements).

Upewnij się, że masz zainstalowaną najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się na koncie platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów `myResourceGroup`, `mystorageaccount`i. `mydisks`

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Utwórz konto magazynu do przechowywania dysków wirtualnych za pomocą [AZ Storage account Create](/cli/azure/storage/account). Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Wyświetl listę kluczy dostępu dla konta magazynu za pomocą funkcji [AZ Storage account Keys list](/cli/azure/storage/account/keys). Zanotuj `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Utwórz kontener na koncie magazynu przy użyciu klucza magazynu uzyskanego za pomocą polecenia [AZ Storage Container Create](/cli/azure/storage/container). Poniższy przykład tworzy kontener o nazwie `mydisks` przy użyciu wartości klucza magazynu z: `key1`

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Na koniec Przekaż swój wirtualny dysk twardy do kontenera utworzonego za pomocą [AZ Storage BLOB upload](/cli/azure/storage/blob). Określ ścieżkę lokalną do dysku VHD w obszarze `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Określ identyfikator URI dysku (`--image`) za pomocą [AZ VM Create](/cli/azure/vm). Poniższy przykład tworzy MASZYNę wirtualną o `myVM` nazwie przy użyciu wcześniej przekazanego dysku wirtualnego:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Docelowe konto magazynu musi być takie samo, jak miejsce przekazania dysku wirtualnego. Należy również określić lub odpowiedzieć na pytanie, wszystkie dodatkowe parametry wymagane przez polecenie **AZ VM Create** , takie jak sieć wirtualna, publiczny adres IP, nazwa użytkownika i klucze SSH. Więcej informacji na temat [dostępnych klasycznych parametrów Menedżer zasobów interfejsu wiersza polecenia](../azure-cli-arm-commands.md#virtual-machines)można znaleźć w artykule.

## <a name="requirements"></a>Wymagania
Aby wykonać następujące kroki, potrzebne są:

* **System operacyjny Linux zainstalowany w pliku VHD** — zainstaluj [dystrybucję systemu Linux z zatwierdzona przez platformę Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (lub zapoznaj się z [informacjami dotyczącymi niezatwierdzonych dystrybucji](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) do dysku wirtualnego w formacie VHD. Istnieje wiele narzędzi do utworzenia maszyny wirtualnej i wirtualnego dysku twardego:
  * Zainstaluj i skonfiguruj [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) lub [KVM](https://www.linux-kvm.org/page/RunningKVM), pamiętając o użyciu dysku VHD jako formatu obrazu. W razie konieczności można [skonwertować obraz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) przy użyciu polecenia `qemu-img convert`.
  * Można również użyć funkcji Hyper-V [w systemie Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) lub [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nowszy format VHDX nie jest obsługiwany na platformie Azure. Podczas tworzenia maszyny wirtualnej Określ plik VHD jako format. W razie potrzeby można skonwertować dyski VHDX na dysk VHD przy [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) użyciu polecenia [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet programu lub programu PowerShell. Ponadto platforma Azure nie obsługuje przekazywania dynamicznych dysków VHD, dlatego przed przekazaniem należy przekonwertować takie dyski na statyczne wirtualne dyski twarde. Możesz użyć narzędzi, takich jak [Narzędzia wirtualnego dysku twardego platformy Azure](https://github.com/Microsoft/azure-vhd-utils-for-go) , aby przekonwertować dyski dynamiczne podczas procesu przekazywania na platformę Azure.
> 
> 

* Maszyny wirtualne utworzone na podstawie dysku niestandardowego muszą znajdować się na tym samym koncie magazynu co dysk
  * Tworzenie konta magazynu i kontenera do przechowywania zarówno dysku niestandardowego, jak i utworzonych maszyn wirtualnych
  * Po utworzeniu wszystkich maszyn wirtualnych możesz bezpiecznie usunąć dysk

Upewnij się, że masz zainstalowaną najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się na koncie platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów `myResourceGroup`, `mystorageaccount`i. `mydisks`

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Przygotuj dysk do przekazania
Platforma Azure obsługuje różne dystrybucje systemu Linux (zobacz [rozpowszechniane dystrybucje](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Poniższe artykuły przeprowadzą Cię przez proces przygotowywania różnych dystrybucji systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje oparte na CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Dystrybucje inne niż niezatwierdzone](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zapoznaj się również z **[informacjami o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes)** , aby uzyskać bardziej ogólne porady dotyczące przygotowywania obrazów z systemem Linux dla platformy Azure.

> [!NOTE]
> [Umowa SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) ma zastosowanie do maszyn wirtualnych z systemem Linux tylko wtedy, gdy jedna z wydawanych dystrybucji jest używana wraz ze szczegółami konfiguracji określonymi w sekcji "obsługiwane wersje" w [systemie Linux w przypadku dystrybucji potwierdzonych przez platformę Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupy zasobów logicznie łączą wszystkie zasoby platformy Azure w celu obsługi maszyn wirtualnych, takich jak wirtualne sieci i magazyn. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie grup zasobów](../../azure-resource-manager/management/overview.md). Przed przekazaniem niestandardowego dysku i utworzeniem maszyn wirtualnych należy najpierw utworzyć grupę zasobów za pomocą [AZ Group Create](/cli/azure/group).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu dla niestandardowego dysku i maszyn wirtualnych za pomocą [AZ Storage account Create](/cli/azure/storage/account). Wszystkie maszyny wirtualne z dyskami niezarządzanymi, które tworzysz z dysku niestandardowego, muszą znajdować się na tym samym koncie magazynu co ten dysk. 

Poniższy przykład tworzy konto magazynu o nazwie `mystorageaccount` w utworzonej wcześniej grupie zasobów:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Wyświetl listę kluczy konta magazynu
Na platformie Azure generowane są 2 512-bitowe klucze dostępu dla każdego konta magazynu. Te klucze dostępu są używane podczas uwierzytelniania na koncie magazynu, na przykład w celu wykonania operacji zapisu. Aby uzyskać więcej informacji na temat kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md). Możesz wyświetlić klawisze dostępu za pomocą [AZ Storage account Keys list](/cli/azure/storage/account/keys).

Wyświetl klucze dostępu dla utworzonego konta magazynu:

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

Zanotuj `key1` , jak będziesz używać go do współpracy z kontem magazynu w następnych krokach.

## <a name="create-a-storage-container"></a>Tworzenie kontenera magazynu
W taki sam sposób, w jaki tworzysz różne katalogi do logicznego organizowania lokalnego systemu plików, tworzysz kontenery w ramach konta magazynu w celu zorganizowania dysków. Konto magazynu może zawierać dowolną liczbę kontenerów. Utwórz kontener za pomocą [AZ Storage Container Create](/cli/azure/storage/container).

Poniższy przykład tworzy kontener o nazwie `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego
Teraz Przekaż dysk niestandardowy za pomocą [AZ Storage BLOB upload](/cli/azure/storage/blob). Dysk niestandardowy można przekazać i przechowywać jako obiekt BLOB strony.

Określ klucz dostępu, kontener utworzony w poprzednim kroku, a następnie ścieżkę do dysku niestandardowego na komputerze lokalnym:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Aby utworzyć maszynę wirtualną z dyskami niezarządzanymi, określ identyfikator URI`--image`dysku () za pomocą polecenie [AZ VM Create](/cli/azure/vm). Poniższy przykład tworzy MASZYNę wirtualną o `myVM` nazwie przy użyciu wcześniej przekazanego dysku wirtualnego:

Należy określić `--image` parametr [AZ VM Create](/cli/azure/vm) , aby wskazywał na dysk niestandardowy. Upewnij się `--storage-account` , że jest zgodna z kontem magazynu, w którym jest przechowywany dysk niestandardowy. Nie jest konieczne używanie tego samego kontenera co dysk niestandardowy do przechowywania maszyn wirtualnych. Upewnij się, że wszystkie dodatkowe kontenery zostały utworzone w taki sam sposób jak w poprzednich krokach przed przekazaniem dysku niestandardowego.

Poniższy przykład tworzy maszynę wirtualną o `myVM` nazwie z dysku niestandardowego:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Nadal trzeba określić lub odpowiedzieć na pytanie, wszystkie dodatkowe parametry wymagane przez polecenie **AZ VM Create** , takie jak nazwa użytkownika i klucze SSH.


## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Szablony Azure Resource Manager są plikami JavaScript Object Notation (JSON), które definiują środowisko, które chcesz skompilować. Szablony są podzielone na różne dostawcy zasobów, takie jak obliczenia lub sieć. Możesz użyć istniejących szablonów lub napisać własny. Przeczytaj więcej na temat [używania Menedżer zasobów i szablonów](../../azure-resource-manager/management/overview.md).

W ramach `Microsoft.Compute/virtualMachines` dostawcy szablonu masz `storageProfile` węzeł zawierający szczegóły konfiguracji maszyny wirtualnej. Dwa główne parametry do edycji to `image` `vhd` identyfikatory URI, które wskazują na dysk niestandardowy i nową maszynę wirtualną maszyny wirtualnej. Poniżej przedstawiono przykładowy kod JSON używany do użycia dysku niestandardowego:

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

Możesz użyć [tego istniejącego szablonu, aby utworzyć maszynę wirtualną na podstawie niestandardowego obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) lub przeczytać o [tworzeniu własnych szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). 

Po skonfigurowaniu szablonu Użyj polecenie [AZ Group Deployment Create](/cli/azure/group/deployment) , aby utworzyć maszyny wirtualne. Określ identyfikator URI szablonu JSON z `--template-uri` parametrem:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Jeśli masz plik JSON przechowywany lokalnie na komputerze, możesz użyć tego `--template-file` parametru zamiast tego:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Następne kroki
Po przygotowaniu i przekazaniu niestandardowego dysku wirtualnego możesz przeczytać więcej na temat [używania Menedżer zasobów i szablonów](../../azure-resource-manager/management/overview.md). Możesz również [dodać dysk z danymi](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nowych maszyn wirtualnych. Jeśli masz aplikacje uruchomione na maszynach wirtualnych, do których musisz uzyskać dostęp, pamiętaj, aby [otworzyć porty i punkty końcowe](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

