---
title: Wdrażanie hybrydowego procesu roboczego runbook systemu Linux w Azure Automation
description: W tym artykule opisano sposób instalowania hybrydowego procesu roboczego Azure Automation Runbook w celu uruchamiania elementów Runbook na maszynach z systemem Linux w lokalnym centrum danych lub środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833656"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego runbook systemu Linux

Możesz użyć funkcji hybrydowego procesu roboczego elementu Runbook użytkownika programu Azure Automation, aby uruchamiać element Runbook bezpośrednio na maszynie platformy Azure lub spoza platformy Azure, w tym na serwerach zarejestrowanych Azure Arc [serwerach z obsługą usługi](../azure-arc/servers/overview.md). Na maszynie lub serwerze hostującym rolę można uruchamiać je bezpośrednio i względem zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi.

Hybrydowy proces roboczy runbook systemu Linux wykonuje podręczniki Runbook jako specjalny użytkownik z podwyższonym poziomem uprawnień w przypadku uruchamiania poleceń, które wymagają podniesienia uprawnień. Azure Automation przechowuje i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczonej maszyny. W tym artykule opisano sposób instalowania hybrydowego procesu roboczego runbook na maszynie z systemem Linux, usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych runbook.

Po pomyślnym wdrożeniu procesu roboczego runbook zapoznaj się z tematem Runbook on a Hybrid Runbook Worker (Uruchamianie w hybrydowym procesów roboczych [runbook),](automation-hrw-run-runbooks.md) aby dowiedzieć się, jak skonfigurować swoje podręczniki runbook w celu zautomatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy.

### <a name="a-log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics

Instalacja i konfiguracja roli hybrydowego procesu roboczego Azure Monitor usługi Log Analytics zależy od konfiguracji roli. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub w Azure Portal [.](../azure-monitor/logs/quick-create-workspace.md)

Jeśli nie masz obszaru roboczego usługi Log Analytics Azure Monitor, przed utworzeniem obszaru roboczego zapoznaj się ze wskazówkami Azure Monitor [Log Log.](../azure-monitor/logs/design-logs-deployment.md)

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowego procesu roboczego runbook wymaga agenta [usługi Log Analytics](../azure-monitor/agents/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Linux. W przypadku serwerów lub maszyn hostowanych poza platformą Azure można zainstalować agenta usługi Log Analytics przy użyciu Azure Arc serwerów z [obsługą usługi](../azure-arc/servers/overview.md).

>[!NOTE]
>Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux nie należy zmieniać uprawnień `sudoers.d` folderu ani jego własności. Uprawnienie Sudo jest wymagane dla konta **nxautomation,** które jest kontekstem użytkownika, w ramach którego działa hybrydowy proces roboczy runbook. Uprawnienia nie powinny być usuwane. Ograniczenie tego do niektórych folderów lub poleceń może spowodować zmianę przerywania.
>

### <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące dystrybucje. Zakłada się, że wszystkie systemy operacyjne to x64. X86 nie jest obsługiwane dla żadnego systemu operacyjnego.

* Amazon Linux od 2012.09 do 2015.09
* CentOS Linux 5, 6, 7 i 8
* Oracle Linux 5, 6 i 7
* Red Hat Enterprise Linux Server 5, 6, 7 i 8
* Debian GNU/Linux 6, 7 i 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS i 18.04 LTS
* SUSE Linux Enterprise Server 12 i 15 (SUSE nie wydania wersji numerowanych 13 lub 14)

> [!IMPORTANT]
> Przed włączeniem funkcji Update Management, która zależy od roli hybrydowego procesu roboczego elementu Runbook systemu, potwierdź dystrybucje, które obsługuje [w tym miejscu.](update-management/overview.md#supported-operating-systems)

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dotyczące systemu Linux i hybrydowego procesu roboczego runbook użytkownika to:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

| **Wymagany pakiet** | **Opis** | **Minimalna wersja**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka GNU C| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1.0 (obsługiwane są TLS 1.1 i TLS 1.2)|
|Narzędzie Curl | Klient internetowy programu cURL | 7.15.5|
|Python-ctypes | Wymagany jest język Python 2.x lub Python 3.x |
|PAM | Podłączane moduły uwierzytelniania (PAM)|
| **Opcjonalny pakiet** | **Opis** | **Minimalna wersja**|
| Program PowerShell Core | Aby można było uruchamiać runbook programu PowerShell, PowerShell Core musi być zainstalowany program . Zobacz [Instalowanie PowerShell Core w systemie Linux,](/powershell/scripting/install/installing-powershell-core-on-linux) aby dowiedzieć się, jak go zainstalować. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Dodawanie maszyny do grupy hybrydowych procesów roboczych runbook

Maszynę procesu roboczego można dodać do grupy hybrydowych procesów roboczych runbook na jednym z kont usługi Automation. W przypadku maszyn hostujących systemowy hybrydowy proces roboczy runbook zarządzany przez Update Management można je dodać do grupy hybrydowych procesów roboczych runbook. Należy jednak używać tego samego konta usługi Automation zarówno dla Update Management, jak i członkostwa w grupie hybrydowych procesów roboczych runbook.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatycznie instaluje systemowy hybrydowy proces roboczy runbook na maszynie platformy Azure lub spoza platformy Azure, która jest włączona dla Update Management. Jednak ten proces roboczy nie jest zarejestrowany w żadnych grupach hybrydowych procesów roboczych runbook na twoim koncie usługi Automation. Aby uruchamiać swoje runbook na tych maszynach, należy dodać je do grupy hybrydowych procesów roboczych runbook. Wykonaj krok 4 w sekcji Instalowanie hybrydowego procesu [roboczego runbook systemu Linux,](#install-a-linux-hybrid-runbook-worker) aby dodać go do grupy.

## <a name="supported-linux-hardening"></a>Obsługiwane wzmacnianie zabezpieczeń systemu Linux

Następujące elementy nie są jeszcze obsługiwane:

* Cis

## <a name="supported-runbook-types"></a>Obsługiwane typy elementów Runbook

Hybrydowe elementy runbook systemu Linux obsługują ograniczony zestaw typów elementów Runbook w Azure Automation i zostały opisane w poniższej tabeli.

|Typ runbook | Obsługiwane |
|-------------|-----------|
|Python 3 (wersja zapoznawcza)|Tak, wymagane tylko dla tych dystrybucji: SUSE LES 15, RHEL 8 i CentOS 8|
|Python 2 |Tak, dla każdej dystrybucji, która nie wymaga języka Python 3<sup>1</sup> |
|PowerShell |Tak<sup>2</sup> |
|Przepływ pracy programu PowerShell |Nie |
|Element graficzny |Nie |
|Graficzny przepływ pracy programu PowerShell |Nie |

<sup>1</sup> Zobacz [Obsługiwane systemy operacyjne Linux.](#supported-linux-operating-systems)

<sup>2.</sup> Na komputerze z systemem Linux PowerShell Core runbook programu PowerShell wymagane jest PowerShell Core programu . Zobacz [Instalowanie PowerShell Core w systemie Linux,](/powershell/scripting/install/installing-powershell-core-on-linux) aby dowiedzieć się, jak go zainstalować.

### <a name="network-configuration"></a>Konfiguracja sieci

Aby uzyskać informacje o wymaganiach dotyczących sieci dla hybrydowego procesu roboczego runbook, [zobacz Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego runbook systemu Linux

Istnieją dwie metody wdrażania hybrydowego procesu roboczego runbook. Można zaimportować i uruchomić runbook z galerii runbook w programie Azure Portal lub ręcznie uruchomić serię poleceń programu PowerShell, aby wykonać to samo zadanie.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importowanie runbook z galerii runbook

Procedura importowania jest szczegółowo opisana w tesłudze [Import runbook from GitHub with the Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). Nazwa importowanego pliku runbook to Create Automation Linux HybridWorker (Utwórz **automatyzację w systemie Linux HybridWorker).**

Elementy Runbook wykorzystują następujące parametry.

| Parametr | Stan | Opis |
| ------- | ----- | ----------- |
| `Location` | Obowiązkowy | Lokalizacja obszaru roboczego usługi Log Analytics. |
| `ResourceGroupName` | Obowiązkowy | Grupa zasobów dla konta usługi Automation. |
| `AccountName` | Obowiązkowy | Nazwa konta usługi Automation, w którym zostanie zarejestrowany hybrydowy proces roboczy uruchamiania. |
| `CreateLA` | Obowiązkowy | W przypadku wartości true program używa wartości `WorkspaceName` w celu utworzenia obszaru roboczego usługi Log Analytics. W przypadku wartości false wartość musi `WorkspaceName` odwoływać się do istniejącego obszaru roboczego. |
| `LAlocation` | Opcjonalne | Lokalizacja, w której zostanie utworzony obszar roboczy usługi Log Analytics lub gdzie już istnieje. |
| `WorkspaceName` | Opcjonalne | Nazwa obszaru roboczego usługi Log Analytics, który ma zostać utworzony lub używany. |
| `CreateVM` | Obowiązkowy | W przypadku wartości true użyj wartości `VMName` jako nazwy nowej maszyny wirtualnej. Jeśli wartość false, użyj funkcji `VMName` , aby znaleźć i zarejestrować istniejącą maszynę wirtualną. |
| `VMName` | Opcjonalne | Nazwa utworzonej lub zarejestrowanej maszyny wirtualnej w zależności od wartości `CreateVM` . |
| `VMImage` | Opcjonalne | Nazwa obrazu maszyny wirtualnej do utworzenia. |
| `VMlocation` | Opcjonalne | Lokalizacja maszyny wirtualnej, która została utworzona lub zarejestrowana. Jeśli ta lokalizacja nie jest określona, używana jest `LAlocation` wartość . |
| `RegisterHW` | Obowiązkowy | W przypadku wartości true zarejestruj maszynę wirtualną jako hybrydowy proces roboczy. |
| `WorkerGroupName` | Obowiązkowy | Nazwa grupy hybrydowych procesów roboczych. |

### <a name="manually-run-powershell-commands"></a>Ręczne uruchamianie poleceń programu PowerShell

Aby zainstalować i skonfigurować hybrydowy proces roboczy runbook systemu Linux, wykonaj następujące kroki.

1. Włącz rozwiązanie Azure Automation w obszarze roboczym usługi Log Analytics, uruchamiając następujące polecenie w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień lub Cloud Shell w Azure Portal [:](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Wd wdrażać agenta usługi Log Analytics na maszynie docelowej.

    * W przypadku maszyn wirtualnych platformy Azure zainstaluj agenta usługi Log Analytics dla systemu Linux przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Linux.](../virtual-machines/extensions/oms-linux.md) Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym usługi Log Analytics. Możesz użyć szablonu usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub usługi Azure Policy, aby przypisać wbudowane zasady Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Linux lub [ *Windows.*](../governance/policy/samples/built-in-policies.md#monitoring) Po zainstalowaniu agenta można dodać maszynę do grupy hybrydowych procesów roboczych runbook na koncie usługi Automation.

    * W przypadku maszyn spoza platformy Azure można zainstalować agenta usługi Log Analytics przy użyciu [Azure Arc serwerów z obsługą usługi](../azure-arc/servers/overview.md). Serwery z usługą Arc obsługują wdrażanie agenta usługi Log Analytics przy użyciu następujących metod:

        - Korzystanie z platformy rozszerzeń maszyn wirtualnych.

            Ta funkcja na serwerach Azure Arc umożliwia wdrożenie rozszerzenia maszyny wirtualnej agenta usługi Log Analytics na serwerze z systemami Windows i/lub Linux spoza platformy Azure. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z usługą Arc:

            - Azure Portal [](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Interfejs [wiersza polecenia platformy Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Szablony [Resource Manager Azure](../azure-arc/servers/manage-vm-extensions-template.md)

        - Przy użyciu Azure Policy.

            Korzystając z tej metody Azure Policy, należy użyć wbudowanych zasad wdrażania agenta usługi Log Analytics na maszynach z systemem Linux lub [Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) w celu inspekcji, czy na serwerze z usługą Arc jest zainstalowany agent usługi Log Analytics. Jeśli agent nie jest zainstalowany, automatycznie wdraża go przy użyciu zadania korygowania. Alternatywnie, jeśli planujesz monitorowanie maszyn za pomocą usługi Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy Włącz usługę [Azure Monitor dla maszyn wirtualnych,](../governance/policy/samples/built-in-initiatives.md#monitoring) aby zainstalować i skonfigurować agenta usługi Log Analytics.

        Zalecamy zainstalowanie agenta usługi Log Analytics dla systemu Windows lub Linux przy użyciu Azure Policy.

    > [!NOTE]
    > Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowych procesów roboczych runbook za pomocą Desired State Configuration (DSC), należy dodać maszyny jako węzły DSC.

    > [!NOTE]
    > Konto [nxautomation z](automation-runbook-execution.md#log-analytics-agent-for-linux) odpowiednimi uprawnieniami sudo musi być obecne podczas instalacji hybrydowego procesu roboczego systemu Linux. Jeśli spróbujesz zainstalować proces roboczy, a konto nie istnieje lub nie ma odpowiednich uprawnień, instalacja zakończy się niepowodzeniem.

3. Sprawdź, czy agent raportuje do obszaru roboczego.

    Agent usługi Log Analytics dla systemu Linux łączy maszyny z Azure Monitor usługi Log Analytics. Po zainstalowaniu agenta na maszynie i połączeniu go z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementów Runbook.

    Po kilku minutach pomyślnego połączenia agenta z obszarem roboczym usługi Log Analytics możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    W wynikach wyszukiwania powinny zostać wyświetlony rekordy pulsu dla maszyny wskazujące, że maszyna jest połączona i raportuje do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego mu obszaru roboczego.

4. Uruchom następujące polecenie, aby dodać maszynę do grupy hybrydowych procesów roboczych runbook, określając wartości parametrów `-w` , `-k` , i `-g` `-e` .

    Informacje wymagane dla parametrów oraz ze strony Klucze na `-k` `-e` koncie usługi Automation.  Wybierz **pozycję Klucze** w sekcji Ustawienia **konta** w lewej części strony.

    ![Strona Zarządzanie kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Dla `-e` parametru skopiuj wartość adresu **URL**.

    * Dla `-k` parametru skopiuj wartość **PRIMARY ACCESS KEY (PODSTAWOWY KLUCZ DOSTĘPU).**

    * Dla parametru określ nazwę grupy hybrydowych procesów roboczych runbook, do których ma dołączyć nowy hybrydowy proces roboczy `-g` runbook systemu Linux. Jeśli ta grupa już istnieje na koncie usługi Automation, zostanie do niego dodana bieżąca maszyna. Jeśli ta grupa nie istnieje, zostanie utworzona z tą nazwą.

    * Dla `-w` parametru określ identyfikator obszaru roboczego usługi Log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Sprawdź wdrożenie po zakończeniu skryptu. Na stronie **Grupy hybrydowych procesów** roboczych elementów Runbook na koncie usługi Automation na karcie Grupa hybrydowych procesów roboczych elementów **Runbook** użytkownika jest przedstawiana nowa lub istniejąca grupa oraz liczba członków. Jeśli jest to istniejąca grupa, liczba członków jest zwiększana. Grupę można wybrać z listy na stronie. W menu po lewej stronie wybierz pozycję **Hybrydowe pracownicy.** Na stronie **Hybrydowe pracownicy** można zobaczyć każdego członka grupy na liście.

    > [!NOTE]
    > Jeśli używasz rozszerzenia maszyny wirtualnej usługi Log Analytics dla systemu Linux dla maszyny wirtualnej platformy Azure, zalecamy ustawienie na , ponieważ wersje automatycznego uaktualniania mogą powodować problemy z hybrydowym `autoUpgradeMinorVersion` `false` procesem roboczym runbook. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenie, zobacz Wdrażanie interfejsu [wiersza polecenia platformy Azure.](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)

## <a name="turn-off-signature-validation"></a>Wyłączanie weryfikacji podpisu

Domyślnie hybrydowe pracownicy runbook systemu Linux wymagają weryfikacji podpisu. Jeśli uruchamiasz niepodpisane element Runbook względem procesu roboczego, zostanie wyświetlony `Signature validation failed` błąd. Aby wyłączyć weryfikację podpisu, uruchom następujące polecenie. Zastąp drugi parametr identyfikatorem obszaru roboczego usługi Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego runbook

Aby uzyskać identyfikator obszaru roboczego, możesz użyć polecenia hybrydowego procesu roboczego `ls /var/opt/microsoft/omsagent` runbook. Zostanie utworzony folder o nazwie z identyfikatorem obszaru roboczego.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten skrypt nie usuwa agenta usługi Log Analytics dla systemu Linux z maszyny. Usuwa tylko funkcje i konfigurację roli hybrydowego procesu roboczego runbook.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych runbook maszyn z systemem Linux, należy wykonać te same kroki co w przypadku grupy hybrydowych procesów roboczych systemu Windows. Zobacz [Usuwanie grupy hybrydowych procesów roboczych.](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować swoje runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz Runbook on a Hybrid Runbook Worker (Uruchamianie runbook w hybrydowym procesu [roboczego runbook).](automation-hrw-run-runbooks.md)

* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi procesami roboczymi runbook, zobacz Troubleshoot Hybrid Runbook Worker issues - Linux (Rozwiązywanie problemów z hybrydowymi procesami [roboczymi runbook — Linux).](troubleshoot/hybrid-runbook-worker.md#linux)
