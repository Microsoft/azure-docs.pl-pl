---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook w systemie Linux w Azure Automation
description: W tym artykule opisano sposób instalowania Azure Automation hybrydowego procesu roboczego elementu Runbook w celu uruchamiania elementów Runbook na maszynach z systemem Linux w lokalnym środowisku centrum danych lub w chmurze.
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: d4d9bcd16e36e76808f19f7fbd43dd0d3e7550c3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182336"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na platformie Azure lub na maszynach spoza platformy Azure, w tym na serwerach zarejestrowanych przy użyciu [usługi Azure ARC z obsługą serwerów](../azure-arc/servers/overview.md). Na komputerze lub serwerze, na którym znajduje się rola, można uruchamiać elementy Runbook bezpośrednio i z zasobami w środowisku, aby zarządzać tymi zasobami lokalnymi.

Hybrydowy proces roboczy elementu Runbook systemu Linux wykonuje elementy Runbook jako specjalny użytkownik, który może mieć podwyższony poziom uprawnień do uruchamiania poleceń wymagających podniesienia uprawnień. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczeniu maszyn. W tym artykule opisano sposób instalowania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Linux, sposobu usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych elementu Runbook.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy.

### <a name="a-log-analytics-workspace"></a>Obszar roboczy Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook zależy od obszaru roboczego Log Analytics Azure Monitor, aby zainstalować i skonfigurować rolę. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub [Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Jeśli nie masz obszaru roboczego Log Analytics Azure Monitor, zapoznaj się ze [wskazówkami dotyczącymi projektu dziennika Azure monitor](../azure-monitor/logs/design-logs-deployment.md) przed utworzeniem obszaru roboczego.

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook wymaga [agenta log Analytics](../azure-monitor/agents/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Linux. W przypadku serwerów lub maszyn hostowanych poza platformą Azure można zainstalować agenta Log Analytics przy użyciu [serwerów z obsługą usługi Azure Arc](../azure-arc/servers/overview.md).

>[!NOTE]
>Po zainstalowaniu agenta Log Analytics dla systemu Linux nie należy zmieniać uprawnień do `sudoers.d` folderu ani jego własności. Dla konta **nxautomation** jest wymagane uprawnienie sudo, które jest kontekstem użytkownika, w którym działa hybrydowy proces roboczy elementu Runbook. Uprawnień nie należy usuwać. Ograniczenie tego do określonych folderów lub poleceń może spowodować powstanie istotnej zmiany.
>

### <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące dystrybucje. Przyjmuje się, że wszystkie systemy operacyjne są w wersji x64. Architektura x86 nie jest obsługiwana dla żadnego systemu operacyjnego.

* Amazon Linux 2012,09 do 2015,09
* CentOS Linux 5, 6, 7 i 8
* Oracle Linux 5, 6 i 7
* Red Hat Enterprise Linux Server 5, 6, 7 i 8
* Debian GNU/Linux 6, 7 i 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS i 18,04 LTS
* SUSE Linux Enterprise Server 12 i 15 (SUSE nie wyprowadził wersji o numerze 13 lub 14)

> [!IMPORTANT]
> Przed włączeniem funkcji Update Management, która zależy od roli hybrydowego procesu roboczego elementu Runbook systemu, należy upewnić się, że dystrybucje są obsługiwane w [tym miejscu](update-management/overview.md#supported-operating-systems).

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dotyczące systemu Linux i hybrydowego procesu roboczego elementu Runbook użytkownika:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

| **Wymagany pakiet** | **Opis** | **Wersja minimalna**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka GNU C| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1,0 (obsługiwane są protokoły TLS 1,1 i TLS 1,2)|
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5|
|Python — ctypes | Wymagany jest język Python 2. x lub Python 3. x |
|PAM | Podłączane moduły uwierzytelniania (PAM)|
| **Opcjonalny pakiet** | **Opis** | **Wersja minimalna**|
| Program PowerShell Core | Aby można było uruchomić elementy Runbook programu PowerShell, należy zainstalować program PowerShell Core. Zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Dodawanie komputera do grupy hybrydowych procesów roboczych elementu Runbook

Możesz dodać maszynę procesu roboczego do grupy hybrydowych procesów roboczych elementu Runbook na jednym z kont usługi Automation. W przypadku maszyn obsługujących systemowy hybrydowy proces roboczy elementu Runbook zarządzany przez Update Management można je dodać do grupy hybrydowych procesów roboczych elementu Runbook. Należy jednak użyć tego samego konta usługi Automation zarówno dla Update Management, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatycznie instaluje hybrydowy proces roboczy elementu Runbook na platformie Azure lub na maszynie spoza platformy Azure, która jest włączona dla Update Management. Jednak ten proces roboczy nie jest zarejestrowany w żadnej z grup hybrydowych procesów roboczych elementu Runbook na Twoim koncie usługi Automation. Aby uruchomić elementy Runbook na tych komputerach, należy dodać je do grupy hybrydowych procesów roboczych elementu Runbook. Wykonaj krok 4 w sekcji [Instalowanie hybrydowego procesu roboczego elementu Runbook systemu Linux](#install-a-linux-hybrid-runbook-worker) w celu dodania go do grupy.

## <a name="supported-linux-hardening"></a>Obsługiwane Ograniczanie poziomu systemu Linux

Następujące elementy nie są jeszcze obsługiwane:

* SIC

## <a name="supported-runbook-types"></a>Obsługiwane typy elementów Runbook

Hybrydowe procesy robocze elementu Runbook systemu Linux obsługują ograniczoną liczbę typów elementów Runbook w Azure Automation i są opisane w poniższej tabeli.

|Typ elementu Runbook | Obsługiwane |
|-------------|-----------|
|Python 3 (wersja zapoznawcza)|Tak, wymagane tylko dla tych dystrybucje: SUSE LES 15, RHEL 8 i CentOS 8|
|Python 2 |Tak, w przypadku wszystkich dystrybucji, które nie wymagają języka Python 3<sup>1</sup> |
|PowerShell |Tak<sup>2</sup> |
|Przepływ pracy programu PowerShell |Nie |
|Element graficzny |Nie |
|Graficzny przepływ pracy programu PowerShell |Nie |

<sup>1</sup> Zobacz [obsługiwane systemy operacyjne Linux](#supported-linux-operating-systems).

<sup>2</sup> Elementy Runbook programu PowerShell wymagają zainstalowania programu PowerShell Core na komputerze z systemem Linux. Zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować.

### <a name="network-configuration"></a>Konfiguracja sieci

Wymagania dotyczące sieci dla hybrydowego procesu roboczego elementu Runbook można znaleźć w temacie [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Linux, wykonaj następujące czynności.

1. Włącz rozwiązanie Azure Automation w obszarze roboczym Log Analytics, uruchamiając następujące polecenie w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień lub w Cloud Shell w [Azure Portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Wdróż agenta Log Analytics na maszynie docelowej.

    * W przypadku maszyn wirtualnych platformy Azure Zainstaluj agenta Log Analytics dla systemu Linux przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Linux](../virtual-machines/extensions/oms-linux.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym Log Analytics obszarze roboczym. Można użyć szablonu Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub Azure Policy do przypisywania w Log Analytics ramach wbudowanych zasad [maszyn wirtualnych z systemem *Linux* lub *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) . Po zainstalowaniu agenta komputer można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.

    * W przypadku maszyn spoza platformy Azure można zainstalować agenta Log Analytics przy użyciu [serwerów z obsługą usługi Azure Arc](../azure-arc/servers/overview.md). Serwery z obsługą Arc obsługują wdrażanie agenta Log Analytics przy użyciu następujących metod:

        - Korzystanie z platformy rozszerzeń maszyn wirtualnych.

            Ta funkcja na serwerach z obsługą usługi Azure Arc umożliwia wdrożenie rozszerzenia maszyny wirtualnej Log Analytics Agent na serwerze z systemem innym niż Azure. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z obsługą ARC:

            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Interfejs wiersza polecenia platformy Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Szablony Menedżer zasobów](../azure-arc/servers/manage-vm-extensions-template.md) platformy Azure

        - Przy użyciu Azure Policy.

            Korzystając z tego podejścia, należy użyć wbudowanej zasady Azure Policy [Wdróż agenta log Analytics do systemu Linux lub Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) , aby przeprowadzić inspekcję, jeśli na serwerze z włączonym łukiem jest zainstalowany agent log Analytics. Jeśli Agent nie jest zainstalowany, zostanie automatycznie wdrożony przy użyciu zadania korygowania. Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../governance/policy/samples/built-in-initiatives.md#monitoring) , aby zainstalować i skonfigurować agenta log Analytics.

        Zalecamy zainstalowanie agenta Log Analytics dla systemu Windows lub Linux przy użyciu Azure Policy.

    > [!NOTE]
    > Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać maszyny jako węzły DSC.

    > [!NOTE]
    > [Konto nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) z odpowiednimi uprawnieniami sudo musi być obecne podczas instalacji hybrydowego procesu roboczego systemu Linux. Jeśli spróbujesz zainstalować proces roboczy, a konto nie jest obecne lub nie ma odpowiednich uprawnień, instalacja nie powiedzie się.

3. Sprawdź, czy Agent jest raportowany do obszaru roboczego.

    Agent Log Analytics dla systemu Linux łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na maszynie i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

    Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla maszyny, co oznacza, że jest ona podłączona i zgłaszana do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego.

4. Uruchom następujące polecenie, aby dodać maszynę do grupy hybrydowych procesów roboczych elementu Runbook, określając wartości parametrów `-w` ,, `-k` `-g` i `-e` .

    Możesz uzyskać informacje wymagane do parametrów `-k` i `-e` ze strony **klucze** na koncie usługi Automation. Wybierz pozycję **klucze** w sekcji **Ustawienia konta** w lewej części strony.

    ![Strona zarządzania kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Dla `-e` parametru skopiuj wartość **adresu URL**.

    * Dla `-k` parametru skopiuj wartość **podstawowy klucz dostępu**.

    * Dla `-g` parametru Określ nazwę grupy hybrydowych procesów roboczych elementu Runbook, która ma zostać przyłączona do nowego hybrydowego procesu roboczego elementu Runbook systemu Linux. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, zostanie utworzona przy użyciu tej nazwy.

    * Dla `-w` parametru Określ identyfikator obszaru roboczego log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Sprawdź wdrożenie po zakończeniu skryptu. Na stronie **grupy hybrydowych procesów roboczych elementu Runbook** na koncie usługi Automation na karcie **Grupa hybrydowe procesy robocze elementu Runbook użytkownika** zostanie wyświetlona nowa lub istniejąca Grupa oraz liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie, wybierając z menu z lewej strony pozycję **hybrydowe procesy robocze**. Na stronie **hybrydowe procesy robocze** można zobaczyć wszystkich członków tej grupy.

    > [!NOTE]
    > Jeśli używasz rozszerzenia maszyny wirtualnej Log Analytics dla systemu Linux dla maszyny wirtualnej platformy Azure, zalecamy ustawienie opcji `autoUpgradeMinorVersion` `false` Autouaktualnianie wersji może spowodować problemy z hybrydowym procesem roboczym elementu Runbook. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenie, zobacz [wdrażanie interfejsu wiersza polecenia platformy Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Wyłącz weryfikację sygnatur

Domyślnie hybrydowe procesy robocze elementu Runbook systemu Linux wymagają weryfikacji podpisu. W przypadku uruchomienia niepodpisanego elementu Runbook dla procesu roboczego zostanie wyświetlony `Signature validation failed` komunikat o błędzie. Aby wyłączyć weryfikację podpisu, uruchom następujące polecenie. Zastąp drugi parametr IDENTYFIKATORem obszaru roboczego Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Usuń hybrydowy proces roboczy elementu Runbook

`ls /var/opt/microsoft/omsagent`Aby uzyskać identyfikator obszaru roboczego, można użyć polecenia w hybrydowym procesie roboczym elementu Runbook. Zostanie utworzony folder o nazwie z IDENTYFIKATORem obszaru roboczego.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten skrypt nie usuwa z komputera agenta Log Analytics dla systemu Linux. Usuwa tylko funkcje i konfigurację roli hybrydowego procesu roboczego elementu Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook dla maszyn z systemem Linux, należy wykonać te same czynności co w przypadku grupy hybrydowych procesów roboczych systemu Windows. Zobacz [Usuwanie grupy hybrydowych procesów roboczych](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym elementu Runbook](troubleshoot/hybrid-runbook-worker.md#linux)