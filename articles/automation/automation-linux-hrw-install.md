---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook w systemie Linux w Azure Automation
description: W tym artykule opisano sposób instalowania Azure Automation hybrydowego procesu roboczego elementu Runbook w celu uruchamiania elementów Runbook na maszynach z systemem Linux w lokalnym środowisku centrum danych lub w chmurze.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 8295b6bba9703c276bf60a0360ded6f0e195369e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776276"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Hybrydowy proces roboczy elementu Runbook systemu Linux wykonuje elementy Runbook jako specjalny użytkownik, który może mieć podwyższony poziom uprawnień do uruchamiania poleceń wymagających podniesienia uprawnień. Azure Automation przechowuje elementy Runbook i zarządza nimi, a następnie dostarcza je do co najmniej jednej wyznaczeniu maszyn. W tym artykule opisano sposób instalowania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Linux, sposobu usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych elementu Runbook.

Po pomyślnym wdrożeniu programu Runbook Worker przejrzyj temat [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md) , aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

### <a name="a-log-analytics-workspace"></a>Obszar roboczy Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook zależy od obszaru roboczego Log Analytics Azure Monitor, aby zainstalować i skonfigurować rolę. Można go utworzyć za pomocą [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), za pomocą [programu PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)lub [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Jeśli nie masz obszaru roboczego Log Analytics Azure Monitor, zapoznaj się ze [wskazówkami dotyczącymi projektu dziennika Azure monitor](../azure-monitor/platform/design-logs-deployment.md) przed utworzeniem obszaru roboczego.

Jeśli masz obszar roboczy, ale nie jest on połączony z kontem usługi Automation, włączenie funkcji automatyzacji powoduje dodanie funkcji dla Azure Automation, w tym obsługę hybrydowego procesu roboczego elementu Runbook. Po włączeniu jednej z Azure Automation funkcji w obszarze roboczym Log Analytics, w odróżnieniu od [Update Management](update-management/update-mgmt-overview.md) lub [Change Tracking i spisu](change-tracking.md), składniki procesu roboczego są automatycznie wypychane do komputera agenta.

Aby dodać funkcję Update Management do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

Aby dodać funkcję Change Tracking i spisu do obszaru roboczego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agent usługi Log Analytics

Rola hybrydowego procesu roboczego elementu Runbook wymaga [agenta log Analytics](../azure-monitor/platform/log-analytics-agent.md) dla obsługiwanego systemu operacyjnego Linux.

>[!NOTE]
>Po zainstalowaniu agenta Log Analytics dla systemu Linux nie należy zmieniać uprawnień do `sudoers.d` folderu ani jego własności. Dla konta **nxautomation** jest wymagane uprawnienie sudo, które jest kontekstem użytkownika, w którym działa hybrydowy proces roboczy elementu Runbook. Uprawnień nie należy usuwać. Ograniczenie tego do określonych folderów lub poleceń może spowodować powstanie istotnej zmiany.
>

### <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące dystrybucje:

* Amazon Linux 2012,09 do 2015,09 (x86/x64)
* CentOS Linux 5, 6 i 7 (x86/x64)
* Oracle Linux 5, 6 i 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 i 7 (x86/x64)
* Debian GNU/Linux 6, 7 i 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS i 18,04 (x86/x64)
* SUSE Linux Enterprise Server 12 (x86/x64)

### <a name="minimum-requirements"></a>Minimalne wymagania

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Linux to:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

| **Wymagany pakiet** | **Opis** | **Wersja minimalna**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka GNU C| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1,0 (obsługiwane są protokoły TLS 1,1 i TLS 1,2)|
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5|
|Python — ctypes | Wymagany jest język Python 2. x |
|PAM | Podłączane moduły uwierzytelniania (PAM)|
| **Opcjonalny pakiet** | **Opis** | **Wersja minimalna**|
| Program PowerShell Core | Aby można było uruchomić elementy Runbook programu PowerShell, należy zainstalować program PowerShell Core. Zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować. | 6.0.0 |

## <a name="supported-linux-hardening"></a>Obsługiwane Ograniczanie poziomu systemu Linux

Następujące elementy nie są jeszcze obsługiwane:

* SIC

## <a name="supported-runbook-types"></a>Obsługiwane typy elementów Runbook

Hybrydowe procesy robocze elementu Runbook systemu Linux obsługują ograniczoną liczbę typów elementów Runbook w Azure Automation i są opisane w poniższej tabeli.

|Typ elementu Runbook | Obsługiwane |
|-------------|-----------|
|Python 2 |Tak |
|Program PowerShell |Tak<sup>1</sup> |
|Przepływ pracy programu PowerShell |Nie |
|Element graficzny |Nie |
|Graficzny przepływ pracy programu PowerShell |Nie |

<sup>1</sup> Elementy Runbook programu PowerShell wymagają zainstalowania programu PowerShell Core na komputerze z systemem Linux. Zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook systemu Linux, wykonaj następujące czynności.

1. Wdróż agenta Log Analytics na maszynie docelowej.

    * W przypadku maszyn wirtualnych platformy Azure Zainstaluj agenta Log Analytics dla systemu Linux przy użyciu [rozszerzenia maszyny wirtualnej dla systemu Linux](../virtual-machines/extensions/oms-linux.md). Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym Log Analytics przy użyciu szablonu Azure Resource Manager lub interfejsu wiersza polecenia platformy Azure. Po zainstalowaniu agenta można dodać maszynę wirtualną do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.

    * W przypadku maszyn wirtualnych z systemem innym niż Azure Zainstaluj agenta Log Analytics dla systemu Linux, korzystając z opcji wdrażania opisanych w artykule [łączenie komputerów z systemem Linux do Azure monitor](../azure-monitor/platform/agent-linux.md) artykułu. Można powtórzyć ten proces dla wielu maszyn, aby dodać wielu procesów roboczych do środowiska. Po zainstalowaniu agenta maszyny wirtualne można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation.

    > [!NOTE]
    > Aby zarządzać konfiguracją maszyn, które obsługują rolę hybrydowego procesu roboczego elementu Runbook z konfiguracją żądanego stanu (DSC), należy dodać maszyny jako węzły DSC.

    > [!NOTE]
    > [Konto nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) z odpowiednimi uprawnieniami sudo musi być obecne podczas instalacji hybrydowego procesu roboczego systemu Linux. Jeśli spróbujesz zainstalować proces roboczy, a konto nie jest obecne lub nie ma odpowiednich uprawnień, instalacja nie powiedzie się.

2. Sprawdź, czy Agent jest raportowany do obszaru roboczego.

    Agent Log Analytics dla systemu Linux łączy komputery z obszarem roboczym Azure Monitor Log Analytics. Po zainstalowaniu agenta na maszynie i nawiązaniu połączenia z obszarem roboczym program automatycznie pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

    Gdy Agent pomyślnie połączył się z obszarem roboczym Log Analytics za kilka minut, możesz uruchomić następujące zapytanie, aby sprawdzić, czy wysyła dane pulsu do obszaru roboczego.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    W wynikach wyszukiwania powinny być widoczne rekordy pulsu dla maszyny, co oznacza, że jest ona podłączona i zgłaszana do usługi. Domyślnie każdy agent przekazuje rekord pulsu do przypisanego im obszaru roboczego.

3. Uruchom następujące polecenie, aby dodać maszynę do grupy hybrydowych procesów roboczych elementu Runbook, określając wartości parametrów `-w` ,, `-k` `-g` i `-e` .

    Możesz uzyskać informacje wymagane do parametrów `-k` i `-e` ze strony **klucze** na koncie usługi Automation. Wybierz pozycję **klucze** w sekcji **Ustawienia konta** w lewej części strony.

    ![Strona zarządzania kluczami](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Dla `-e` parametru skopiuj wartość **adresu URL**.

    * Dla `-k` parametru skopiuj wartość **podstawowy klucz dostępu**.

    * Dla `-g` parametru Określ nazwę grupy hybrydowych procesów roboczych elementu Runbook, która ma zostać przyłączona do nowego hybrydowego procesu roboczego elementu Runbook systemu Linux. Jeśli ta grupa już istnieje na koncie usługi Automation, do niej zostanie dodany bieżący komputer. Jeśli ta grupa nie istnieje, zostanie utworzona przy użyciu tej nazwy.

    * Dla `-w` parametru Określ identyfikator obszaru roboczego log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. Po zakończeniu działania polecenia na stronie grupy hybrydowych procesów roboczych na koncie usługi Automation zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie grupy hybrydowych procesów roboczych i wybrać kafelek **hybrydowe procesy** robocze. Na stronie hybrydowe procesy robocze zobaczysz każdego członka grupy na liście.

    > [!NOTE]
    > Jeśli używasz rozszerzenia maszyny wirtualnej Log Analytics dla systemu Linux dla maszyny wirtualnej platformy Azure, zalecamy ustawienie opcji `autoUpgradeMinorVersion` `false` Autouaktualnianie wersji może spowodować problemy z hybrydowym procesem roboczym elementu Runbook. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenie, zobacz [wdrażanie interfejsu wiersza polecenia platformy Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Wyłącz weryfikację sygnatur

Domyślnie hybrydowe procesy robocze elementu Runbook systemu Linux wymagają weryfikacji podpisu. W przypadku uruchomienia niepodpisanego elementu Runbook dla procesu roboczego zostanie wyświetlony `Signature validation failed` komunikat o błędzie. Aby wyłączyć weryfikację podpisu, uruchom następujące polecenie. Zastąp drugi parametr IDENTYFIKATORem obszaru roboczego Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnej maszyny z systemem Linux

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
