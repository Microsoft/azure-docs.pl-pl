---
title: Przygotowanie obrazu maszyny wirtualnej platformy Azure do użycia z usługą Cloud-init
description: Jak przygotować istniejący obraz maszyny wirtualnej platformy Azure do wdrożenia przy użyciu funkcji Cloud-init
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 6785bb1f5ebde4f746dc8aa1ea9f586f406f8431
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373678"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Przygotowywanie istniejącego obrazu maszyny wirtualnej z systemem Linux na potrzeby użycia z usługą Cloud-init
W tym artykule opisano sposób tworzenia istniejącej maszyny wirtualnej platformy Azure i przygotowania jej do ponownego wdrożenia i gotowości do korzystania z funkcji Cloud-init. Ten obraz może służyć do wdrażania nowej maszyny wirtualnej lub zestawów skalowania maszyn wirtualnych, z których można następnie dostosowywać program Cloud-init w czasie wdrażania.  Te skrypty usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa Cloud-init działa natywnie na platformie Azure i obsługiwanych dystrybucje z systemem Linux, zobacz [Omówienie usługi Cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Wymagania wstępne
W tym dokumencie przyjęto założenie, że masz już działającą maszynę wirtualną platformy Azure z obsługiwaną wersją systemu operacyjnego Linux. Komputer został już skonfigurowany zgodnie z potrzebami, zainstalowano wszystkie wymagane moduły, przetworzono wszystkie wymagane aktualizacje i przetestowano je w celu upewnienia się, że spełniają one wymagania. 

## <a name="preparing-rhel-76--centos-76"></a>Przygotowywanie RHEL 7,6/CentOS 7,6
Musisz być SSH do maszyny wirtualnej z systemem Linux i uruchamiać następujące polecenia w celu zainstalowania funkcji Cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Zaktualizuj `cloud_init_modules` sekcję w programie, `/etc/cloud/cloud.cfg` Aby uwzględnić następujące moduły:

```bash
- disk_setup
- mounts
```

Poniżej znajduje się przykładowa sekcja ogólnego przeznaczenia `cloud_init_modules` .

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

Kilka zadań związanych z obsługą administracyjną i obsługą dysków tymczasowych należy zaktualizować w programie `/etc/waagent.conf` . Uruchom następujące polecenia, aby zaktualizować odpowiednie ustawienia.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Zezwól na dostęp do platformy Azure jako źródła danych dla agenta systemu Azure Linux, tworząc nowy plik `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` za pomocą wybranego edytora z następującym wierszem:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Jeśli istniejący obraz platformy Azure ma skonfigurowany plik wymiany i chcesz zmienić konfigurację pliku wymiany dla nowych obrazów przy użyciu funkcji Cloud-init, musisz usunąć istniejący plik wymiany.

W przypadku obrazów opartych na Red Hat — postępuj zgodnie z instrukcjami podanymi w następującym dokumencie Red Hat, wyjaśniając, jak [usunąć plik wymiany](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

W przypadku obrazów CentOS z włączonym swapfile można uruchomić następujące polecenie, aby wyłączyć swapfile:

```bash
sudo swapoff /mnt/resource/swapfile
```

Upewnij się, że odwołanie swapfile zostało usunięte z `/etc/fstab` — powinien wyglądać podobnie do następującego:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Aby zaoszczędzić miejsce i usunąć plik wymiany, można uruchomić następujące polecenie:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Dodatkowy krok dla przygotowanego obrazu Cloud-init
> [!NOTE]
> Jeśli obraz był wcześniej gotowym i skonfigurowanym obrazem usługi **Cloud-init** , należy wykonać następujące czynności.

Poniższe trzy polecenia są używane tylko wtedy, gdy maszyna wirtualna, którą chcesz dostosować, to nowy, wyspecjalizowany obraz źródłowy został wcześniej zainicjowany przez funkcję Cloud-init.  NIE musisz wykonywać tych czynności, jeśli obraz został skonfigurowany przy użyciu agenta platformy Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizowanie ustawienia agenta systemu Linux 
Wszystkie obrazy platformy Azure mają zainstalowany agent systemu Azure Linux, niezależnie od tego, czy został on skonfigurowany przez funkcję Cloud-init, czy nie.  Uruchom następujące polecenie, aby zakończyć cofanie aprowizacji użytkownika z komputera z systemem Linux. 

```bash
sudo waagent -deprovision+user -force
```

Aby uzyskać więcej informacji na temat poleceń anulowania aprowizacji agenta platformy Azure w systemie Linux, zobacz [Agent systemu Linux Azure](../extensions/agent-linux.md) , aby uzyskać więcej informacji.

Zakończ sesję SSH, a następnie z poziomu powłoki bash Uruchom następujące polecenia AzureCLI, aby cofnąć alokację, uogólnić i utworzyć nowy obraz maszyny wirtualnej platformy Azure.  Zamień `myResourceGroup` i `sourceVmName` na odpowiednie informacje odzwierciedlające sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady dotyczące zmian konfiguracji w chmurze, zobacz następujące tematy:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
