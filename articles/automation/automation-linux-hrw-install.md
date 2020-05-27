---
title: Wdrażanie hybrydowego procesu roboczego elementu Runbook w systemie Linux w Azure Automation
description: W tym artykule opisano sposób instalowania Azure Automation hybrydowego procesu roboczego elementu Runbook do uruchamiania elementów Runbook na komputerach z systemem Linux w lokalnym środowisku centrum danych lub w chmurze.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: a6cf348142d694a03da24f32793fc72325701931
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835227"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Hybrydowy proces roboczy elementu Runbook systemu Linux wykonuje elementy Runbook jako specjalny użytkownik, który może mieć podwyższony poziom uprawnień do uruchamiania poleceń wymagających podniesienia uprawnień. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego wyznaczenia komputerów.

W tym artykule opisano sposób instalowania hybrydowego procesu roboczego elementu Runbook na komputerze z systemem Linux, sposobu usuwania procesu roboczego oraz usuwania grupy hybrydowych procesów roboczych elementu Runbook.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja hybrydowego procesu roboczego elementu Runbook obsługuje następujące dystrybucje:

* Amazon Linux 2012,09 do 2015,09 (x86/x64)
* CentOS Linux 5, 6 i 7 (x86/x64)
* Oracle Linux 5, 6 i 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 i 7 (x86/x64)
* Debian GNU/Linux 6, 7 i 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS i 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 i 12 (x86/x64)

## <a name="supported-runbook-types"></a>Obsługiwane typy elementów Runbook

Hybrydowe procesy robocze elementu Runbook systemu Linux nie obsługują pełnego zestawu typów elementów Runbook w Azure Automation.

Następujące typy elementów Runbook działają w hybrydowym procesie roboczym systemu Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Elementy Runbook programu PowerShell wymagają zainstalowania programu PowerShell Core na komputerze z systemem Linux. Zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować.

Następujące typy elementów Runbook nie działają w hybrydowym procesie roboczym Linux:

* Przepływ pracy programu PowerShell
* Element graficzny
* Graficzny przepływ pracy programu PowerShell

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

Minimalne wymagania dla hybrydowego procesu roboczego elementu Runbook systemu Linux to:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (ruch wychodzący)

### <a name="package-requirements"></a>Wymagania dotyczące pakietu

| **Wymagany pakiet** | **Opis** | **Wersja minimalna**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka GNU C| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1,0 (obsługiwane są protokoły TLS 1,1 i TLS 1,2)|
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5|
|Python — ctypes | Wymagany jest język Python 2. x |
|PAM | Podłączane moduły uwierzytelniania (PAM)|
| **Opcjonalny pakiet** | **Opis** | **Wersja minimalna**|
| Program PowerShell Core | Aby uruchomić elementy Runbook programu PowerShell, należy zainstalować program PowerShell, zobacz temat [Instalowanie programu PowerShell Core w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , aby dowiedzieć się, jak go zainstalować.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego elementu Runbook systemu Linux

Aby zainstalować i skonfigurować hybrydowy proces roboczy elementu Runbook na komputerze z systemem Linux, wykonaj czynności ręczne. Wymaga włączenia hybrydowego procesu roboczego elementu Runbook w obszarze roboczym usługi Azure Log Analytics, a następnie uruchomienia zestawu poleceń, aby zarejestrować komputer jako proces roboczy i dodać go do grupy.

Przed kontynuowaniem Zanotuj Log Analytics obszar roboczy, z którym jest połączone konto usługi Automation. Należy również zwrócić uwagę na klucz podstawowy konta usługi Automation. Oba te elementy można znaleźć na stronie Azure Portal, wybierając swoje konto usługi Automation, wybierając pozycję **obszar roboczy** dla identyfikatora obszaru roboczego, a następnie wybierając **klucze** klucza podstawowego. Aby uzyskać informacje na temat portów i adresów potrzebnych dla hybrydowego procesu roboczego elementu Runbook, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> [Konto nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) z odpowiednimi uprawnieniami sudo musi być obecne podczas instalacji hybrydowego procesu roboczego systemu Linux. Jeśli spróbujesz zainstalować proces roboczy, a konto nie jest obecne lub nie ma odpowiednich uprawnień, instalacja nie powiedzie się.

1. Włącz hybrydowy proces roboczy elementu Runbook na platformie Azure za pomocą jednej z następujących metod:

   * Dodaj hybrydowy proces roboczy elementu Runbook do subskrypcji przy użyciu procedury w [obszarze dodawanie Azure monitor dzienników do obszaru roboczego](../log-analytics/log-analytics-add-solutions.md).
   * Uruchom następujące polecenie cmdlet:

        ```azurepowershell-interactive
         Set-AzOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Zainstaluj agenta Log Analytics dla systemu Linux, uruchamiając następujące polecenie. Zastąp \< \> wartości identyfikator obszaru roboczego i \< WorkspaceKey \> odpowiednimi wartościami z obszaru roboczego.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Uruchom następujące polecenie, zmieniając wartości parametrów *-w*, *-k*, *-g*i *-e*. Dla parametru *-g* Zastąp wartość nazwą grupy hybrydowych procesów roboczych elementu Runbook, do której ma zostać dołączony nowy hybrydowy proces roboczy elementu Runbook systemu Linux. Jeśli nazwa nie istnieje na koncie usługi Automation, Nowa grupa hybrydowych procesów roboczych elementu Runbook zostanie nadana przy użyciu tej nazwy.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po zakończeniu działania polecenia na stronie grupy hybrydowych procesów roboczych w Azure Portal zostanie wyświetlona nowa grupa i liczba członków. Jeśli jest to istniejąca Grupa, liczba członków jest zwiększana. Możesz wybrać grupę z listy na stronie grupy hybrydowych procesów roboczych i wybrać kafelek **hybrydowe procesy** robocze. Na stronie hybrydowe procesy robocze zobaczysz każdego członka grupy na liście.

> [!NOTE]
> Jeśli używasz rozszerzenia maszyny wirtualnej Azure Monitor dla systemu Linux dla maszyny wirtualnej platformy Azure, zalecamy ustawienie `autoUpgradeMinorVersion` wartości false, ponieważ funkcja autouaktualniania wersji może powodować problemy z hybrydowym procesem roboczym elementu Runbook. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenie, zobacz [wdrażanie interfejsu wiersza polecenia platformy Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Wyłącz weryfikację sygnatur

Domyślnie hybrydowe procesy robocze elementu Runbook systemu Linux wymagają weryfikacji podpisu. W przypadku uruchomienia niepodpisanego elementu Runbook dla procesu roboczego zostanie wyświetlony `Signature validation failed` komunikat o błędzie. Aby wyłączyć weryfikację podpisu, uruchom następujące polecenie. Zastąp drugi parametr IDENTYFIKATORem obszaru roboczego Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Usuwanie hybrydowego procesu roboczego elementu Runbook z lokalnego komputera z systemem Linux

`ls /var/opt/microsoft/omsagent`Aby uzyskać identyfikator obszaru roboczego, można użyć polecenia w hybrydowym procesie roboczym elementu Runbook. Zostanie utworzony folder o nazwie z IDENTYFIKATORem obszaru roboczego.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ten kod nie usuwa z komputera agenta Log Analytics dla systemu Linux. Usuwa tylko funkcje i konfigurację roli hybrydowego procesu roboczego elementu Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Usuwanie grupy hybrydowego procesu roboczego)

Aby usunąć grupę hybrydowych procesów roboczych elementu Runbook z komputerów z systemem Linux, należy wykonać te same czynności co w przypadku grupy hybrydowych procesów roboczych systemu Windows. Zobacz [Usuwanie grupy hybrydowych procesów roboczych](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym elementu Runbook](troubleshoot/hybrid-runbook-worker.md#linux)
