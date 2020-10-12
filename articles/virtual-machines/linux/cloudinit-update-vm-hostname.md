---
title: Używanie funkcji Cloud-init do ustawiania nazwy hosta dla maszyny wirtualnej z systemem Linux
description: Jak używać funkcji Cloud-init do dostosowywania maszyny wirtualnej z systemem Linux podczas tworzenia przy użyciu interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 85d2772596224a3c4252d950ca6ca8d6af02991b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373661"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Używanie funkcji Cloud-init do ustawiania nazwy hosta dla maszyny wirtualnej z systemem Linux na platformie Azure
W tym artykule pokazano, jak za pomocą usługi [Cloud-init](https://cloudinit.readthedocs.io) skonfigurować konkretną nazwę hosta na maszynie wirtualnej lub w zestawach skalowania maszyn wirtualnych (VMSS) w czasie aprowizacji na platformie Azure. Te skrypty usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ustawianie nazwy hosta przy użyciu funkcji Cloud-init
Domyślnie nazwę hosta jest taka sama jak nazwa maszyny wirtualnej podczas tworzenia nowej maszyny wirtualnej na platformie Azure.  Aby uruchomić skrypt inicjowania usługi Cloud-init, aby zmienić domyślną nazwę hosta podczas tworzenia maszyny wirtualnej na platformie Azure za pomocą funkcji [AZ VM Create](/cli/azure/vm), określ plik Cloud-init z `--custom-data` przełącznikiem.  

Aby zobaczyć proces uaktualniania w akcji, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_hostname.txt* i wklej następującą konfigurację. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_hostname.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że cały plik Cloud-init został poprawnie skopiowany, szczególnie w pierwszym wierszu.  

```yaml
#cloud-config
hostname: myhostname
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm) i określ plik Cloud-init w `--custom-data cloud_init_hostname.txt` następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po utworzeniu interfejsu wiersza polecenia platformy Azure zostaną wyświetlone informacje o maszynie wirtualnej. Używaj protokołu `publicIpAddress` SSH z maszyną wirtualną. Wprowadź własny adres w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby wyświetlić nazwę maszyny wirtualnej, użyj `hostname` polecenia w następujący sposób:

```bash
hostname
```

Maszyna wirtualna powinna zgłosić nazwę hosta jako wartość ustawioną w pliku Cloud-init, jak pokazano w następujących przykładowych danych wyjściowych:

```bash
myhostname
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
