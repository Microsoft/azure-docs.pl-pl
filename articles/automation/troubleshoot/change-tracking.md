---
title: Rozwiązywanie problemów z śledzeniem zmian i magazynem
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać problemy za pomocą rozwiązania Śledzenia zmian i zapasów usługi Azure Automation.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679345"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Rozwiązywanie problemów ze śledzeniem zmian i zapasami

W tym artykule opisano, jak rozwiązywać problemy ze śledzeniem zmian i zapasami.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenariusz: nie są wyświetlane rekordy śledzenia zmian i zapasów dla komputerów z systemem Windows

#### <a name="issue"></a>Problem

Nie widać żadnych wyników śledzenia zmian i zapasów dla komputerów z systemem Windows, które są wbudowane.

#### <a name="cause"></a>Przyczyna

Ten błąd może mieć następujące przyczyny:

* Agent analizy dzienników dla systemu Windows nie jest uruchomiony.
* Komunikacja z powrotem do konta automatyzacji jest blokowana.
* Pakiety zarządzania śledzeniem zmian i zapasami nie są pobierane.
* Maszyna wirtualna jest wbudowany może pochodzić z sklonowanego komputera, który nie był sysprepped z agentem usługi Log Analytics dla systemu Windows zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Na komputerze agenta usługi Log Analytics przejdź do **pozycji C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** i uruchom następujące polecenia:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Jeśli nadal potrzebujesz pomocy, możesz zbierać informacje diagnostyczne i kontaktować się z pomocą techniczną. 

> [!NOTE]
> Agent analizy dzienników domyślnie umożliwia śledzenie błędów. Aby włączyć pełne komunikaty o błędach, jak `VER` w poprzednim przykładzie, należy użyć parametru. Aby uzyskać informacje `INF` śledzenia, `StartTracing.cmd`należy użyć podczas wywoływania .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent analizy dzienników dla systemu Windows nie jest uruchomiony

Sprawdź, czy agent usługi Log Analytics dla systemu Windows **(HealthService.exe)** jest uruchomiony na komputerze.

##### <a name="communication-to-automation-account-blocked"></a>Zablokowano komunikację z kontem automatyzacji

Sprawdź Podgląd zdarzeń na komputerze i poszukaj zdarzeń, które mają w sobie słowo. `changetracking`

Zobacz [Automatyzacja zasobów w centrum danych lub w chmurze za pomocą hybrydowego środowiska roboczego żyła,](../automation-hybrid-runbook-worker.md#network-planning) aby dowiedzieć się więcej o adresach i portach, które muszą być dozwolone do śledzenia zmian i zapasów do pracy.

##### <a name="management-packs-not-downloaded"></a>Nie pobrano pakietów administracyjnych

Sprawdź, czy następujące pakiety zarządzania śledzeniem zmian i zapasami są zainstalowane lokalnie:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Maszyna wirtualna ze sklonowanej maszyny, która nie została sysprepped

Jeśli używasz sklonowanego obrazu, najpierw przesuń obraz, a następnie zainstaluj agenta usługi Log Analytics dla systemu Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenariusz: Brak śledzenia zmian i wyniki zapasów na komputerach z systemem Linux

#### <a name="issue"></a>Problem

Nie widać żadnych wyników śledzenia zapasów i zmian dla komputerów z systemem Linux, które są wbudowane dla rozwiązania. 

#### <a name="cause"></a>Przyczyna
Oto możliwe przyczyny specyficzne dla tego problemu:
* Agent analizy dzienników dla systemu Linux nie jest uruchomiony.
* Agent usługi Log Analytics dla systemu Linux nie jest poprawnie skonfigurowany.
* Występują konflikty monitorowania integralności plików (FIM).

#### <a name="resolution"></a>Rozwiązanie 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent analizy dzienników dla systemu Linux nie jest uruchomiony

Sprawdź, czy demon dla agenta usługi Log Analytics dla systemu Linux **(omsagent)** jest uruchomiony na komputerze. Uruchom następującą kwerendę w obszarze roboczym usługi Log Analytics, która jest połączona z kontem automatyzacji.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Jeśli komputer nie jest widoczny w wynikach kwerendy, nie został ostatnio zaewidencjonowany. Prawdopodobnie występuje problem z konfiguracją lokalną i należy ponownie zainstalować agenta. Aby uzyskać informacje o instalacji i konfiguracji, zobacz [Zbieranie danych dziennika za pomocą agenta usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Jeśli komputer pojawia się w wynikach kwerendy, sprawdź konfigurację zakresu. Zobacz [rozwiązania do monitorowania kierowania w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)

Aby uzyskać więcej problemów z tym problemem, zobacz [Problem: Nie widzisz żadnych danych systemu Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent usługi Log Analytics dla systemu Linux nie został poprawnie skonfigurowany

Agent usługi Log Analytics dla systemu Linux może nie być poprawnie skonfigurowany dla kolekcji danych wyjściowych dziennika i wiersza polecenia przy użyciu narzędzia Moduł zbierający dzienniki OMS. Zobacz [Śledzenie zmian w środowisku za pomocą rozwiązania Śledzenia zmian i zapasów](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkcja FIM usługi Azure Security Center może niepoprawnie sprawdzać integralność plików systemu Linux. Sprawdź, czy FIM działa i poprawnie skonfigurowany do monitorowania plików systemu Linux. Zobacz [Śledzenie zmian w środowisku za pomocą rozwiązania Śledzenia zmian i zapasów](../change-tracking.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz powyższego problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
