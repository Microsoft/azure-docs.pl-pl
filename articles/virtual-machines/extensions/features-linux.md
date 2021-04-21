---
title: Rozszerzenia i funkcje maszyn wirtualnych platformy Azure dla systemu Linux
description: Dowiedz się, jakie rozszerzenia są dostępne dla maszyn wirtualnych platformy Azure w systemie Linux pogrupowane według ich zapewnień lub ulepszeń.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: bdbbc4c421b83fd041c7d900fb0edd01c4d636e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785095"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Na przykład jeśli maszyna wirtualna wymaga instalacji oprogramowania, ochrony antywirusowej lub uruchomienia skryptu w swoim środowisku, można użyć rozszerzenia maszyny wirtualnej. Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, szablonów usługi Azure Resource Manager i witryny Azure Portal. Rozszerzenia można połączyć w pakiecie z nowym wdrożeniem nowej maszyny wirtualnej lub uruchamiać w dowolnym istniejącym systemie.

Ten artykuł zawiera omówienie rozszerzeń maszyn wirtualnych, wymagań wstępnych dotyczących korzystania z rozszerzeń maszyn wirtualnych platformy Azure oraz wskazówek dotyczących wykrywania i usuwania rozszerzeń maszyn wirtualnych oraz zarządzania nimi. Ten artykuł zawiera uogólnione informacje, ponieważ dostępnych jest wiele rozszerzeń maszyn wirtualnych, z których każde ma potencjalnie unikatową konfigurację. Szczegóły specyficzne dla rozszerzenia można znaleźć w poszczególnych dokumentach specyficznych dla poszczególnych rozszerzeń.

## <a name="use-cases-and-samples"></a>Przypadki użycia i przykłady

Dostępnych jest kilka różnych rozszerzeń maszyn wirtualnych platformy Azure, z których każde ma określony przypadek użycia. Oto niektóre przykłady:

- Zastosuj konfiguracje żądanego stanu programu PowerShell do maszyny wirtualnej z rozszerzeniem DSC dla systemu Linux. Aby uzyskać więcej informacji, zobacz [Azure Desired State configuration extension (Rozszerzenie konfiguracji żądanego stanu platformy Azure).](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)
- Konfigurowanie monitorowania maszyny wirtualnej przy użyciu Microsoft Monitoring Agent maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak monitorować maszynę wirtualną z systemem Linux.](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)
- Konfigurowanie monitorowania infrastruktury platformy Azure przy użyciu rozszerzenia Chef lub Datadog. Aby uzyskać więcej informacji, zobacz blog [chef docs](https://docs.chef.io/azure_portal.html) lub [datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Oprócz rozszerzeń specyficznych dla procesu, rozszerzenie niestandardowego skryptu jest dostępne zarówno dla maszyn wirtualnych z systemem Windows, jak i Linux. Rozszerzenie niestandardowego skryptu dla systemu Linux umożliwia uruchamianie dowolnego skryptu powłoki Bash na maszynie wirtualnej. Skrypty niestandardowe są przydatne do projektowania wdrożeń platformy Azure, które wymagają konfiguracji wykraczających poza to, co mogą zapewnić natywne narzędzia platformy Azure. Aby uzyskać więcej informacji, zobacz [Rozszerzenie niestandardowego skryptu maszyny wirtualnej z systemem Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby obsłużyć rozszerzenie na maszynie wirtualnej, musisz mieć zainstalowanego agenta platformy Azure dla systemu Linux. Niektóre poszczególne rozszerzenia mają wymagania wstępne, takie jak dostęp do zasobów lub zależności.

### <a name="azure-vm-agent"></a>Agent maszyny wirtualnej platformy Azure

Agent maszyny wirtualnej platformy Azure zarządza interakcjami między maszyną wirtualną platformy Azure a kontrolerem sieci szkieletowej platformy Azure. Agent maszyny wirtualnej jest odpowiedzialny za wiele aspektów funkcjonalnych wdrażania maszyn wirtualnych platformy Azure i zarządzania nimi, w tym za uruchamianie rozszerzeń maszyn wirtualnych. Agent maszyny wirtualnej platformy Azure jest wstępnie zainstalowany na obrazach Azure Marketplace i można go zainstalować ręcznie w obsługiwanych systemach operacyjnych. Agent maszyny wirtualnej platformy Azure dla systemu Linux jest znany jako agent systemu Linux.

Aby uzyskać informacje na temat obsługiwanych systemów operacyjnych i instrukcje dotyczące instalacji, zobacz [Agent maszyny wirtualnej platformy Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Obsługiwane wersje agentów

Aby zapewnić najlepsze możliwe środowisko, istnieją minimalne wersje agenta. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Obsługiwane system operacyjny

Agent systemu Linux działa na wielu systemach operacyjnych, jednak w ramach struktury rozszerzeń obowiązuje limit dla tych systemów operacyjnych. Więcej informacji znajduje się w [tym artykule](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Niektóre rozszerzenia nie są obsługiwane we wszystkich systemach operacyjnych i mogą emitować kod *błędu 51 "Nieobsługiwany system operacyjny".* Zapoznaj się z dokumentacją poszczególnych rozszerzeń, aby uzyskać informacje na temat możliwości obsługi.

#### <a name="network-access"></a>Dostęp do sieci

Pakiety rozszerzeń są pobierane z repozytorium rozszerzeń usługi Azure Storage, a przekazywanie stanu rozszerzenia jest publikowane w usłudze Azure Storage. Jeśli używasz [obsługiwanej](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) wersji agentów, nie musisz zezwalać na dostęp do usługi Azure Storage w regionie maszyny wirtualnej, ponieważ możesz użyć agenta do przekierowania komunikacji do kontrolera sieci szkieletowej platformy Azure na potrzeby komunikacji z agentem. Jeśli używasz obsługiwanej wersji agenta, musisz zezwolić na dostęp wychodzący do usługi Azure Storage w tym regionie z maszyny wirtualnej.

> [!IMPORTANT]
> Jeśli zablokowano dostęp do *usługi 168.63.129.16 przy* użyciu zapory gościa, rozszerzenia nie powiodą się niezależnie od powyższych.

Agentów można używać tylko do pobierania pakietów rozszerzeń i stanu raportowania. Jeśli na przykład instalacja rozszerzenia musi pobrać skrypt z usługi GitHub (skrypt niestandardowy) lub wymaga dostępu do usługi Azure Storage (Azure Backup), należy otworzyć dodatkowe porty zapory/sieciowej grupy zabezpieczeń. Różne rozszerzenia mają różne wymagania, ponieważ są aplikacjami samodzielnie. W przypadku rozszerzeń, które wymagają dostępu do usługi Azure Storage, możesz zezwolić na dostęp przy użyciu tagów usługi sieciowej platformy Azure dla [magazynu](../../virtual-network/network-security-groups-overview.md#service-tags).

Aby przekierowywać żądania ruchu agenta, agent systemu Linux obsługuje serwer proxy. Jednak ta obsługa serwera proxy nie stosuje rozszerzeń. Należy skonfigurować poszczególne rozszerzenia do pracy z serwerem proxy.

## <a name="discover-vm-extensions"></a>Odnajdywanie rozszerzeń maszyn wirtualnych

Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. Aby wyświetlić pełną listę, użyj [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list). W poniższym przykładzie wymieniono wszystkie dostępne rozszerzenia w *lokalizacji westus:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Uruchamianie rozszerzeń maszyn wirtualnych

Rozszerzenia maszyn wirtualnych platformy Azure działają na istniejących maszynach wirtualnych, co jest przydatne, gdy trzeba wprowadzić zmiany w konfiguracji lub odzyskać łączność na już wdrożonej maszynie wirtualnej. Rozszerzenia maszyn wirtualnych można również dołączać do Azure Resource Manager wdrożeń szablonów. Korzystając z rozszerzeń z Resource Manager szablonów, maszyny wirtualne platformy Azure można wdrażać i konfigurować bez interwencji po wdrożeniu.

Następujące metody mogą służyć do uruchamiania rozszerzenia dla istniejącej maszyny wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Rozszerzenia maszyn wirtualnych platformy Azure można uruchamiać względem istniejącej maszyny wirtualnej za pomocą [polecenia az vm extension set.](/cli/azure/vm/extension#az_vm_extension_set) Poniższy przykład uruchamia rozszerzenie niestandardowego skryptu dla maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup*. Zastąp przykładową nazwę grupy zasobów, nazwę maszyny wirtualnej i skrypt do uruchomienia (https: \/ /raw.githubusercontent.com/me/project/hello.sh) własnymi informacjami. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Gdy rozszerzenie działa poprawnie, dane wyjściowe są podobne do następującego przykładu:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

Rozszerzenia maszyn wirtualnych można stosować do istniejącej maszyny wirtualnej za pośrednictwem Azure Portal. Wybierz maszynę wirtualną w portalu, wybierz **pozycję Rozszerzenia,** a następnie wybierz pozycję **Dodaj.** Wybierz rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami w kreatorze.

Na poniższej ilustracji przedstawiono instalację rozszerzenia niestandardowego skryptu systemu Linux z Azure Portal:

![Instalowanie rozszerzenia niestandardowego skryptu](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Rozszerzenia maszyn wirtualnych można dodać do Azure Resource Manager szablonu i wykonać wraz z wdrożeniem szablonu. Podczas wdrażania rozszerzenia za pomocą szablonu można tworzyć w pełni skonfigurowane wdrożenia platformy Azure. Na przykład poniższy kod JSON pochodzi z szablonu usługi Resource Manager, który wdraża zestaw maszyn wirtualnych o zrównoważonym obciążeniu i Azure SQL Database, a następnie instaluje aplikację platformy .NET Core na każdej maszynie wirtualnej. Rozszerzenie maszyny wirtualnej zajmuje się instalacją oprogramowania.

Aby uzyskać więcej informacji, zobacz pełny [Resource Manager szablonu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Aby uzyskać więcej informacji na temat tworzenia Resource Manager szablonów, zobacz [Authoring Azure Resource Manager templates (Tworzenie Azure Resource Manager szablonów).](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Zabezpieczanie danych rozszerzenia maszyny wirtualnej

Po uruchomieniu rozszerzenia maszyny wirtualnej może być konieczne dołączenie poufnych informacji, takich jak poświadczenia, nazwy kont magazynu i klucze dostępu do konta magazynu. Wiele rozszerzeń maszyn wirtualnych zawiera chronioną konfigurację, która szyfruje dane i odszyfrowuje je tylko wewnątrz docelowej maszyny wirtualnej. Każde rozszerzenie ma określony chroniony schemat konfiguracji, a każde z nich jest szczegółowo opisane w dokumentacji specyficznej dla rozszerzenia.

W poniższym przykładzie pokazano wystąpienie rozszerzenia niestandardowego skryptu dla systemu Linux. Polecenie do wykonania zawiera zestaw poświadczeń. W tym przykładzie polecenie do wykonania nie jest szyfrowane:

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

Przeniesienie polecenia **w celu wykonania** właściwości do **chronionej** konfiguracji zabezpiecza ciąg wykonywania, jak pokazano w poniższym przykładzie:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak agenci i rozszerzenia są aktualizowane?

Agenci i rozszerzenia współdzielą ten sam mechanizm aktualizacji. Niektóre aktualizacje nie wymagają dodatkowych reguł zapory.

Gdy aktualizacja jest dostępna, jest ona instalowana na maszynie wirtualnej tylko w przypadku zmiany rozszerzeń, a inne zmiany modelu maszyny wirtualnej, takie jak:

- Dyski danych
- Rozszerzenia
- Kontener diagnostyki rozruchu
- Wpisy tajne systemu operacyjnego gościa
- Rozmiar maszyny wirtualnej
- Profil sieci

Wydawcy publikują aktualizacje w różnych regionach w różnych momentach, więc możliwe jest, że maszyny wirtualne mogą być w różnych regionach w różnych wersjach.

#### <a name="agent-updates"></a>Aktualizacje agenta

Agent maszyny wirtualnej z systemem Linux *zawiera* kod agenta aprowizowania i kod obsługi rozszerzeń *w*  jednym pakiecie, którego nie można oddzielić. Agenta aprowizowania można *wyłączyć,* jeśli chcesz aprowizować go na platformie Azure przy użyciu narzędzia cloud-init. Aby to zrobić, zobacz [używanie cloud-init](../linux/using-cloud-init.md).

Obsługiwane wersje agentów mogą używać aktualizacji automatycznych. Jedynym kodem, który można zaktualizować, jest *kod obsługi* rozszerzeń, a nie kod aprowizowania. Kod *agenta aprowowania to* kod uruchamiany raz.

Kod *obsługi rozszerzeń* jest odpowiedzialny za komunikację z siecią szkieletową platformy Azure i obsługę operacji rozszerzeń maszyn wirtualnych, takich jak instalowanie, raportowanie stanu, aktualizowanie poszczególnych rozszerzeń i usuwanie ich. Aktualizacje zawierają poprawki zabezpieczeń, poprawki błędów i ulepszenia kodu *obsługi rozszerzeń*.

Po zainstalowaniu agenta jest tworzony demon nadrzędny. Ten element nadrzędny następnie du zduplikowane proces podrzędny, który jest używany do obsługi rozszerzeń. Jeśli aktualizacja jest dostępna dla agenta, zostanie pobrana, nadrzędny zatrzymuje proces podrzędny, uaktualnia go, a następnie uruchamia go ponownie. Jeśli występuje problem z aktualizacją, proces nadrzędny jest wycofywał poprzednią wersję podrzędną.

Nie można automatycznie zaktualizować procesu nadrzędnego. Element nadrzędny można zaktualizować tylko przez aktualizację pakietu dystrybucji.

Aby sprawdzić, która wersja jest uruchomiona, sprawdź `waagent` następujące informacje:

```bash
waagent --version
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

W poprzednich przykładowych danych wyjściowych wersją nadrzędną lub "wdrożoną wersją pakietu" jest *WALinuxAgent-2.2.17*

"Agent stanu celu" to wersja automatycznej aktualizacji.

Zdecydowanie zaleca się, aby zawsze mieć automatyczną aktualizację agenta [AutoUpdate.Enabled=y](./update-linux-agent.md). Jeśli ta opcja nie jest włączona, oznacza to, że należy ręcznie aktualizować agenta, a nie uzyskać poprawek błędów i zabezpieczeń.

#### <a name="extension-updates"></a>Aktualizacje rozszerzeń

Gdy aktualizacja rozszerzenia jest dostępna, agent systemu Linux pobiera i uaktualnia rozszerzenie. Automatyczne aktualizacje rozszerzeń to aktualizacje *pomocnicze* *lub poprawki.* Podczas aprowizować rozszerzenie możesz  zrezygnować z rozszerzeń lub z nich zrezygnować. W poniższym przykładzie pokazano, jak automatycznie uaktualnić wersje pomocnicze w szablonie Resource Manager za pomocą *funkcji autoUpgradeMinorVersion": true,'*:

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

Aby uzyskać najnowsze poprawki drobnych usterek wydania, zdecydowanie zaleca się, aby zawsze wybierać automatyczne aktualizacje we wdrożeniach rozszerzeń. Nie można zrezygnować z aktualizacji poprawek, które obejmują poprawki zabezpieczeń lub kluczowych usterek.

### <a name="how-to-identify-extension-updates"></a>Jak identyfikować aktualizacje rozszerzeń

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Określanie, czy rozszerzenie jest ustawione za pomocą opcji autoUpgradeMinorVersion na maszynie wirtualnej

Z modelu maszyny wirtualnej można zobaczyć, czy rozszerzenie zostało aprowowane za pomocą opcji "autoUpgradeMinorVersion". Aby to sprawdzić, użyj [az vm show](/cli/azure/vm#az_vm_show) i podaj grupę zasobów oraz nazwę maszyny wirtualnej w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Następujące przykładowe dane wyjściowe pokazują, że dla ustawienia *autoUpgradeMinorVersion* ustawiono wartość *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identyfikowanie, kiedy wystąpiła opcja autoUpgradeMinorVersion

Aby sprawdzić, kiedy wystąpiła aktualizacja rozszerzenia, przejrzyj dzienniki agenta na maszynie wirtualnej pod *plikiem /var/log/waagent.log.*

W poniższym przykładzie na maszynie wirtualnej zainstalowano pakiet *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025.* Poprawka była dostępna dla *microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

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

Aby wykonać swoje zadania, agent musi działać jako *główny .*

## <a name="troubleshoot-vm-extensions"></a>Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych

Każde rozszerzenie maszyny wirtualnej może zawierać kroki rozwiązywania problemów specyficzne dla tego rozszerzenia. Na przykład w przypadku korzystania z rozszerzenia niestandardowego skryptu szczegóły wykonywania skryptu można znaleźć lokalnie na maszynie wirtualnej, na której zostało uruchomione rozszerzenie. Wszystkie kroki rozwiązywania problemów specyficzne dla rozszerzenia są szczegółowo opisane w dokumentacji specyficznej dla rozszerzenia.

Poniższe kroki rozwiązywania problemów dotyczą wszystkich rozszerzeń maszyn wirtualnych.

1. Aby sprawdzić dziennik agenta systemu Linux, przyjrzyj się działaniu, w którym aprowizujesz rozszerzenie w *pliku /var/log/waagent.log*

2. Sprawdź rzeczywiste dzienniki rozszerzeń, aby uzyskać więcej informacji w */var/log/azure/ \<extensionName>*

3. Zapoznaj się z sekcjami rozwiązywania problemów z dokumentacją specyficzną dla rozszerzenia, aby uzyskać kody błędów, znane problemy itp.

3. Przyjrzyj się dziennikom systemu. Sprawdź inne operacje, które mogą zakłócać rozszerzenie, takie jak długotrwała instalacja innej aplikacji, która wymagała wyłącznego dostępu do menedżera pakietów.

### <a name="common-reasons-for-extension-failures"></a>Typowe przyczyny błędów rozszerzeń

1. Rozszerzenia mają do uruchomienia 20 minut (wyjątki to rozszerzenia CustomScript, chef i DSC, które mają 90 minut). Jeśli wdrożenie przekroczy ten czas, zostanie oznaczone jako limit czasu. Przyczyną może być niska ilość zasobów maszyn wirtualnych, inne konfiguracje/zadania uruchamiania maszyn wirtualnych zużywające duże ilości zasobów, podczas gdy rozszerzenie próbuje aprowizować.

2. Minimalne wymagania wstępne nie zostały spełnione. Niektóre rozszerzenia mają zależności od jednostki SKU maszyn wirtualnych, takie jak obrazy HPC. Rozszerzenia mogą wymagać pewnych wymagań dotyczących dostępu do sieci, takich jak komunikacja z usługą Azure Storage lub usługami publicznymi. Inne przykłady to dostęp do repozytoriów pakietów, zaoczestnia miejsce na dysku lub ograniczenia zabezpieczeń.

3. Wyłączny dostęp do menedżera pakietów. W niektórych przypadkach może wystąpić konflikt długotrwałej konfiguracji i instalacji rozszerzenia maszyny wirtualnej, w przypadku którego oba te urządzenia potrzebują wyłącznego dostępu do menedżera pakietów.

### <a name="view-extension-status"></a>Wyświetlanie stanu rozszerzenia

Po uruchomieniu rozszerzenia maszyny wirtualnej dla maszyny wirtualnej użyj az [vm get-instance-view,](/cli/azure/vm#az_vm_get_instance_view) aby zwrócić stan rozszerzenia w następujący sposób:

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

Stan wykonywania rozszerzenia można również znaleźć w Azure Portal. Aby wyświetlić stan rozszerzenia, wybierz maszynę wirtualną, wybierz pozycję **Rozszerzenia,** a następnie wybierz żądane rozszerzenie.

### <a name="rerun-a-vm-extension"></a>Ponowne uruchomić rozszerzenie maszyny wirtualnej

Mogą wystąpić przypadki, w których należy ponownie uruchomić rozszerzenie maszyny wirtualnej. Rozszerzenie można uruchomić ponownie, usuwając je, a następnie ponownie uruchomić rozszerzenie przy użyciu wybranej metody wykonywania. Aby usunąć rozszerzenie, użyj az [vm extension delete w](/cli/azure/vm/extension#az_vm_extension_delete) następujący sposób:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Możesz również usunąć rozszerzenie w Azure Portal w następujący sposób:

1. Wybierz maszynę wirtualną.
2. Wybierz **pozycję Rozszerzenia.**
3. Wybierz żądane rozszerzenie.
4. Wybierz pozycję **Odinstaluj.**

## <a name="common-vm-extension-reference"></a>Typowe informacje o rozszerzeniu maszyny wirtualnej

| Nazwa rozszerzenia | Opis | Więcej informacji |
| --- | --- | --- |
| Rozszerzenie niestandardowego skryptu dla systemu Linux |Uruchamianie skryptów dla maszyny wirtualnej platformy Azure |[Rozszerzenie niestandardowego skryptu dla systemu Linux](custom-script-linux.md) |
| Rozszerzenie dostępu do maszyny wirtualnej |Odzyskaj dostęp do maszyny wirtualnej platformy Azure |[Rozszerzenie dostępu do maszyny wirtualnej](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Rozszerzenie Diagnostyki Azure |Zarządzanie Diagnostyka Azure |[Rozszerzenie Diagnostyki Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozszerzenie dostępu do maszyny wirtualnej platformy Azure |Zarządzanie użytkownikami i poświadczeniami |[Rozszerzenie dostępu do maszyny wirtualnej dla systemu Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz Omówienie rozszerzeń i funkcji maszyn wirtualnych [platformy Azure.](overview.md)