---
title: Rozwiązywanie problemów dotyczących Change Tracking i spisu
description: Informacje na temat rozwiązywania problemów z Azure Automation Change Tracking i rozwiązania spisu.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582149"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Rozwiązywanie problemów dotyczących Change Tracking i spisu

W tym artykule opisano sposób rozwiązywania problemów z Azure Automation Change Tracking i spisu.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji za pomocą [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenariusz: nie są wyświetlane rekordy Change Tracking i spisu dla maszyn z systemem Windows

#### <a name="issue"></a>Problem

Nie widzisz żadnych Change Tracking i wyników spisu dla maszyn z systemem Windows, które są dołączane.

#### <a name="cause"></a>Przyczyna

Ten błąd może mieć następujące przyczyny:

* Agent Log Analytics platformy Azure dla systemu Windows nie jest uruchomiony.
* Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
* Nie pobrano pakietów administracyjnych Change Tracking i spisu.
* Dołączanie maszyny wirtualnej może pochodzić ze sklonowanego komputera, który nie był Sysprep z zainstalowanym agentem Log Analytics dla systemu Windows.

#### <a name="resolution"></a>Rozwiązanie

Na maszynie agenta Log Analytics przejdź do **folderu C:\Program Files\Microsoft monitoring Agent\Agent\Tools** i uruchom następujące polecenia:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Jeśli nadal potrzebujesz pomocy, możesz zebrać informacje diagnostyczne i skontaktować się z pomocą techniczną.

> [!NOTE]
> Agent Log Analytics domyślnie włącza śledzenie błędów. Aby włączyć pełne komunikaty o błędach tak jak w poprzednim przykładzie, użyj `VER` parametru. Aby uzyskać informacje na temat `INF` śladów, `StartTracing.cmd`Użyj po wywołaniu.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent Log Analytics dla systemu Windows nie działa

Sprawdź, czy na maszynie jest uruchomiony agent Log Analytics dla systemu Windows (**HealthService. exe**).

##### <a name="communication-to-automation-account-blocked"></a>Zablokowano komunikację z kontem usługi Automation

Sprawdź Podgląd zdarzeń na maszynie i Wyszukaj wszystkie zdarzenia, które mają w nich słowo `changetracking` .

Aby dowiedzieć się więcej o adresach i portach, które muszą być dozwolone do pracy Change Tracking i spisu, zobacz [Automatyzowanie zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego elementu Runbook](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Nie pobrano pakietów administracyjnych

Sprawdź, czy następujące pakiety administracyjne Change Tracking i spisu są zainstalowane lokalnie:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Maszyna wirtualna ze sklonowanego komputera, która nie została Sysprep

W przypadku korzystania z sklonowanego obrazu najpierw należy zainstalować obraz programu Sysprep, a następnie Zainstaluj agenta Log Analytics dla systemu Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenariusz: brak Change Tracking i wyników spisu na maszynach z systemem Linux

#### <a name="issue"></a>Problem

Nie widzisz żadnych Change Tracking i wyników spisu dla maszyn z systemem Linux, które zostały dołączone do rozwiązania. 

#### <a name="cause"></a>Przyczyna
Poniżej przedstawiono możliwe przyczyny tego problemu:
* Agent Log Analytics dla systemu Linux nie jest uruchomiony.
* Agent Log Analytics dla systemu Linux nie jest prawidłowo skonfigurowany.
* Istnieją konflikty monitorowania integralności plików (FIM).

#### <a name="resolution"></a>Rozwiązanie 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics dla systemu Linux nie jest uruchomiony

Upewnij się, że demon dla Log Analytics Agent dla systemu Linux (**omsagent**) jest uruchomiony na komputerze. Uruchom następujące zapytanie w obszarze roboczym Log Analytics, który jest połączony z kontem usługi Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Jeśli komputer nie jest wyświetlany w wynikach zapytania, nie został ostatnio zaewidencjonowany. Prawdopodobnie wystąpił problem z konfiguracją lokalną i należy ponownie zainstalować agenta. Informacje o instalacji i konfiguracji znajdują się w temacie [zbieranie danych dziennika za pomocą agenta log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Jeśli komputer jest wyświetlany w wynikach zapytania, sprawdź konfigurację zakresu. Zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Aby uzyskać więcej informacji na temat tego problemu, zobacz [problem: nie widzisz żadnych danych systemu Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics dla systemu Linux nie został poprawnie skonfigurowany

Agent Log Analytics dla systemu Linux może nie być prawidłowo skonfigurowany do zbierania danych wyjściowych dziennika i wiersza polecenia za pomocą narzędzia zbierającego dzienniki OMS. Zobacz [śledzenie zmian w środowisku przy użyciu rozwiązania Change Tracking i spisu](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty programu FIM

Funkcja FIM Azure Security Center może nieprawidłowo sprawdzać integralność plików systemu Linux. Sprawdź, czy kod FIM działa i poprawnie skonfigurowany do monitorowania plików systemu Linux. Zobacz [śledzenie zmian w środowisku przy użyciu rozwiązania Change Tracking i spisu](../change-tracking.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
