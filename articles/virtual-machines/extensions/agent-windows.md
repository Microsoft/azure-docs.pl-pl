---
title: Omówienie agenta maszyny wirtualnej platformy Azure
description: Omówienie agenta maszyny wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: a002479375d835f7fafe031517e5b2fe61b77b5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608693"
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
- Aby można było uruchomić agenta maszyny wirtualnej z systemem Windows, wymagany jest co najmniej system Windows Server 2008 (64-bitowy) z programem .NET Framework 4,0. Zobacz [minimalną obsługę wersji dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Upewnij się, że maszyna wirtualna ma dostęp do adresu IP 168.63.129.16. Aby uzyskać więcej informacji, zobacz [co to jest adres IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Wykrywanie agenta maszyny wirtualnej

### <a name="powershell"></a>PowerShell

Moduł Azure Resource Manager PowerShell może służyć do pobierania informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje dotyczące maszyny wirtualnej, na przykład stanu aprowizacji agenta maszyny wirtualnej platformy Azure, użyj polecenie [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Następujące wąskie przykładowe dane wyjściowe pokazują Właściwość *ProvisionVMAgent* zagnieżdżoną wewnątrz *OSProfile*. Ta właściwość może służyć do określenia, czy Agent maszyny wirtualnej został wdrożony na maszynie wirtualnej:

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
Agent maszyny wirtualnej platformy Azure dla systemu Windows jest automatycznie uaktualniany. Gdy nowe maszyny wirtualne są wdrażane na platformie Azure, otrzymują najnowszego agenta maszyny wirtualnej na czas udostępniania maszyny wirtualnej. Niestandardowe obrazy maszyn wirtualnych należy zaktualizować ręcznie, aby uwzględnić nowego agenta maszyny wirtualnej podczas tworzenia obrazu.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Automatyczne zbieranie dzienników agenta gościa systemu Windows
Agent gościa systemu Windows zawiera funkcję automatycznego zbierania niektórych dzienników. Ta funkcja jest kontrolerem przez proces CollectGuestLogs.exe. Istnieje zarówno dla PaaS Cloud Services, jak i Virtual Machines IaaS, a jej celem jest szybkie & Automatyczne zbieranie niektórych dzienników diagnostycznych z maszyny wirtualnej, dzięki czemu mogą one być używane do analizy w trybie offline. Zbierane dzienniki to dzienniki zdarzeń, Dzienniki systemu operacyjnego, dzienniki platformy Azure i niektóre klucze rejestru. Tworzy plik ZIP, który jest przesyłany do hosta maszyny wirtualnej. Ten plik ZIP może następnie być oglądany przez zespoły inżynieryjne i specjalistów pomocy technicznej w celu zbadania problemów na żądanie klienta będącego właścicielem maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [Omówienie rozszerzeń i funkcji maszyny wirtualnej platformy Azure](overview.md).
