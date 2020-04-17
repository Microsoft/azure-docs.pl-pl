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
ms.openlocfilehash: f29a20ddeb93ec3d4aa98bbcb36f50456b543667
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452574"
---
# <a name="azure-virtual-machine-agent-overview"></a>Usługa Azure Virtual Machine Agent — omówienie
Agent maszyny wirtualnej platformy Microsoft Azure (Agent maszyn wirtualnych) to bezpieczny, lekki proces, który zarządza interakcją maszyny wirtualnej z kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej odgrywa podstawową rolę w procesie włączania i wykonywania rozszerzeń maszyny wirtualnej platformy Azure. Rozszerzenia maszyn wirtualnych umożliwiają konfigurację maszyny Wirtualnej po wdrożeniu, taką jak instalowanie i konfigurowanie oprogramowania. Rozszerzenia maszyn wirtualnych umożliwiają również funkcje odzyskiwania, takie jak resetowanie hasła administracyjnego maszyny Wirtualnej. Bez agenta maszyny wirtualnej platformy Azure nie można uruchomić rozszerzeń maszyn wirtualnych.

W tym artykule opisano instalację i wykrywanie agenta maszyny wirtualnej platformy Azure.

## <a name="install-the-vm-agent"></a>Instalowanie agenta maszyny Wirtualnej

### <a name="azure-marketplace-image"></a>Obraz portalu Azure Marketplace

Agent maszyny wirtualnej platformy Azure jest instalowany domyślnie na dowolnej maszynie wirtualnej systemu Windows wdrożonej na podstawie obrazu portalu Azure Marketplace. Po wdrożeniu obrazu portalu portalu, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager zostanie również zainstalowany agent maszyny Wirtualnej platformy Azure.

Pakiet agenta gościa systemu Windows jest podzielony na dwie części:

- Agent inicjowania obsługi administracyjnej (PA)
- Agent gościa systemu Windows (WinGA)

Aby uruchomić maszynę wirtualną, na maszynie wirtualnej musi być zainstalowana maszyna wirtualna, jednak nie trzeba jej instalować. W czasie wdrażania maszyny Wirtualnej można wybrać, aby nie instalować winga. W poniższym przykładzie pokazano, jak wybrać opcję *aprowidrowośćVmAgent* za pomocą szablonu usługi Azure Resource Manager:

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

Jeśli nie masz zainstalowanych agentów, nie można korzystać z niektórych usług platformy Azure, takich jak Usługa Azure Backup lub Azure Security. Te usługi wymagają rozszerzenia do zainstalowania. Jeśli wdrożono maszynę wirtualną bez winga, można zainstalować najnowszą wersję agenta później.

### <a name="manual-installation"></a>Instalacja ręczna
Agent maszyny Wirtualnej systemu Windows można zainstalować ręcznie z pakietem instalatora systemu Windows. Ręczna instalacja może być konieczna podczas tworzenia niestandardowego obrazu maszyny Wirtualnej, który jest wdrażany na platformie Azure. Aby ręcznie zainstalować agenta maszyny Wirtualnej systemu Windows, [pobierz instalator agenta maszyny Wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789). Agent maszyny Wirtualnej jest obsługiwany w systemie Windows Server 2008 R2 i nowszych.

> [!NOTE]
> Ważne jest, aby zaktualizować AllowExtensionOperations opcja po ręcznym zainstalowaniu vmAgent na maszynie wirtualnej, który został wdrożony z obrazu bez provisionVMAgent włączyć.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Wymagania wstępne
- Agent maszyn wirtualnych systemu Windows potrzebuje co najmniej systemu Windows Server 2008 R2 (64 bity) do uruchomienia z programem .Net Framework 4.0. Zobacz [Minimalna obsługa wersji dla agentów maszyn wirtualnych na platformie Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Upewnij się, że maszyna wirtualna ma dostęp do adresu IP 168.63.129.16. Aby uzyskać więcej informacji, zobacz [Jaki jest adres IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Wykrywanie agenta maszyny Wirtualnej

### <a name="powershell"></a>PowerShell

Moduł programu Azure Resource Manager PowerShell może służyć do pobierania informacji o maszynach wirtualnych platformy Azure. Aby wyświetlić informacje o maszynie Wirtualnej, takie jak stan inicjowania obsługi administracyjnej agenta maszyny Wirtualnej platformy Azure, należy użyć [funkcji Get-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)

```powershell
Get-AzVM
```

Następujące skondensowane przykładowe dane wyjściowe pokazuje *ProvisionVMAgent* właściwość zagnieżdżona wewnątrz *OSProfile*. Ta właściwość może służyć do określenia, czy agent maszyny Wirtualnej został wdrożony na maszynie Wirtualnej:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Poniższy skrypt może służyć do zwracania zwięzłej listy nazw maszyn wirtualnych i stanu agenta maszyny Wirtualnej:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ręczne wykrywanie

Po zalogowaniu się do maszyny Wirtualnej systemu Windows Menedżer zadań może służyć do sprawdzania uruchomionych procesów. Aby sprawdzić, czy agent maszyny Wirtualnej platformy Azure, otwórz Menedżera zadań, kliknij kartę *Szczegóły* i poszukaj nazwy procesu **WindowsAzureGuestAgent.exe**. Obecność tego procesu wskazuje, że agent maszyny Wirtualnej jest zainstalowany.


## <a name="upgrade-the-vm-agent"></a>Uaktualnienie agenta maszyny Wirtualnej
Agent maszyny Wirtualnej platformy Azure dla systemu Windows jest automatycznie uaktualniany. Gdy nowe maszyny wirtualne są wdrażane na platformie Azure, otrzymują najnowszy agent maszyny Wirtualnej w czasie inicjowania obsługi administracyjnej maszyny Wirtualnej. Niestandardowe obrazy maszyn wirtualnych powinny być ręcznie aktualizowane w celu uwzględnienia nowego agenta maszyny Wirtualnej w czasie tworzenia obrazu.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Zbieranie automatycznych dzienników agenta gościa systemu Windows
Agent gościa systemu Windows ma funkcję automatycznego zbierania niektórych dzienników. Ta funkcja jest kontrolerem przez proces CollectGuestLogs.exe. Istnieje zarówno dla usług w chmurze PaaS i IaaS maszyn wirtualnych i jego celem jest szybko & automatycznie zbierać dzienniki diagnostyki z maszyny wirtualnej — dzięki czemu mogą być używane do analizy w trybie offline. Zebrane dzienniki to dzienniki zdarzeń, dzienniki systemu operacyjnego, dzienniki platformy Azure i niektóre klucze rejestru. Tworzy plik ZIP, który jest przesyłany do hosta maszyny Wirtualnej. Ten plik ZIP można następnie przeglądać przez zespoły inżynierów i specjalistów pomocy technicznej w celu zbadania problemów na żądanie klienta posiadającego maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [omówienie rozszerzeń i funkcji maszyn wirtualnych platformy Azure.](overview.md)
