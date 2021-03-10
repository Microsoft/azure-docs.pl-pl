---
title: Omówienie agenta maszyny wirtualnej platformy Azure
description: Omówienie agenta maszyny wirtualnej platformy Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.openlocfilehash: 1b1766c0385303993af436911391a1c858bbff61
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547462"
---
# <a name="azure-virtual-machine-agent-overview"></a>Omówienie agenta maszyny wirtualnej platformy Azure
Agent maszyny wirtualnej Microsoft Azure (Agent VM) to bezpieczny, lekki proces zarządzający interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej Azure. Agent maszyny wirtualnej odgrywa podstawową rolę w procesie włączania i wykonywania rozszerzeń maszyny wirtualnej platformy Azure. Rozszerzenia maszyn wirtualnych umożliwiają konfigurację po wdrożeniu maszyny wirtualnej, taką jak instalowanie i Konfigurowanie oprogramowania. Rozszerzenia maszyn wirtualnych umożliwiają również włączenie funkcji odzyskiwania, takich jak resetowanie hasła administracyjnego maszyny wirtualnej. Bez agenta maszyny wirtualnej platformy Azure nie można uruchomić rozszerzeń maszyn wirtualnych.

Ten artykuł zawiera szczegółowe informacje dotyczące instalacji i wykrywania agenta maszyny wirtualnej platformy Azure.

## <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny wirtualnej

### <a name="azure-marketplace-image"></a>Obraz witryny Azure Marketplace

Agent maszyny wirtualnej platformy Azure jest instalowany domyślnie na dowolnej maszynie wirtualnej z systemem Windows wdrożonej z obrazu portalu Azure Marketplace. Po wdrożeniu obrazu witryny Azure Marketplace z poziomu portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu Azure Resource Manager jest również instalowany Agent maszyny wirtualnej platformy Azure.

Pakiet agenta gościa systemu Windows jest podzielony na dwie części:

- Agent aprowizacji (PA)
- Agent gościa systemu Windows (skrzydło)

Aby uruchomić maszynę wirtualną, należy zainstalować na maszynie wirtualnej PA, ale nie trzeba instalować tego skrzydła. Po wdrożeniu maszyny wirtualnej można wybrać opcję nie instaluj skrzydła. Poniższy przykład pokazuje, jak wybrać opcję *provisionVmAgent* z szablonem Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Jeśli nie masz zainstalowanych agentów, nie możesz używać niektórych usług platformy Azure, takich jak Azure Backup lub zabezpieczenia platformy Azure. Te usługi wymagają zainstalowania rozszerzenia. Jeśli maszyna wirtualna została wdrożona bez skrzydła, można zainstalować najnowszą wersję agenta później.

### <a name="manual-installation"></a>Instalacja ręczna
Agenta maszyny wirtualnej z systemem Windows można zainstalować ręcznie przy użyciu pakietu Instalatora Windows. Jeśli tworzysz niestandardowy obraz maszyny wirtualnej wdrożony na platformie Azure, może być konieczna instalacja ręczna. Aby ręcznie zainstalować agenta maszyny wirtualnej z systemem Windows, [Pobierz instalatora agenta maszyny wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789). Agent maszyny wirtualnej jest obsługiwany w systemie Windows Server 2008 (64 bitowym) i nowszych.

> [!NOTE]
> Ważne jest, aby zaktualizować opcję AllowExtensionOperations po ręcznej instalacji VMAgent na maszynie wirtualnej, która została wdrożona z obrazu bez ProvisionVMAgent Enable.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Wymagania wstępne

- Aby można było uruchomić agenta maszyny wirtualnej z systemem Windows, wymagany jest co najmniej system Windows Server 2008 z dodatkiem SP2 (64-bitowy) z .NET Framework 4,0. Zobacz [minimalną obsługę wersji dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Upewnij się, że maszyna wirtualna ma dostęp do adresu IP 168.63.129.16. Aby uzyskać więcej informacji, zobacz [co to jest adres IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Upewnij się, że usługa DHCP jest włączona wewnątrz maszyny wirtualnej gościa. Jest to wymagane do uzyskania adresu hosta lub sieci szkieletowej z usługi DHCP dla agenta maszyny wirtualnej IaaS i rozszerzeń do działania. Jeśli potrzebujesz statycznego prywatnego adresu IP, należy go skonfigurować za pomocą Azure Portal lub PowerShell i upewnić się, że opcja DHCP wewnątrz maszyny wirtualnej jest włączona. [Dowiedz się więcej](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o konfigurowaniu statycznego adresu IP za pomocą programu PowerShell.


## <a name="detect-the-vm-agent"></a>Wykrywanie agenta maszyny wirtualnej

### <a name="powershell"></a>PowerShell

Moduł Azure Resource Manager PowerShell może służyć do pobierania informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje dotyczące maszyny wirtualnej, na przykład stanu aprowizacji agenta maszyny wirtualnej platformy Azure, użyj polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Następujące wąskie przykładowe dane wyjściowe pokazują Właściwość *ProvisionVMAgent* zagnieżdżoną wewnątrz `OSProfile` . Ta właściwość może służyć do określenia, czy Agent maszyny wirtualnej został wdrożony na maszynie wirtualnej:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwrócenia zwięzłej listy nazw maszyn wirtualnych i stanu agenta maszyny wirtualnej:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Wykrywanie ręczne

Po zalogowaniu się do maszyny wirtualnej z systemem Windows Menedżer zadań może służyć do badania uruchomionych procesów. Aby sprawdzić agenta maszyny wirtualnej platformy Azure, Otwórz Menedżera zadań, kliknij kartę *szczegóły* , a następnie wyszukaj nazwę procesu **WindowsAzureGuestAgent.exe**. Obecność tego procesu wskazuje, że Agent maszyny wirtualnej jest zainstalowany.


## <a name="upgrade-the-vm-agent"></a>Uaktualnianie agenta maszyny wirtualnej
Agent maszyny wirtualnej platformy Azure dla systemu Windows jest automatycznie uaktualniany na obrazach wdrożonych z portalu Azure Marketplace. Gdy nowe maszyny wirtualne są wdrażane na platformie Azure, otrzymują najnowszego agenta maszyny wirtualnej na czas udostępniania maszyny wirtualnej. Jeśli Agent został zainstalowany ręcznie lub wdraża niestandardowe obrazy maszyn wirtualnych, należy ręcznie zaktualizować, aby uwzględnić nowego agenta maszyny wirtualnej podczas tworzenia obrazu.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Automatyczne zbieranie dzienników agenta gościa systemu Windows
Agent gościa systemu Windows zawiera funkcję automatycznego zbierania niektórych dzienników. Ta funkcja jest kontrolerem przez proces CollectGuestLogs.exe. Istnieje zarówno dla PaaS Cloud Services, jak i Virtual Machines IaaS, a jej celem jest szybkie & Automatyczne zbieranie niektórych dzienników diagnostycznych z maszyny wirtualnej, dzięki czemu mogą one być używane do analizy w trybie offline. Zbierane dzienniki to dzienniki zdarzeń, Dzienniki systemu operacyjnego, dzienniki platformy Azure i niektóre klucze rejestru. Tworzy plik ZIP, który jest przesyłany do hosta maszyny wirtualnej. Ten plik ZIP może następnie być oglądany przez zespoły inżynieryjne i specjalistów pomocy technicznej w celu zbadania problemów na żądanie klienta będącego właścicielem maszyny wirtualnej.

## <a name="guest-agent-and-osprofile-certificates"></a>Agent gościa i OSProfile certyfikaty
Agent maszyny wirtualnej platformy Azure jest odpowiedzialny za Instalowanie certyfikatów przywoływanych w ramach `OSProfile` maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. Jeśli te certyfikaty zostaną ręcznie usunięte z konsoli MMC certyfikatów Wewnątrz maszyny wirtualnej gościa, oczekuje się, że Agent gościa doda je ponownie.
Aby trwale usunąć certyfikat, należy usunąć go z usługi `OSProfile` , a następnie usunąć z poziomu systemu operacyjnego gościa.

W przypadku maszyny wirtualnej Użyj polecenie [Remove-AzVMSecret]() , aby usunąć certyfikaty z programu `OSProfile` .

Aby uzyskać więcej informacji na temat certyfikatów zestawu skalowania maszyn wirtualnych, zobacz [Virtual Machine Scale Sets-jak mogę usunąć przestarzałe certyfikaty?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [Omówienie rozszerzeń i funkcji maszyny wirtualnej platformy Azure](overview.md).