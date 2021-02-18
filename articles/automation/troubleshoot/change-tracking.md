---
title: Rozwiązywanie problemów z Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób rozwiązywania problemów z Azure Automation Change Tracking i funkcji spisu.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: 9fe53a343a9f6675519b60d37d077886adaf8a9d
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651168"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Rozwiązywanie problemów ze śledzeniem zmian i spisem

W tym artykule opisano sposób rozwiązywania problemów i rozwiązywania problemów z Azure Automation Change Tracking i spisu. Aby uzyskać ogólne informacje na temat Change Tracking i spisu, zobacz [omówienie Change Tracking i spisu](../change-tracking/overview.md).

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenariusz: maszyna jest już zarejestrowana na innym koncie

### <a name="issue"></a>Problem

Zostanie wyświetlony następujący komunikat o błędzie:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Przyczyna

Maszyna została już wdrożona w innym obszarze roboczym dla Change Tracking.

### <a name="resolution"></a>Rozwiązanie

1. Upewnij się, że komputer jest raportowany do prawidłowego obszaru roboczego. Aby uzyskać wskazówki dotyczące sposobu weryfikacji, zobacz [Weryfikowanie łączności agenta z Azure monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Upewnij się również, że ten obszar roboczy jest połączony z kontem Azure Automation. Aby potwierdzić, przejdź do konta usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

1. Upewnij się, że maszyny są widoczne w obszarze roboczym Log Analytics połączonym z kontem usługi Automation. Uruchom następujące zapytanie w obszarze roboczym Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Jeśli komputer nie jest widoczny w wynikach zapytania, nie został ostatnio zaewidencjonowany. Prawdopodobnie wystąpił problem z konfiguracją lokalną. Należy ponownie zainstalować agenta Log Analytics.

   Jeśli Twoja maszyna jest wymieniona w wynikach zapytania, sprawdź Właściwość Solutions, która **śledzenia zmian** jest wymieniona na liście. Spowoduje to sprawdzenie, czy jest on zarejestrowany w Change Tracking i spisie. Jeśli tak nie jest, sprawdź, czy występują problemy z konfiguracją zakresu. Konfiguracja zakresu określa, które maszyny są skonfigurowane do Change Tracking i spisu. Aby skonfigurować konfigurację zakresu dla maszyny docelowej, zobacz [włączanie Change Tracking i spisu na podstawie konta usługi Automation](../change-tracking/enable-from-automation-account.md).

   W obszarze roboczym Uruchom to zapytanie.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Jeśli otrzymasz ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` wynik, zostanie osiągnięty limit przydziału zdefiniowany w obszarze roboczym, w którym zarejestrowano dane. W obszarze roboczym przejdź do pozycji **użycie i szacowane koszty**. Wybierz nową **warstwę cenową** , która umożliwia użycie większej ilości danych, lub kliknij pozycję **dzienny limit** i Usuń limit.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Użycie i szacowane koszty." lightbox="./media/change-tracking/change-tracking-usage.png":::

Jeśli problem nadal nie zostanie rozwiązany, wykonaj kroki opisane w sekcji [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](../automation-windows-hrw-install.md) w celu ponownego zainstalowania hybrydowego procesu roboczego dla systemu Windows. W przypadku systemu Linux wykonaj kroki opisane w sekcji  [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Linux](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenariusz: nie są wyświetlane rekordy Change Tracking i spisu dla maszyn z systemem Windows

#### <a name="issue"></a>Problem

Nie widzisz żadnych Change Tracking i wyników spisu dla maszyn z systemem Windows, dla których włączono obsługę tej funkcji.

#### <a name="cause"></a>Przyczyna

Ten błąd może mieć następujące przyczyny:

* Agent Log Analytics platformy Azure dla systemu Windows nie jest uruchomiony.
* Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
* Nie pobrano pakietów administracyjnych Change Tracking i spisu.
* Włączona maszyna wirtualna może pochodzić z sklonowanego komputera, który nie został przygotowany przy użyciu przygotowania systemu (Sysprep) z zainstalowanym agentem Log Analytics dla systemu Windows.

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
> Agent Log Analytics domyślnie włącza śledzenie błędów. Aby włączyć pełne komunikaty o błędach tak jak w poprzednim przykładzie, użyj `VER` parametru. Aby uzyskać informacje na temat śladów, użyj `INF` po wywołaniu `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent Log Analytics dla systemu Windows nie działa

Sprawdź, czy na komputerze jest uruchomiony agent Log Analytics dla systemu Windows (**HealthService.exe**).

##### <a name="communication-to-automation-account-blocked"></a>Zablokowano komunikację z kontem usługi Automation

Sprawdź Podgląd zdarzeń na maszynie i Wyszukaj wszystkie zdarzenia, które mają `changetracking` w nich słowo.

Aby dowiedzieć się więcej o adresach i portach, które muszą być dozwolone do pracy Change Tracking i spisu, zobacz [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning).

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

Nie widzisz żadnych Change Tracking i wyników spisu dla maszyn z systemem Linux, dla których włączono obsługę tej funkcji. 

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

Jeśli komputer nie jest wyświetlany w wynikach zapytania, nie został ostatnio zaewidencjonowany. Prawdopodobnie wystąpił problem z konfiguracją lokalną i należy ponownie zainstalować agenta. Informacje o instalacji i konfiguracji znajdują się w temacie [zbieranie danych dziennika za pomocą agenta log Analytics](../../azure-monitor/agents/log-analytics-agent.md).

Jeśli komputer jest wyświetlany w wynikach zapytania, sprawdź konfigurację zakresu. Zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor](../../azure-monitor/insights/solution-targeting.md).

Aby uzyskać więcej informacji na temat tego problemu, zobacz [problem: nie widzisz żadnych danych systemu Linux](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics dla systemu Linux nie został poprawnie skonfigurowany

Agent Log Analytics dla systemu Linux może nie być prawidłowo skonfigurowany do zbierania danych wyjściowych dziennika i wiersza polecenia za pomocą narzędzia zbierającego dzienniki OMS. Zobacz [przegląd Change Tracking i spisu](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>Konflikty programu FIM

Funkcja FIM Azure Security Center może nieprawidłowo sprawdzać integralność plików systemu Linux. Sprawdź, czy kod FIM działa i poprawnie skonfigurowany do monitorowania plików systemu Linux. Zobacz [przegląd Change Tracking i spisu](../change-tracking/overview.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
