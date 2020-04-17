---
title: Hybrydowy proces roboczy elementu runbook usługi Azure Automation (Linux)
description: Ten artykuł zawiera informacje dotyczące instalowania procesu roboczego hybrydowego systemu Azure Automation, dzięki czemu można uruchamiać elementy runbook na komputerach z systemem Linux w lokalnym centrum danych lub środowisku chmury.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: f2584a8d4e68b7c16b3acdc29f64f0a19d83d735
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457675"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Wdrażanie procesu roboczego hybrydowego systemu Linux

Za pomocą funkcji Hybrydowy proces roboczy elementu runbook usługi Azure Automation można uruchamiać elementy runbook bezpośrednio na komputerze obsługującym rolę i przeciwko zasobom w środowisku do zarządzania tymi zasobami lokalnymi. Proces roboczy hybrydowego systemu Linux wykonuje runbooks jako specjalnego użytkownika, który może być podwyższony dla uruchamiania poleceń, które wymagają podniesienia uprawnień. Elementy runbook są przechowywane i zarządzane w usłudze Azure Automation, a następnie dostarczane do jednego lub więcej wyznaczonych komputerów.

W tym artykule opisano sposób instalowania hybrydowego procesu roboczego egoisty na komputerze z systemem Linux.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Funkcja Hybrydowy proces roboczy elementu runbook obsługuje następujące dystrybucje:

* Amazon Linux 2012.09 do 2015.09 (x86/x64)
* CentOS Linux 5, 6 i 7 (x86/x64)
* Oracle Linux 5, 6 i 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 i 7 (x86/x64)
* Debian GNU/Linux 6, 7 i 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS i 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 i 12 (x86/x64)

## <a name="supported-runbook-types"></a>Obsługiwane typy elementów runbook

Hybrydowe procesy owe elementów workers systemu Linux nie obsługują pełnego zestawu typów elementów runbook w usłudze Azure Automation.

Następujące typy elementów runbook działają na proces roboczy hybrydowy systemu Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Podręczniki programu PowerShell wymagają zainstalowania programu PowerShell Core na komputerze z systemem Linux. Zobacz [Instalowanie programu PowerShell Core w systemie Linux,](/powershell/scripting/install/installing-powershell-core-on-linux) aby dowiedzieć się, jak go zainstalować.

Następujące typy elementów runbook nie działają na pracownika hybrydowego systemu Linux:

* Przepływ pracy programu PowerShell
* Element graficzny
* Graficzny przepływ pracy programu PowerShell

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalowanie hybrydowego procesu roboczego systemu Linux

Aby zainstalować i skonfigurować hybrydowy proces roboczy systemu runbook na komputerze z systemem Linux, wykonaj prosty proces ręczny. Wymaga włączenia rozwiązania Automation Hybrid Worker w obszarze roboczym usługi Azure Log Analytics, a następnie uruchomienie zestawu poleceń, aby zarejestrować komputer jako proces roboczy i dodać go do grupy.

Minimalne wymagania dla procesu roboczego hybrydowego systemu Linux są następujące:

* Dwa rdzenie
* 4 GB pamięci RAM
* Port 443 (wychodzący)

### <a name="package-requirements"></a>Wymagania dotyczące pakietu

| **Wymagany pakiet** | **Opis** | **Wersja minimalna**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteka GNU C| 2.5-12 |
|Openssl| Biblioteki OpenSSL | 1.0 (obsługiwane są TLS 1.1 i TLS 1.2|
|Narzędzie Curl | Klient sieci web cURL | 7.15.5|
|Python-ctypes | Python 2.x jest wymagany |
|PAM | Podłączane moduły uwierzytelniania (PAM)|
| **Pakiet opcjonalny** | **Opis** | **Wersja minimalna**|
| Program PowerShell Core | Aby uruchomić programy PowerShell, program PowerShell musi zostać zainstalowany, zobacz [Instalowanie programu PowerShell Core w systemie Linux,](/powershell/scripting/install/installing-powershell-core-on-linux) aby dowiedzieć się, jak go zainstalować.  | 6.0.0 |

### <a name="installation"></a>Instalacja

Przed kontynuowaniem należy zwrócić uwagę na obszar roboczy usługi Log Analytics, z którego jest połączone konto automatyzacji. Zanotuj również klucz podstawowy konta automatyzacji. Można znaleźć zarówno w witrynie Azure portal, wybierając konto automatyzacji, wybierając **obszar roboczy** dla identyfikatora obszaru roboczego i wybierając **klawisze** dla klucza podstawowego. Aby uzyskać informacje o portach i adresach potrzebnych dla hybrydowego procesu roboczego niu gospołowego, zobacz [Konfigurowanie sieci](automation-hybrid-runbook-worker.md#network-planning).

1. Włącz rozwiązanie automation hybrid worker na platformie Azure przy użyciu jednej z następujących metod:

   * Dodaj rozwiązanie Automation Hybrid Worker do subskrypcji przy użyciu procedury [w obszarze roboczym Dodawanie rozwiązań dziennika usługi Azure Monitor.](../log-analytics/log-analytics-add-solutions.md)
   * Uruchom następujące polecenie cmdlet:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Zainstaluj agenta usługi Log Analytics dla systemu Linux, uruchamiając następujące polecenie. \<Zamień\> identyfikator \<obszaru\> roboczego i klawisz WorkspaceKey na odpowiednie wartości z obszaru roboczego.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Uruchom następujące polecenie, zmieniając wartości parametrów *-w*, *-k*, *-g*i *-e*. Dla parametru *-g* zastąp wartość nazwą grupy hybrydowego procesu roboczego żylik, do którą powinien dołączyć nowy hybrydowy proces roboczy systemu Linux. Jeśli nazwa nie istnieje na koncie automatyzacji, nowa grupa hybrydowego systemu runbook roboczego jest o tej nazwie.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po zakończeniu polecenia hybrydowe grupy robocze strony w witrynie Azure Portal pokazuje nową grupę i liczbę członków. Jeśli jest to istniejąca grupa, liczba elementów członkowskich jest zwiększana. Grupę można wybrać z listy na stronie Hybrydowe grupy robocze i wybrać kafelek **Hybrydowych pracowników.** Na stronie Hybrydowe pracowników strony, zobaczysz każdy członek grupy na liście.

> [!NOTE]
> Jeśli używasz rozszerzenia maszyny wirtualnej usługi Azure Monitor dla `autoUpgradeMinorVersion` systemu Linux dla maszyny wirtualnej platformy Azure, zalecamy ustawienie false, ponieważ wersje automatycznego uaktualniania mogą powodować problemy z hybrydowym pracownikiem życiorysu. Aby dowiedzieć się, jak ręcznie uaktualnić rozszerzenie, zobacz [Wdrażanie interfejsu wiersza polecenia platformy Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Wyłączanie sprawdzania poprawności podpisu

Domyślnie hybrydowe środowiska członkowskie systemu Linux wymagają weryfikacji podpisu. Po uruchomieniu niepodpisanego śmiętu `Signature validation failed` dla pracownika zostanie wyświetlony błąd. Aby wyłączyć sprawdzanie poprawności podpisu, uruchom następujące polecenie. Zastąp drugi parametr identyfikatorem obszaru roboczego usługi Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować programy runbook do automatyzacji procesów w lokalnym centrum danych lub innym środowisku chmury, zobacz [Runbooks w hybrydowym usłudze Runbook Worker](automation-hrw-run-runbooks.md).
* Aby uzyskać instrukcje dotyczące usuwania hybrydowych workers ceł funkcjonowania, zobacz [Usuwanie hybrydowych workers ceł funkcjonowania usługi Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi pracownikami funkcjonowania, zobacz [Rozwiązywanie problemów z hybrydowymi robotami ujedniającymi systemu Linux](troubleshoot/hybrid-runbook-worker.md#linux)
