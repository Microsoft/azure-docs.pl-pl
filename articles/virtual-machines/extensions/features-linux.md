---
title: Rozszerzenia i funkcje maszyny wirtualnej platformy Azure dla systemu Linux
description: Dowiedz się, jakie rozszerzenia są dostępne dla usługi Azure Virtual Machines w systemie Linux, pogrupowane według ich udostępniania lub usprawnienia.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: b61bbacf889df23455266fb81124e14ef44388d2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336126"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i witryny Azure Portal. Rozszerzenia można połączyć w pakiecie z nowym wdrożeniem nowej maszyny wirtualnej lub uruchamiać w dowolnym istniejącym systemie.

Ten artykuł zawiera Omówienie rozszerzeń maszyn wirtualnych, wymagania wstępne dotyczące korzystania z rozszerzeń maszyn wirtualnych platformy Azure oraz wskazówki dotyczące wykrywania i usuwania rozszerzeń maszyn wirtualnych oraz zarządzania nimi. Ten artykuł zawiera uogólnione informacje, ponieważ wiele rozszerzeń maszyn wirtualnych jest dostępnych, z których każdy może mieć unikatową konfigurację. Szczegóły dotyczące rozszerzenia można znaleźć w każdym dokumencie specyficznym dla danego rozszerzenia.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępne są różne rozszerzenia maszyn wirtualnych platformy Azure z konkretnym przypadkiem użycia. Oto niektóre przykłady:

- Zastosuj konfiguracje żądanego stanu programu PowerShell do maszyny wirtualnej z rozszerzeniem DSC dla systemu Linux. Aby uzyskać więcej informacji, zobacz [rozszerzenie konfiguracji żądanego stanu platformy Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Skonfiguruj monitorowanie maszyny wirtualnej przy użyciu rozszerzenia maszyny wirtualnej Microsoft Monitoring Agent. Aby uzyskać więcej informacji, zobacz [Jak monitorować maszynę wirtualną z systemem Linux](../linux/tutorial-monitor.md).
- Skonfiguruj monitorowanie infrastruktury platformy Azure przy użyciu rozszerzenia Chef lub usługi Datadog. Aby uzyskać więcej informacji, zobacz blog [Chef](https://docs.chef.io/azure_portal.html) lub [usługi Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oprócz rozszerzeń specyficznych dla procesu, rozszerzenie niestandardowego skryptu jest dostępne zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. Rozszerzenie niestandardowego skryptu dla systemu Linux umożliwia uruchamianie dowolnego skryptu bash na maszynie wirtualnej. Skrypty niestandardowe są przydatne do projektowania wdrożeń platformy Azure, które wymagają konfiguracji poza możliwością dostarczania natywnych narzędzi platformy Azure. Aby uzyskać więcej informacji, zobacz [rozszerzenie niestandardowego skryptu maszyny wirtualnej systemu Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsłużyć rozszerzenie na maszynie wirtualnej, należy zainstalować agenta systemu Azure Linux. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takie jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny wirtualnej platformy Azure zarządza interakcjami między maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej jest odpowiedzialny za wiele funkcjonalnych aspektów wdrażania maszyn wirtualnych platformy Azure i zarządzania nimi, w tym uruchamiania rozszerzeń maszyn wirtualnych. Agent maszyny wirtualnej platformy Azure jest wstępnie instalowany w obrazach portalu Azure Marketplace i można go zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny wirtualnej platformy Azure dla systemu Linux jest znany jako agent Linux.

Aby uzyskać informacje dotyczące obsługiwanych systemów operacyjnych i instrukcji instalacji, zobacz [Azure Virtual Machine Agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Obsługiwane wersje agentów

Aby zapewnić najlepsze możliwe środowisko, istnieją minimalne wersje agenta. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane systemów operacyjnych

Agent systemu Linux działa na wielu systemów operacyjnych, jednak struktura rozszerzeń ma limit dla systemów operacyjnych. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane przez wszystkie systemów operacyjnych i mogą emitować *Kod błędu 51, "nieobsługiwany system operacyjny"*. Zapoznaj się z dokumentacją poszczególnych rozszerzeń, aby uzyskać pomoc techniczną.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzeń usługi Azure Storage, a operacje przekazywania stanu rozszerzenia są ogłaszane w usłudze Azure Storage. Jeśli używasz [obsługiwanej](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie musisz zezwalać na dostęp do usługi Azure Storage w regionie maszyny wirtualnej, ponieważ może on użyć agenta w celu przekierowania komunikacji do kontrolera sieci szkieletowej platformy Azure w celu komunikacji z agentem. Jeśli korzystasz z nieobsługiwanej wersji agenta, musisz zezwolić na dostęp wychodzący do usługi Azure Storage w tym regionie z maszyny wirtualnej.

> [!IMPORTANT]
> Jeśli zablokowano dostęp do usługi *168.63.129.16* za pomocą zapory gościa, rozszerzenia nie powiodą się, niezależnie od powyższych.

Agentów można używać tylko do pobierania pakietów rozszerzeń i stanu raportowania. Jeśli na przykład instalacja rozszerzenia wymaga pobrania skryptu z witryny GitHub (skrypt niestandardowy) lub wymaga dostępu do usługi Azure Storage (Azure Backup), wówczas konieczne będzie otwarcie dodatkowej zapory/portów sieciowych grup zabezpieczeń. Różne rozszerzenia mają różne wymagania, ponieważ są one aplikacjami w ich własnym zakresie. W przypadku rozszerzeń, które wymagają dostępu do usługi Azure Storage, można zezwolić na dostęp za pomocą tagów usługi Azure sieciowej grupy zabezpieczeń dla [magazynu](../../virtual-network/security-overview.md#service-tags).

Aby przekierować żądania ruchu agentów, Agent systemu Linux ma obsługę serwera proxy. Jednak ten serwer proxy nie stosuje rozszerzeń. Należy skonfigurować każde pojedyncze rozszerzenie, aby działało z serwerem proxy.

## <a name="discover-vm-extensions"></a>Odkryj rozszerzenia maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, użyj polecenie [AZ VM Extension Image list](/cli/azure/vm/extension/image#az-vm-extension-image-list). Poniższy przykład wyświetla listę wszystkich dostępnych rozszerzeń w lokalizacji *zachodniej* :

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzeń maszyn wirtualnych

Rozszerzenia maszyny wirtualnej platformy Azure są uruchamiane na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub odzyskać łączność na już wdrożonej maszynie wirtualnej. Rozszerzenia maszyn wirtualnych można również powiązać z wdrożeniami szablonów Azure Resource Manager. Korzystając z rozszerzeń z szablonami Menedżer zasobów, maszyny wirtualne platformy Azure można wdrażać i konfigurować bez interwencji po wdrożeniu.

Poniższe metody mogą służyć do uruchamiania rozszerzenia dla istniejącej maszyny wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Rozszerzenia maszyny wirtualnej platformy Azure można uruchamiać dla istniejącej maszyny wirtualnej za pomocą polecenia [AZ VM Extension Set](/cli/azure/vm/extension#az-vm-extension-set) . W poniższym przykładzie zostanie uruchomione niestandardowe rozszerzenie skryptu dla maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *resourceName*. Zastąp przykładową nazwę grupy zasobów, nazwę maszyny wirtualnej i skrypt do uruchomienia (https: \/ /RAW.githubusercontent.com/Me/Project/Hello.sh) przy użyciu własnych informacji. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Po poprawnym uruchomieniu rozszerzenia dane wyjściowe są podobne do następującego przykładu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyn wirtualnych można stosować do istniejącej maszyny wirtualnej za pomocą Azure Portal. Wybierz maszynę wirtualną w portalu, wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz odpowiednie rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze.

Na poniższej ilustracji przedstawiono instalację rozszerzenia niestandardowego skryptu systemu Linux z Azure Portal:

![Zainstaluj rozszerzenie niestandardowego skryptu](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyny wirtualnej można dodać do szablonu Azure Resource Manager i wykonać przy użyciu wdrożenia szablonu. Podczas wdrażania rozszerzenia przy użyciu szablonu można utworzyć w pełni skonfigurowane wdrożenia platformy Azure. Na przykład poniższy kod JSON jest pobierany z szablonu Menedżer zasobów, który wdraża zestaw maszyn wirtualnych o zrównoważonym obciążeniu i Azure SQL Database, a następnie instaluje aplikację .NET Core na każdej maszynie wirtualnej. Rozszerzenie maszyny wirtualnej zajmuje się instalacją oprogramowania.

Aby uzyskać więcej informacji, zobacz pełny [szablon Menedżer zasobów](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżer zasobów, zobacz [tworzenie Azure Resource Manager szablonów](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Bezpieczne dane rozszerzenia maszyny wirtualnej

Po uruchomieniu rozszerzenia maszyny wirtualnej może być konieczne uwzględnienie poufnych informacji, takich jak poświadczenia, nazwy kont magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych zawiera chronioną konfigurację, która szyfruje dane i odszyfrowuje ją tylko wewnątrz docelowej maszyny wirtualnej. Każde rozszerzenie ma określony chroniony Schemat konfiguracji, a każdy z nich jest szczegółowo opisany w dokumentacji dotyczącej rozszerzenia.

W poniższym przykładzie pokazano wystąpienie niestandardowego rozszerzenia skryptu dla systemu Linux. Polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie polecenie do wykonania nie jest szyfrowane:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
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
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

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

#### <a name="agent-updates"></a>Aktualizacje agenta

Agent maszyny wirtualnej systemu Linux zawiera kod *agenta aprowizacji* i *kod obsługi rozszerzenia*  w jednym pakiecie, którego nie można oddzielić. *Agenta aprowizacji* można wyłączyć, jeśli chcesz zainicjować obsługę administracyjną na platformie Azure przy użyciu funkcji Cloud-init. Aby to zrobić, zobacz [using Cloud-init](../linux/using-cloud-init.md).

Obsługiwane wersje agentów mogą korzystać z funkcji aktualizacji automatycznych. Jedyny kod, który można zaktualizować, to *kod obsługi rozszerzenia*, a nie kod aprowizacji. *Kod agenta aprowizacji* jest uruchamiany jednokrotnie.

*Kod obsługi rozszerzenia* jest odpowiedzialny za komunikowanie się z siecią szkieletową Azure i obsługują operacje rozszerzeń maszyn wirtualnych, takie jak instalowanie, raportowanie stanu, aktualizowanie poszczególnych rozszerzeń i ich usuwanie. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia *kodu obsługi rozszerzenia*.

Po zainstalowaniu agenta zostanie utworzony demon nadrzędny. Ten element nadrzędny następnie duplikuje proces podrzędny, który jest używany do obsługi rozszerzeń. Jeśli dla agenta jest dostępna aktualizacja, zostanie ona pobrana, a element nadrzędny zatrzyma proces podrzędny, uaktualni go, a następnie ponownie go uruchamia. W przypadku wystąpienia problemu z aktualizacją proces nadrzędny zostanie wycofany do poprzedniej wersji podrzędnej.

Nie można zaktualizować procesu nadrzędnego. Element nadrzędny może zostać zaktualizowany tylko przez aktualizację pakietu dystrybucji.

Aby sprawdzić, która wersja jest uruchomiona, sprawdź `waagent` następujące informacje:

```bash
waagent --version
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

W poprzednim przykładzie danych wyjściowych element nadrzędny lub "wdrożony pakiet" ma wartość *WALinuxAgent-2.2.17*

"Agent stanu celu" jest wersją aktualizacji AutoUpdate.

Zdecydowanie zaleca się, aby zawsze mieć automatyczne aktualizacje dla agenta, [Funkcja Aktualizacje automatyczne. włączone = y](./update-linux-agent.md). Jeśli to ustawienie nie jest włączone, należy ręcznie zaktualizować agenta i nie pobrać poprawek błędów i zabezpieczeń.

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Po udostępnieniu aktualizacji rozszerzenia Agent systemu Linux pobiera i uaktualnia rozszerzenie. Automatyczna aktualizacja rozszerzeń jest *niewielka* lub *poprawka*. Można zrezygnować z rozszerzeń lub zrezygnować z nich podczas inicjowania obsługi *administracyjnej rozszerzenia.* Poniższy przykład pokazuje, jak automatycznie uaktualniać wersje pomocnicze w szablonie Menedżer zasobów z *włączoną flagą autoupgrademinorversion ": true*":

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Aby uzyskać najnowsze poprawki błędów wersji, zdecydowanie zaleca się, aby zawsze wybierać aktualizacje AutoUpdate we wdrożeniach rozszerzenia. Nie można zrezygnować z aktualizacji poprawek, które mają poprawki zabezpieczeń lub klucza.

### <a name="how-to-identify-extension-updates"></a>Jak zidentyfikować aktualizacje rozszerzeń

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Określanie, czy rozszerzenie jest ustawione na włączoną flagą autoupgrademinorversion na maszynie wirtualnej

Jeśli rozszerzenie zostało udostępnione za pomocą elementu "włączoną flagą autoupgrademinorversion", można je zobaczyć z modelu maszyny wirtualnej. Aby sprawdzić, użyj [AZ VM show](/cli/azure/vm#az-vm-show) i podaj nazwę grupy zasobów i maszyny wirtualnej w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Następujące przykładowe dane wyjściowe pokazują, że *włączoną flagą autoupgrademinorversion* jest ustawiona na *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie, kiedy wystąpił włączoną flagą autoupgrademinorversion

Aby sprawdzić, kiedy wystąpiła aktualizacja rozszerzenia, Przejrzyj dzienniki agenta na maszynie wirtualnej pod adresem */var/log/waagent.log*.

W poniższym przykładzie maszyna wirtualna miała zainstalowany *pakiet Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* . Poprawka była dostępna dla *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Uprawnienia agenta

Aby wykonać swoje zadania, Agent musi być uruchomiony jako *główny*.

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyny wirtualnej

Każde rozszerzenie maszyny wirtualnej może zawierać kroki rozwiązywania problemów specyficzne dla danego rozszerzenia. Na przykład jeśli używasz niestandardowego rozszerzenia skryptu, szczegóły wykonania skryptu można znaleźć lokalnie na maszynie wirtualnej, w której uruchomiono rozszerzenie. Wszelkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji dotyczącej rozszerzenia.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta systemu Linux, zapoznaj się z działaniem w przypadku aprowizacji rozszerzenia w */var/log/waagent.log*

2. Zapoznaj się z rzeczywistymi dziennikami rozszerzeń, aby uzyskać więcej szczegółów w */var/log/Azure/ \<extensionName> *

3. Sprawdź sekcje dotyczące rozwiązywania problemów z dokumentacją, aby zapoznać się z kodami błędów, znanymi problemami itp.

3. Sprawdź dzienniki systemu. Sprawdź inne operacje, które mogły mieć wpływ na rozszerzenie, takie jak długotrwała instalacja innej aplikacji wymagającej wyłącznego dostępu do Menedżera pakietów.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny błędów rozszerzeń

1. Rozszerzenia mają 20 minut na uruchomienie (wyjątki to rozszerzenia CustomScript, Chef i DSC o 90 minutach). Jeśli wdrożenie przekroczy ten czas, zostanie ono oznaczone jako przekroczenie limitu czasu. Przyczyną tego problemu może być niewielka liczba maszyn wirtualnych zasobów, inne konfiguracje maszyn wirtualnych/zadania uruchamiania zużywające duże ilości zasobów, gdy rozszerzenie próbuje zainicjować obsługę administracyjną.

2. Nie spełniono minimalnych wymagań wstępnych. Niektóre rozszerzenia mają zależności od jednostek SKU maszyn wirtualnych, takich jak obrazy HPC. Rozszerzenia mogą wymagać pewnych wymagań dostępu do sieci, na przykład komunikacji z usługą Azure Storage lub usługami publicznymi. Innym przykładem może być dostęp do repozytoriów pakietów, brak miejsca na dysku lub ograniczenia zabezpieczeń.

3. Wyłączny dostęp do Menedżera pakietów. W niektórych przypadkach może wystąpić konfiguracja długotrwałej maszyny wirtualnej i rozwiązywanie konfliktów instalacji rozszerzeń, gdzie oba potrzebują wyłącznego dostępu do Menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetl stan rozszerzenia

Po uruchomieniu rozszerzenia maszyny wirtualnej na maszynie wirtualnej Użyj polecenie [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) , aby przywrócić stan rozszerzenia w następujący sposób:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Dane wyjściowe są podobne do następujących przykładowych danych wyjściowych:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Stan wykonania rozszerzenia można również znaleźć w Azure Portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz pozycję **rozszerzenia**, a następnie wybierz odpowiednie rozszerzenie.

### <a name="rerun-a-vm-extension"></a>Ponowne uruchamianie rozszerzenia maszyny wirtualnej

Mogą wystąpić sytuacje, w których rozszerzenie maszyny wirtualnej musi zostać ponownie uruchomione. Możesz ponownie uruchomić rozszerzenie, usuwając je, a następnie uruchamiając ponownie rozszerzenie z wybraną metodą wykonywania. Aby usunąć rozszerzenie, użyj [AZ VM Extension Delete](/cli/azure/vm/extension#az-vm-extension-delete) w następujący sposób:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Możesz również usunąć rozszerzenie w Azure Portal w następujący sposób:

1. Wybierz maszynę wirtualną.
2. Wybierz **rozszerzenia**.
3. Wybierz żądane rozszerzenie.
4. Wybierz **Odinstaluj**.

## <a name="common-vm-extension-reference"></a>Wspólne informacje o rozszerzeniu maszyny wirtualnej

| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Niestandardowe rozszerzenie skryptu dla systemu Linux |Uruchamianie skryptów na maszynie wirtualnej platformy Azure |[Niestandardowe rozszerzenie skryptu dla systemu Linux](custom-script-linux.md) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskaj dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyki Azure |Zarządzaj Diagnostyka Azure |[Rozszerzenie Diagnostyki Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie dostępu do maszyny wirtualnej platformy Azure |Zarządzanie użytkownikami i poświadczeniami |[Rozszerzenie dostępu do maszyny wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [Omówienie rozszerzeń i funkcji maszyny wirtualnej platformy Azure](overview.md).
