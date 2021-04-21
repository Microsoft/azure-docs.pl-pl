---
title: Wyłączanie lub usuwanie agenta aprowizowania
description: Dowiedz się, jak wyłączyć lub usunąć agenta aprowizowania na komputerach wirtualnych i obrazach z systemem Linux.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c70b02bdc554c723f53ad5f8c0d36c5eca87811e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774372"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Wyłączanie lub usuwanie agenta systemu Linux z maszyn wirtualnych i obrazów

Przed usunięciem agenta systemu Linux musisz wiedzieć, co maszyna wirtualna nie będzie mogła zrobić po usunięciu agenta systemu Linux.

Rozszerzenia maszyn wirtualnych [](../extensions/overview.md) platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure, a rozszerzenia są instalowane i zarządzane przez płaszczyznę sterowania platformy Azure. Agent systemu Linux platformy [Azure](../extensions/agent-linux.md) musi przetwarzać polecenia rozszerzenia platformy i zapewniać prawidłowy stan rozszerzenia wewnątrz maszyny wirtualnej.

Platforma Azure hostuje wiele rozszerzeń, od konfiguracji maszyny wirtualnej, monitorowania, zabezpieczeń i aplikacji narzędziowych. Istnieje duży wybór rozszerzeń pierwszego i innych firm. Przykłady kluczowych scenariuszy, w których rozszerzenia są używane:
* Obsługa usług platformy Azure innych firm, takich jak Azure Backup, monitorowanie, szyfrowanie dysków, zabezpieczenia, replikacja lokacji i inne.
* Resetowanie hasła/SSH
* Konfiguracja maszyny wirtualnej — uruchamianie skryptów niestandardowych, instalowanie programu Chef, agentów Puppet itp.
* Produkty innych firm, takie jak produkty AV, narzędzia do luk w zabezpieczeniach maszyn wirtualnych, maszyny wirtualne i narzędzia do monitorowania aplikacji.
* Rozszerzenia można dołączać do nowego wdrożenia maszyny wirtualnej. Mogą one być na przykład częścią większego wdrożenia, konfigurowania aplikacji na maszynie wirtualnej lub uruchamiania w obsługiwanych systemach obsługiwanych przez rozszerzenia po wdrożeniu.

## <a name="disabling-extension-processing"></a>Wyłączanie przetwarzania rozszerzeń

Istnieje kilka sposobów wyłączania przetwarzania rozszerzeń, w zależności od  potrzeb, ale przed kontynuowaniem musisz usunąć wszystkie rozszerzenia wdrożone [](/cli/azure/vm/extension#az_vm_extension_list) na maszynie wirtualnej, na przykład przy użyciu interfejsu wiersza polecenia platformy Azure, możesz wyświetlić listę i [usunąć](/cli/azure/vm/extension#az_vm_extension_delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Jeśli nie zrobisz tego powyżej, platforma spróbuje wysłać konfigurację rozszerzenia i limit czasu po 40 minutach.

### <a name="disable-at-the-control-plane"></a>Wyłącz na płaszczyźnie sterowania
Jeśli nie masz pewności, czy w przyszłości będą potrzebne rozszerzenia, możesz pozostawić agenta systemu Linux zainstalowanego na maszynie wirtualnej, a następnie wyłączyć możliwość przetwarzania rozszerzeń z platformy. Ta opcja jest dostępna w wersji interfejsu API lub wyższej i nie ma zainstalowanej zależności od wersji `Microsoft.Compute` `2018-06-01` agenta systemu Linux.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
To przetwarzanie rozszerzenia można łatwo wywrzeć ponownie z platformy za pomocą powyższego polecenia, ale ustawić je na wartość "true".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Usuwanie agenta systemu Linux z uruchomionej maszyny wirtualnej

Upewnij się, **że wcześniej usunięto** wszystkie istniejące rozszerzenia z maszyny wirtualnej, zgodnie z powyższymi informacjami.

### <a name="step-1-remove-the-azure-linux-agent"></a>Krok 1. Usuwanie agenta platformy Azure dla systemu Linux

Jeśli po prostu usuniesz agenta systemu Linux, a nie skojarzone artefakty konfiguracji, możesz zainstalować ponownie później. Uruchom jedną z następujących czynności jako katalog główny, aby usunąć agenta platformy Azure dla systemu Linux:

#### <a name="for-ubuntu-1804"></a>Dla systemu Ubuntu >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>W przypadku >Redhat = 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>W przypadku SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Krok 2. (Opcjonalnie) Usuwanie artefaktów agenta platformy Azure dla systemu Linux
> [!IMPORTANT] 
>
> Możesz usunąć wszystkie skojarzone artefakty agenta systemu Linux, ale będzie to oznaczać, że nie będzie można zainstalować go ponownie w późniejszym terminie. W związku z tym zdecydowanie zaleca się wyłączenie agenta systemu Linux w pierwszej kolejności, usuwając agenta systemu Linux przy użyciu tylko powyższych. 

Jeśli wiesz, że kiedykolwiek ponownie nie zainstalujesz agenta systemu Linux, możesz uruchomić następujące czynności:

#### <a name="for-ubuntu-1804"></a>Dla systemu Ubuntu >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>W przypadku >Redhat = 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>W przypadku SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Przygotowywanie obrazu bez agenta systemu Linux
Jeśli masz obraz, który zawiera już plik cloud-init i chcesz usunąć agenta systemu Linux, ale nadal aprowizować go przy użyciu pliku cloud-init, uruchom kroki opisane w kroku 2 (i opcjonalnie Kroku 3) jako użytkownik główny, aby usunąć agenta systemu Linux na platformie Azure, a następnie następujące czynności spowodują usunięcie konfiguracji pliku cloud-init i buforowanych danych oraz przygotowanie maszyny wirtualnej do utworzenia obrazu niestandardowego.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Coprowizować i utworzyć obraz
Agent systemu Linux ma możliwość oczyszczenia niektórych istniejących metadanych obrazu za pomocą kroku "waagent -deprovision+user", jednak po jego usunięciu należy wykonać akcje, takie jak poniższe, i usunąć z niego wszystkie inne poufne dane.

- Usuń wszystkie istniejące klucze hosta SSH

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Usuwanie konta administratora

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Usuwanie hasła głównego

   ```bash
   passwd -d root
   ```

Po zakończeniu powyższych operacji możesz utworzyć obraz niestandardowy przy użyciu interfejsu wiersza polecenia platformy Azure.


**Tworzenie zwykłego obrazu zarządzanego**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Tworzenie wersji obrazu w Shared Image Gallery**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Tworzenie maszyny wirtualnej na pomocą obrazu, który nie zawiera agenta systemu Linux
Podczas tworzenia maszyny wirtualnej na podstawie obrazu bez agenta systemu Linux należy upewnić się, że konfiguracja wdrożenia maszyny wirtualnej wskazuje, że rozszerzenia nie są obsługiwane na tej maszynie wirtualnej.

> [!NOTE] 
> 
> Jeśli nie zrobisz tego powyżej, platforma spróbuje wysłać konfigurację rozszerzenia i limit czasu po 40 minutach.

Aby wdrożyć maszynę wirtualną z wyłączonymi rozszerzeniami, możesz użyć interfejsu wiersza polecenia platformy Azure [z ustawieniem --enable-agent.](/cli/azure/vm#az_vm_create)

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Alternatywnie można to zrobić przy użyciu szablonów Azure Resource Manager (ARM), `"provisionVMAgent": false,` ustawiając .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Provisioning Linux (Aprowizowanie systemu Linux).](provisioning.md)
