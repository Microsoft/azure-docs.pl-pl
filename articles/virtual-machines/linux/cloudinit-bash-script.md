---
title: Uruchamianie skryptu bash na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu funkcji Cloud-init
description: Jak używać funkcji Cloud-init do uruchamiania skryptu bash na maszynie wirtualnej z systemem Linux podczas tworzenia przy użyciu interfejsu wiersza polecenia platformy Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 0370ddeafd1f695292ba122bfebd6197a360f560
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372811"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Uruchamianie skryptu bash na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu funkcji Cloud-init
W tym artykule pokazano, jak za pomocą usługi [Cloud-init](https://cloudinit.readthedocs.io) uruchamiać istniejący skrypt bash na maszynie wirtualnej z systemem Linux lub w zestawach skalowania maszyn wirtualnych (VMSS) w czasie aprowizacji na platformie Azure. Te skrypty usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Uruchamianie skryptu bash przy użyciu funkcji Cloud-init
Przy użyciu funkcji Cloud-init nie trzeba konwertować istniejących skryptów do pliku Cloud-config, ponieważ w chmurze-init są akceptowane wiele typów danych wejściowych, z których jeden jest skrypt bash.

Jeśli do uruchamiania skryptów użyto rozszerzenia Azure Custom Script skrypt w systemie Linux, można je migrować do korzystania z funkcji Cloud-init. Rozszerzenia platformy Azure mają jednak zintegrowane raportowanie do alertów dotyczących błędów skryptów, więc wdrożenie obrazu w chmurze nie powiedzie się, jeśli wystąpi błąd skryptu.

Aby wyświetlić tę funkcję w działaniu, Utwórz prosty skrypt bash na potrzeby testowania. Podobnie jak w przypadku pliku Cloud-init `#cloud-config` , ten skrypt musi być lokalnym miejscem, w którym będą uruchamiane polecenia AzureCLI w celu aprowizacji maszyny wirtualnej.  Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor simple_bash.sh`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że cały plik Cloud-init został poprawnie skopiowany, szczególnie w pierwszym wierszu.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Przed wdrożeniem tego obrazu należy utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm) i określ plik skryptu bash w `--custom-data simple_bash.sh` następujący sposób:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Sprawdź, czy skrypt bash został uruchomiony
SSH do publicznego adresu IP maszyny wirtualnej wyświetlanej w danych wyjściowych poprzedniego polecenia. Wprowadź własne **publicIpAddress** w następujący sposób:

```bash
ssh <publicIpAddress>
```

Przejdź do katalogu **/tmp** i sprawdź, czy plik myScript.txt istnieje i czy znajduje się w nim odpowiedni tekst.  Jeśli tak nie jest, możesz sprawdzić **/var/log/Cloud-init.log** , aby uzyskać więcej szczegółów.  Wyszukaj następujący wpis:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
