---
title: Dodawanie użytkownika do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu funkcji Cloud-init
description: Jak używać funkcji Cloud-init do dodawania użytkownika do maszyny wirtualnej z systemem Linux podczas tworzenia przy użyciu interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559413"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Dodawanie użytkownika do maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu funkcji Cloud-init
W tym artykule pokazano, jak za pomocą funkcji [Cloud-init](https://cloudinit.readthedocs.io) dodać użytkownika do maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych (VMSS) w czasie aprowizacji na platformie Azure. Ten skrypt usługi Cloud-init jest uruchamiany podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Dodawanie użytkownika do maszyny wirtualnej przy użyciu funkcji Cloud-init
Jednym z pierwszych zadań na każdej nowej maszynie wirtualnej z systemem Linux jest dodanie dodatkowego użytkownika w celu uniknięcia korzystania z *katalogu głównego*. Klucze SSH są najlepszym rozwiązaniem w zakresie bezpieczeństwa i użyteczności. Klucze są dodawane do pliku *~/.ssh/authorized_keys* za pomocą tego skryptu Cloud-init.

Aby dodać użytkownika do maszyny wirtualnej z systemem Linux, Utwórz plik w bieżącej powłoce o nazwie *cloud_init_add_user.txt* i wklej następującą konfigurację. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud_init_add_user.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że cały plik Cloud-init został poprawnie skopiowany, szczególnie w pierwszym wierszu.  Musisz podać własny klucz publiczny (na przykład zawartość *~/.ssh/id_rsa. pub*) dla wartości parametru `ssh-authorized-keys:` -została skrócona w tym miejscu, aby uprościć przykład.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Plik #cloud-config zawiera `- default` dołączony parametr. Spowoduje to dołączenie użytkownika do istniejącego użytkownika administracyjnego utworzonego podczas aprowizacji. Jeśli użytkownik zostanie utworzony bez `- default` parametru — automatycznie wygenerowany użytkownik administracyjny utworzony przez platformę Azure zostanie zastąpiony. 

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm) i określ plik Cloud-init w `--custom-data cloud_init_add_user.txt` następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH do publicznego adresu IP maszyny wirtualnej wyświetlanej w danych wyjściowych poprzedniego polecenia. Wprowadź własne **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Aby potwierdzić, że użytkownik został dodany do maszyny wirtualnej i określonych grup, Wyświetl zawartość pliku */etc/Group* w następujący sposób:

```bash
cat /etc/group
```

Następujące przykładowe dane wyjściowe pokazują, że użytkownik z pliku *cloud_init_add_user.txt* został dodany do maszyny wirtualnej i z odpowiednią grupą:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
