---
title: Rozszerzenia i funkcje maszyny wirtualnej platformy Azure dla systemu Windows
description: Dowiedz się, jakie rozszerzenia są dostępne dla usługi Azure Virtual Machines, pogrupowane według ich udostępniania lub usprawnienia.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78ea26adb8299cc13d4677c66a0e06cba901d9dc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977378"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i witryny Azure Portal. Rozszerzenia można połączyć w pakiecie z nowym wdrożeniem nowej maszyny wirtualnej lub uruchamiać w dowolnym istniejącym systemie.

Ten artykuł zawiera Omówienie rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące korzystania z rozszerzeń maszyn wirtualnych platformy Azure oraz wskazówki dotyczące wykrywania i usuwania rozszerzeń maszyn wirtualnych oraz zarządzania nimi. Ten artykuł zawiera uogólnione informacje, ponieważ wiele rozszerzeń maszyn wirtualnych jest dostępnych, z których każdy może mieć unikatową konfigurację. Szczegóły dotyczące rozszerzenia można znaleźć w każdym dokumencie specyficznym dla danego rozszerzenia.

 

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępne są różne rozszerzenia maszyn wirtualnych platformy Azure z konkretnym przypadkiem użycia. Oto niektóre przykłady:

- Zastosuj konfiguracje żądanego stanu programu PowerShell do maszyny wirtualnej z rozszerzeniem DSC dla systemu Windows. Aby uzyskać więcej informacji, zobacz [rozszerzenie konfiguracji żądanego stanu platformy Azure](dsc-overview.md).
- Skonfiguruj monitorowanie maszyny wirtualnej przy użyciu rozszerzenia maszyny wirtualnej agenta Log Analytics. Aby uzyskać więcej informacji, zobacz [łączenie maszyn wirtualnych platformy Azure z dziennikami Azure monitor](../../azure-monitor/learn/quick-collect-azurevm.md).
- Skonfiguruj maszynę wirtualną platformy Azure przy użyciu Chef. Aby uzyskać więcej informacji, zobacz [Automatyzowanie wdrożenia maszyny wirtualnej platformy Azure za pomocą Chef](/azure/developer/chef/windows-vm-configure).
- Skonfiguruj monitorowanie infrastruktury platformy Azure przy użyciu rozszerzenia usługi Datadog. Aby uzyskać więcej informacji, zobacz [blog usługi Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Oprócz rozszerzeń specyficznych dla procesu, rozszerzenie niestandardowego skryptu jest dostępne zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. Rozszerzenie niestandardowego skryptu dla systemu Windows umożliwia uruchamianie dowolnego skryptu programu PowerShell na maszynie wirtualnej. Skrypty niestandardowe są przydatne do projektowania wdrożeń platformy Azure, które wymagają konfiguracji poza możliwością dostarczania natywnych narzędzi platformy Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie niestandardowego skryptu maszyny wirtualnej systemu Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsłużyć rozszerzenie na maszynie wirtualnej, należy zainstalować agenta systemu Windows Azure. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takie jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny wirtualnej platformy Azure zarządza interakcjami między maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej jest odpowiedzialny za wiele funkcjonalnych aspektów wdrażania maszyn wirtualnych platformy Azure i zarządzania nimi, w tym uruchamiania rozszerzeń maszyn wirtualnych. Agent maszyny wirtualnej platformy Azure jest wstępnie instalowany w obrazach portalu Azure Marketplace i można go zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny wirtualnej platformy Azure dla systemu Windows jest znany jako agent gościa systemu Windows.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcji instalacji, zobacz [Azure Virtual Machine Agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Obsługiwane wersje agentów

Aby zapewnić najlepsze możliwe środowisko, istnieją minimalne wersje agenta. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemów operacyjnych

Agent gościa systemu Windows działa na wielu systemów operacyjnych, jednak struktura rozszerzeń ma limit dla systemów operacyjnych. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane przez wszystkie systemów operacyjnych i mogą emitować *Kod błędu 51, "nieobsługiwany system operacyjny"*. Zapoznaj się z dokumentacją poszczególnych rozszerzeń, aby uzyskać pomoc techniczną.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzeń usługi Azure Storage, a operacje przekazywania stanu rozszerzenia są ogłaszane w usłudze Azure Storage. Jeśli używasz [obsługiwanej](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie musisz zezwalać na dostęp do usługi Azure Storage w regionie maszyny wirtualnej, ponieważ może on używać agenta do przekierowywania komunikacji do kontrolera sieci szkieletowej platformy Azure na potrzeby komunikacji agenta (HostGAPlugin funkcję za pośrednictwem kanału uprzywilejowanego w prywatnym IP [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)). Jeśli korzystasz z nieobsługiwanej wersji agenta, musisz zezwolić na dostęp wychodzący do usługi Azure Storage w tym regionie z maszyny wirtualnej.

> [!IMPORTANT]
> Jeśli zablokowano dostęp do usługi *168.63.129.16* za pomocą zapory gościa lub serwera proxy, rozszerzenia nie powiodą się od powyższych. Porty 80, 443 i 32526 są wymagane.

Agentów można używać tylko do pobierania pakietów rozszerzeń i stanu raportowania. Jeśli na przykład instalacja rozszerzenia wymaga pobrania skryptu z witryny GitHub (skrypt niestandardowy) lub wymaga dostępu do usługi Azure Storage (Azure Backup), wówczas konieczne będzie otwarcie dodatkowej zapory/portów sieciowych grup zabezpieczeń. Różne rozszerzenia mają różne wymagania, ponieważ są one aplikacjami w ich własnym zakresie. W przypadku rozszerzeń, które wymagają dostępu do usługi Azure Storage lub Azure Active Directory, można zezwolić na dostęp za pomocą [tagów usługi Azure sieciowej grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md#service-tags) do magazynu lub usługi azureactivedirectory.

Agent gościa systemu Windows nie ma obsługi serwera proxy, aby przekierowywać żądania ruchu agenta przez, co oznacza, że Agent gościa systemu Windows będzie zależeć od niestandardowego serwera proxy (jeśli istnieje), aby uzyskać dostęp do zasobów w Internecie lub na hoście za pośrednictwem protokołu IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Odkryj rozszerzenia maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, użyj polecenie [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). Poniższy przykład wyświetla listę wszystkich dostępnych rozszerzeń w lokalizacji *zachodniej* :

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzeń maszyn wirtualnych

Rozszerzenia maszyny wirtualnej platformy Azure są uruchamiane na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub odzyskać łączność na już wdrożonej maszynie wirtualnej. Rozszerzenia maszyn wirtualnych można również powiązać z wdrożeniami szablonów Azure Resource Manager. Korzystając z rozszerzeń z szablonami Menedżer zasobów, maszyny wirtualne platformy Azure można wdrażać i konfigurować bez interwencji po wdrożeniu.

Poniższe metody mogą służyć do uruchamiania rozszerzenia dla istniejącej maszyny wirtualnej.

### <a name="powershell"></a>Program PowerShell

Istnieje kilka poleceń programu PowerShell do uruchamiania indywidualnych rozszerzeń. Aby wyświetlić listę, użyj [poleceń Get-Command](/powershell/module/microsoft.powershell.core/get-command) i Filter on *Extension*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Zapewnia to dane wyjściowe podobne do następujących:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Poniższy przykład używa niestandardowego rozszerzenia skryptu w celu pobrania skryptu z repozytorium GitHub na docelową maszynę wirtualną, a następnie uruchomienia skryptu. Aby uzyskać więcej informacji na temat niestandardowego rozszerzenia skryptu, zobacz [Omówienie rozszerzenia niestandardowego skryptu](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

W poniższym przykładzie rozszerzenie dostępu do maszyny wirtualnej służy do resetowania hasła administracyjnego maszyny wirtualnej z systemem Windows do tymczasowego hasła. Aby uzyskać więcej informacji na temat rozszerzenia dostępu do maszyny wirtualnej, zobacz [resetowanie pulpit zdalny usługi na maszynie wirtualnej z systemem Windows](../troubleshooting/reset-rdp.md). Po uruchomieniu tego ustawienia należy zresetować hasło przy pierwszym logowaniu:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension`Polecenie może służyć do uruchamiania dowolnego rozszerzenia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat [Set-AzVMExtension Reference](/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyn wirtualnych można stosować do istniejącej maszyny wirtualnej za pomocą Azure Portal. Wybierz maszynę wirtualną w portalu, wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz odpowiednie rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze.

W poniższym przykładzie przedstawiono instalację rozszerzenia ochrony przed złośliwym oprogramowaniem firmy Microsoft z Azure Portal:

![Zainstaluj rozszerzenie chroniące przed złośliwym kodem](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny wirtualnej można dodać do szablonu Azure Resource Manager i wykonać przy użyciu wdrożenia szablonu. Podczas wdrażania rozszerzenia przy użyciu szablonu można utworzyć w pełni skonfigurowane wdrożenia platformy Azure. Na przykład poniższy kod JSON jest pobierany z szablonu Menedżer zasobów wdraża zestaw maszyn wirtualnych ze zrównoważonym obciążeniem i Azure SQL Database, a następnie instaluje aplikację .NET Core na każdej maszynie wirtualnej. Rozszerzenie maszyny wirtualnej zajmuje się instalacją oprogramowania.

Aby uzyskać więcej informacji, zobacz [pełny szablon Menedżer zasobów](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżer zasobów, zobacz [tworzenie Azure Resource Manager szablonów z rozszerzeniami maszyny wirtualnej systemu Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Bezpieczne dane rozszerzenia maszyny wirtualnej

Po uruchomieniu rozszerzenia maszyny wirtualnej może być konieczne uwzględnienie poufnych informacji, takich jak poświadczenia, nazwy kont magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych zawiera chronioną konfigurację, która szyfruje dane i odszyfrowuje ją tylko wewnątrz docelowej maszyny wirtualnej. Każde rozszerzenie ma określony chroniony Schemat konfiguracji, a każdy z nich jest szczegółowo opisany w dokumentacji dotyczącej rozszerzenia.

W poniższym przykładzie pokazano wystąpienie niestandardowego rozszerzenia skryptu dla systemu Windows. Polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie polecenie do wykonania nie jest szyfrowane:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Przeniesienie **polecenia do wykonania** właściwości do **chronionej** konfiguracji zabezpiecza ciąg wykonywania, jak pokazano w następującym przykładzie:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Na maszynie wirtualnej IaaS platformy Azure, która korzysta z rozszerzeń, w konsoli Certyfikaty mogą zostać wyświetlone certyfikaty, które mają temat **_Generator certyfikatów usługi Microsoft Azure CRP_**. Na klasycznej maszynie wirtualnej frontonu reddog te certyfikaty mają nazwę podmiotu **_usługi Microsoft Azure Service Management dla rozszerzeń_**.

Te certyfikaty zabezpieczają komunikację między maszyną wirtualną i jej hostem podczas transferu ustawień chronionych (hasło, inne poświadczenia) używanych przez rozszerzenia. Certyfikaty są tworzone przez kontroler sieci szkieletowej platformy Azure i przekazywane do agenta maszyny wirtualnej. Jeśli codziennie zatrzymujesz i uruchamiasz maszynę wirtualną, kontroler sieci szkieletowej może utworzyć nowy certyfikat. Certyfikat jest przechowywany w magazynie certyfikatów osobistych komputera. Te certyfikaty można usunąć. Agent maszyny wirtualnej w razie potrzeby tworzy ponowne utworzenie certyfikatów.

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak są aktualizowane agenci i rozszerzenia?

Agenci i rozszerzenia korzystają z tego samego mechanizmu aktualizacji. Niektóre aktualizacje nie wymagają dodatkowych reguł zapory.

Gdy aktualizacja jest dostępna, jest zainstalowana na maszynie wirtualnej tylko w przypadku zmiany rozszerzeń, a inne zmiany modelu maszyny wirtualnej, takie jak:

- Dyski danych
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Wpisy tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieci

Wydawcy udostępniają aktualizacje regionom w różnym czasie, więc możliwe jest posiadanie maszyn wirtualnych w różnych regionach w różnych wersjach.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Wyświetlanie listy rozszerzeń wdrożonych na maszynie wirtualnej

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizacje agenta

Agent gościa systemu Windows zawiera tylko *kod obsługi rozszerzenia*, *kod aprowizacji systemu Windows* jest oddzielony. Można odinstalować agenta gościa systemu Windows. Nie można wyłączyć automatycznej aktualizacji agenta gościa systemu Windows.

*Kod obsługi rozszerzenia* jest odpowiedzialny za komunikowanie się z siecią szkieletową Azure i obsługują operacje rozszerzeń maszyn wirtualnych, takie jak instalowanie, raportowanie stanu, aktualizowanie poszczególnych rozszerzeń i ich usuwanie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *kodu obsługi rozszerzenia*.

Aby sprawdzić, która wersja jest uruchomiona, zobacz [wykrywanie zainstalowanego agenta gościa systemu Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Po udostępnieniu aktualizacji rozszerzenia Agent gościa systemu Windows pobiera i uaktualnia rozszerzenie. Automatyczna aktualizacja rozszerzeń jest *niewielka* lub *poprawka*. Można zrezygnować z rozszerzeń lub zrezygnować z nich podczas inicjowania obsługi *administracyjnej rozszerzenia.* Poniższy przykład pokazuje, jak automatycznie uaktualniać wersje pomocnicze w szablonie Menedżer zasobów z *włączoną flagą autoupgrademinorversion ": true*":

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Aby uzyskać najnowsze poprawki błędów wersji, zdecydowanie zaleca się, aby zawsze wybierać aktualizacje AutoUpdate we wdrożeniach rozszerzenia. Nie można zrezygnować z aktualizacji poprawek, które mają poprawki zabezpieczeń lub klucza.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować aktualizacje rozszerzeń

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Określanie, czy rozszerzenie jest ustawione na włączoną flagą autoupgrademinorversion na maszynie wirtualnej

Jeśli rozszerzenie zostało udostępnione za pomocą elementu "włączoną flagą autoupgrademinorversion", można je zobaczyć z modelu maszyny wirtualnej. Aby sprawdzić, użyj polecenie [Get-AzVm](/powershell/module/az.compute/get-azvm) i podaj nazwę grupy zasobów i maszyny wirtualnej w następujący sposób:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Następujące przykładowe dane wyjściowe pokazują, że *włączoną flagą autoupgrademinorversion* jest ustawiona na *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie, kiedy wystąpił włączoną flagą autoupgrademinorversion

Aby sprawdzić, kiedy wystąpiła aktualizacja rozszerzenia, Przejrzyj dzienniki agenta na maszynie wirtualnej w lokalizacji *C:\WindowsAzure\Logs\WaAppAgent.log*

W poniższym przykładzie maszyna wirtualna ma zainstalowaną *firmę Microsoft. COMPUTE. CustomScriptExtension 1,8* . Poprawka była dostępna w wersji *1,9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Aby wykonać swoje zadania, Agent musi działać jako *System lokalny*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyny wirtualnej

Każde rozszerzenie maszyny wirtualnej może zawierać kroki rozwiązywania problemów specyficzne dla danego rozszerzenia. Na przykład jeśli używasz niestandardowego rozszerzenia skryptu, szczegóły wykonania skryptu można znaleźć lokalnie na maszynie wirtualnej, w której uruchomiono rozszerzenie. Wszelkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji dotyczącej rozszerzenia.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta gościa systemu Windows, zapoznaj się z działaniem w przypadku aprowizacji rozszerzenia w *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Zapoznaj się z rzeczywistymi dziennikami rozszerzeń, aby uzyskać więcej szczegółów w *C:\WindowsAzure\Logs\Plugins \<extensionName> *

3. Sprawdź sekcje dotyczące rozwiązywania problemów z dokumentacją dotyczącą kodów błędów, znanych problemów itp.

4. Sprawdź dzienniki systemu. Sprawdź inne operacje, które mogły mieć wpływ na rozszerzenie, takie jak długotrwała instalacja innej aplikacji wymagającej wyłącznego dostępu do Menedżera pakietów.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny błędów rozszerzeń

1. Rozszerzenia mają 20 minut na uruchomienie (wyjątki to rozszerzenia CustomScript, Chef i DSC o 90 minutach). Jeśli wdrożenie przekroczy ten czas, zostanie ono oznaczone jako przekroczenie limitu czasu. Przyczyną tego problemu może być niewielka liczba maszyn wirtualnych zasobów, inne konfiguracje maszyn wirtualnych/zadania uruchamiania zużywające duże ilości zasobów, gdy rozszerzenie próbuje zainicjować obsługę administracyjną.

2. Nie spełniono minimalnych wymagań wstępnych. Niektóre rozszerzenia mają zależności od jednostek SKU maszyn wirtualnych, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych wymagań dostępu do sieci, na przykład komunikacji z usługą Azure Storage lub usługami publicznymi. Innym przykładem może być dostęp do repozytoriów pakietów, brak miejsca na dysku lub ograniczenia zabezpieczeń.

3. Wyłączny dostęp do Menedżera pakietów. W niektórych przypadkach może wystąpić konfiguracja długotrwałej maszyny wirtualnej i rozwiązywanie konfliktów instalacji rozszerzeń, gdzie oba potrzebują wyłącznego dostępu do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny wirtualnej na maszynie wirtualnej Użyj polecenie [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby przywrócić stan rozszerzenia. *Stany podstanów [0]* pokazują, że inicjowanie obsługi rozszerzenia zakończyło się pomyślnie, co oznacza, że zostało pomyślnie wdrożone na maszynie wirtualnej, ale wykonanie rozszerzenia wewnątrz maszyny wirtualnej nie powiodło się, *podstanów [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Dane wyjściowe są podobne do następujących przykładowych danych wyjściowych:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Stan wykonania rozszerzenia można również znaleźć w Azure Portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz pozycję **rozszerzenia**, a następnie wybierz odpowiednie rozszerzenie.

### <a name="rerun-vm-extensions"></a>Uruchom ponownie rozszerzenia maszyn wirtualnych

Mogą wystąpić sytuacje, w których rozszerzenie maszyny wirtualnej musi zostać ponownie uruchomione. Możesz ponownie uruchomić rozszerzenie, usuwając je, a następnie uruchamiając ponownie rozszerzenie z wybraną metodą wykonywania. Aby usunąć rozszerzenie, użyj polecenie [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) w następujący sposób:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Możesz również usunąć rozszerzenie w Azure Portal w następujący sposób:

1. Wybierz maszynę wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **Odinstaluj**.

## <a name="common-vm-extensions-reference"></a>Informacje o typowych rozszerzeniach maszyn wirtualnych
| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie niestandardowego skryptu dla systemu Windows |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Rozszerzenie niestandardowego skryptu dla systemu Windows](custom-script-windows.md) |
| Rozszerzenie DSC dla systemu Windows |Rozszerzenie DSC programu PowerShell (Konfiguracja żądanego stanu) |[Rozszerzenie DSC dla systemu Windows](dsc-overview.md) |
| Rozszerzenie Diagnostyki Azure |Zarządzaj Diagnostyka Azure |[Diagnostyka Azure rozszerzenie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie dostępu do maszyny wirtualnej platformy Azure |Zarządzanie użytkownikami i poświadczeniami |[Rozszerzenie dostępu do maszyny wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [Omówienie rozszerzeń i funkcji maszyny wirtualnej platformy Azure](overview.md).