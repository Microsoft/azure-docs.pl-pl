---
title: Wyłączanie lub usuwanie agenta aprowizacji
description: Dowiedz się, jak wyłączyć lub usunąć agenta aprowizacji w maszynach wirtualnych i obrazach systemu Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 0fea82c376a178de0be8ede6c0393e1de21de614
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675808"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Wyłączanie i usuwanie agenta systemu Linux z maszyn wirtualnych i obrazów

Przed usunięciem agenta systemu Linux należy zrozumieć, jakie maszyny wirtualne nie będą mogły zostać wprowadzone po usunięciu agenta systemu Linux.

[Rozszerzenia](../extensions/overview.md) maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure, rozszerzenia są instalowane i zarządzane przez płaszczyznę kontroli systemu Azure. Jest to zadanie [agenta systemu Azure Linux](../extensions/agent-linux.md) do przetwarzania poleceń rozszerzenia platformy i zapewnienia poprawnego stanu rozszerzenia w ramach maszyny wirtualnej.

Platforma Azure obsługuje wiele rozszerzeń, które przedziały od konfiguracji maszyny wirtualnej, monitorowania, zabezpieczeń i narzędzi. Istnieje duże wybór rozszerzeń pierwszej i innej firmy, przykłady kluczowych scenariuszy, w których rozszerzenia są używane dla:
* Obsługa usług platformy Azure w ramach pierwszej firmy, takich jak Azure Backup, monitorowanie, szyfrowanie dysków, zabezpieczenia, replikacja lokacji i inne.
* Resetowanie protokołu SSH/hasła
* Konfiguracja maszyny wirtualnej — uruchamianie skryptów niestandardowych, instalowanie Chef, agentów Puppet itp..
* Produkty innych firm, takie jak produkty antywirusowe, narzędzia do luk w zabezpieczeniach maszyn wirtualnych, narzędzie do monitorowania maszyn wirtualnych i aplikacji.
* Rozszerzenia można powiązać z nowym wdrożeniem maszyny wirtualnej. Mogą na przykład być częścią większego wdrożenia, konfigurowania aplikacji na maszynach wirtualnych lub uruchamiania ich na wszystkich obsługiwanych systemach obsługujących rozszerzenia po wdrożeniu.

## <a name="disabling-extension-processing"></a>Wyłączanie przetwarzania rozszerzenia

Istnieje kilka sposobów, aby wyłączyć przetwarzanie rozszerzeń, w zależności od potrzeb, ale przed kontynuowaniem **należy** usunąć wszystkie rozszerzenia wdrożone na maszynie wirtualnej, na przykład korzystając z interfejsu wiersza polecenia platformy Azure, można [wyświetlać](/cli/azure/vm/extension#az-vm-extension-list) i [usuwać](/cli/azure/vm/extension#az-vm-extension-delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Jeśli nie wykonasz powyższych czynności, platforma podejmie próbę wysłania konfiguracji rozszerzenia i przekroczenia limitu czasu po 40min.

### <a name="disable-at-the-control-plane"></a>Wyłącz na płaszczyźnie kontroli
Jeśli nie masz pewności, czy w przyszłości będziesz potrzebować rozszerzeń, możesz pozostawić na maszynie wirtualnej agenta systemu Linux, a następnie wyłączyć możliwość przetwarzania rozszerzeń z poziomu platformy. Ta opcja jest dostępna w `Microsoft.Compute` wersji interfejsu API `2018-06-01` lub nowszej i nie ma zależności od zainstalowanej wersji agenta systemu Linux.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Można łatwo włączyć to przetwarzanie rozszerzenia z poziomu platformy przy użyciu powyższego polecenia, ale ustawić wartość "true".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Usuwanie agenta systemu Linux z uruchomionej maszyny wirtualnej

Upewnij się, że wszystkie istniejące rozszerzenia z maszyny wirtualnej zostały **usunięte** wcześniej, zgodnie z powyższym.

### <a name="step-1-remove-the-azure-linux-agent"></a>Krok 1. Usuwanie agenta systemu Linux platformy Azure

W przypadku usunięcia agenta systemu Linux, a nie skojarzonych artefaktów konfiguracji, można ponownie zainstalować program w późniejszym terminie. Uruchom jeden z następujących elementów jako główny, aby usunąć agenta platformy Azure dla systemu Linux:

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Dla SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Krok 2: (opcjonalnie) Usuń artefakty agenta platformy Azure dla systemu Linux
> [!IMPORTANT] 
>
> Można usunąć wszystkie skojarzone artefakty agenta systemu Linux, ale oznacza to, że nie będzie można go ponownie zainstalować w późniejszym czasie. Dlatego zdecydowanie zaleca się najpierw wyłączyć agenta systemu Linux, usuwając agenta systemu Linux przy użyciu powyższych opcji. 

Jeśli wiesz, że nie będziesz nigdy ponownie ponownie instalować agenta systemu Linux, możesz uruchomić następujące polecenie:

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Dla SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Przygotowywanie obrazu bez agenta systemu Linux
Jeśli masz obraz, który zawiera już funkcję Cloud-init, i chcesz usunąć agenta systemu Linux, ale nadal zainicjuj go przy użyciu funkcji Cloud-init, wykonaj kroki opisane w sekcji Krok 2 (i opcjonalnie krok 3) jako element główny, aby usunąć agenta Azure Linux, a następnie wykonaj następujące czynności w celu utworzenia obrazu niestandardowego.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Cofanie aprowizacji i tworzenie obrazu
Agent systemu Linux ma możliwość oczyszczenia niektórych istniejących metadanych obrazu, w tym kroku "waagent-unaprowizacji + User", jednak po jego usunięciu należy wykonać następujące działania, jak na przykład, i usunąć z niego inne poufne dane.

- Usuń wszystkie istniejące klucze hosta SSH

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Usuwanie konta administratora

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Usuń hasło główne

   ```bash
   passwd -d root
   ```

Po ukończeniu powyższych operacji można utworzyć niestandardowy obraz przy użyciu interfejsu wiersza polecenia platformy Azure.


**Tworzenie regularnego zarządzanego obrazu**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Tworzenie wersji obrazu w galerii obrazów udostępnionych**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Tworzenie maszyny wirtualnej na podstawie obrazu, który nie zawiera agenta systemu Linux
Podczas tworzenia maszyny wirtualnej z obrazu bez agenta systemu Linux należy upewnić się, że konfiguracja wdrożenia maszyny wirtualnej wskazuje, że rozszerzenia nie są obsługiwane na tej maszynie wirtualnej.

> [!NOTE] 
> 
> Jeśli nie wykonasz powyższych czynności, platforma podejmie próbę wysłania konfiguracji rozszerzenia i przekroczenia limitu czasu po 40min.

Aby wdrożyć maszynę wirtualną z wyłączonymi rozszerzeniami, można użyć interfejsu wiersza polecenia platformy Azure z opcją [--enable-Agent](/cli/azure/vm#az-vm-create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Alternatywnie możesz to zrobić za pomocą szablonów Azure Resource Manager (ARM), ustawiając `"provisionVMAgent": false,` .

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

Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi systemu Linux](provisioning.md).
