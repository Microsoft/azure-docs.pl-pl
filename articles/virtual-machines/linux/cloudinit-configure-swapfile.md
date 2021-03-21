---
title: Konfigurowanie partycji wymiany na maszynie wirtualnej z systemem Linux przy użyciu funkcji Cloud-init
description: Jak używać funkcji Cloud-init do konfigurowania partycji wymiany na maszynie wirtualnej z systemem Linux podczas tworzenia przy użyciu interfejsu wiersza polecenia platformy Azure
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559379"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Konfigurowanie partycji wymiany na maszynie wirtualnej z systemem Linux przy użyciu funkcji Cloud-init
W tym artykule przedstawiono sposób użycia usługi [Cloud-init](https://cloudinit.readthedocs.io) w celu skonfigurowania partycji wymiany dla różnych dystrybucji systemu Linux. Partycja wymiany jest tradycyjnie konfigurowana przez agenta systemu Linux (WALA) na podstawie tego, które z nich są wymagane.  W tym dokumencie opisano proces tworzenia partycji wymiany na żądanie podczas aprowizacji czasu przy użyciu funkcji Cloud-init.  Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Utwórz partycję wymiany dla obrazów opartych na Ubuntu
Domyślnie na platformie Azure obrazy galerii Ubuntu nie tworzą partycji wymiany. Aby włączyć konfigurację partycji wymiany podczas udostępniania maszyny wirtualnej za pomocą funkcji Cloud-init — zobacz [dokument AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) w witrynie typu wiki Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Utwórz partycję wymiany dla obrazów opartych na Red Hat i CentOS

Utwórz plik w bieżącej powłoce o nazwie *cloud_init_swappart.txt* i wklej następującą konfigurację. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_swappart.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że cały plik Cloud-init został poprawnie skopiowany, szczególnie w pierwszym wierszu.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm) i określ plik Cloud-init w `--custom-data cloud_init_swappart.txt` następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Weryfikowanie tworzenia partycji wymiany
SSH do publicznego adresu IP maszyny wirtualnej wyświetlanej w danych wyjściowych poprzedniego polecenia. Wprowadź własne **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Po SSH'ed na maszynę wirtualną Sprawdź, czy została utworzona partycja wymiany

```bash
swapon -s
```

Dane wyjściowe tego polecenia powinny wyglądać następująco:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Jeśli masz skonfigurowany obraz platformy Azure, który ma skonfigurowaną partycję wymiany i chcesz zmienić konfigurację partycji wymiany dla nowych obrazów, należy usunąć istniejącą partycję wymiany. Aby uzyskać więcej informacji, zobacz sekcję "Dostosowywanie obrazów do aprowizacji przez funkcję Cloud-init".

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
