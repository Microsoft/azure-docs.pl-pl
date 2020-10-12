---
title: Używanie funkcji Cloud-init na maszynie wirtualnej z systemem Linux na platformie Azure
description: Jak używać funkcji Cloud-init do aktualizowania i instalowania pakietów na maszynie wirtualnej z systemem Linux podczas tworzenia przy użyciu interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 98198a2eb2476138f7794b26c30397798ede32b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373440"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Używanie funkcji Cloud-init do aktualizowania i instalowania pakietów na maszynie wirtualnej z systemem Linux na platformie Azure
W tym artykule pokazano, jak za pomocą usługi [Cloud-init](https://cloudinit.readthedocs.io) aktualizować pakiety na maszynie wirtualnej z systemem Linux lub w zestawach skalowania maszyn wirtualnych w czasie aprowizacji na platformie Azure. Te skrypty usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizowanie maszyny wirtualnej za pomocą funkcji Cloud-init
Ze względów bezpieczeństwa można skonfigurować maszynę wirtualną w celu zastosowania najnowszych aktualizacji podczas pierwszego rozruchu. Ponieważ usługa Cloud-init działa w różnych dystrybucjeach systemu Linux, nie ma potrzeby określania `apt` ani `yum` dla Menedżera pakietów. Zamiast tego należy zdefiniować `package_upgrade` i pozwolić procesowi inicjowania w chmurze ustalić odpowiedni mechanizm do użycia w dystrybucji. Ten przepływ pracy umożliwia korzystanie z tego samego skryptów Cloud-init w programie dystrybucje.

Aby zobaczyć proces uaktualniania w akcji, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_upgrade.txt* i wklej następującą konfigurację. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_upgrade.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że cały plik Cloud-init został poprawnie skopiowany, szczególnie w pierwszym wierszu.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm) i określ plik Cloud-init w `--custom-data cloud_init_upgrade.txt` następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH do publicznego adresu IP maszyny wirtualnej wyświetlanej w danych wyjściowych poprzedniego polecenia. Wprowadź własne **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Uruchom narzędzie do zarządzania pakietami i sprawdź, czy są dostępne aktualizacje.

```bash
sudo yum update
```

Gdy usługa Cloud-init zaznaczono i zainstalowano aktualizacje przy rozruchu, nie powinno być żadnych dodatkowych aktualizacji do zastosowania.  Zobaczysz proces aktualizacji, liczbę zmienionych pakietów oraz instalację programu, `httpd` uruchamiając `yum history` i przeglądając dane wyjściowe podobne do przedstawionego poniżej.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
