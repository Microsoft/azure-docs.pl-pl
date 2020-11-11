---
title: Monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure Monitor
description: Opisuje sposób zbierania i analizowania danych monitorowania z maszyn wirtualnych na platformie Azure przy użyciu Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 84db7f58c292cf0a9d01cf90da4b847691f601fb
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491634"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorowanie maszyn wirtualnych platformy Azure za pomocą Azure Monitor
W tym artykule opisano, jak za pomocą Azure Monitor zbierać i analizować dane monitorowania z maszyn wirtualnych platformy Azure w celu utrzymania ich kondycji. Maszyny wirtualne mogą być monitorowane pod kątem dostępności i wydajności przy użyciu Azure Monitor jak dowolnego [innego zasobu platformy Azure](monitor-azure-resource.md), ale są unikatowe z innych zasobów, ponieważ konieczne jest również monitorowanie systemu operacyjnego i systemów gościa oraz obciążeń, które są w nim uruchomione. 

> [!NOTE]
> Ten artykuł zawiera kompletny przegląd pojęć i opcji monitorowania maszyn wirtualnych w programie Azure Monitor. Aby szybko monitorować maszyny wirtualne bez skoncentrowania się na podstawowych pojęciach, zobacz [Szybki Start: monitorowanie maszyny wirtualnej platformy Azure przy użyciu Azure monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Różnice między innymi zasobami platformy Azure
[Monitorowanie zasobów platformy Azure za pomocą Azure monitor](monitor-azure-resource.md) zawiera opis danych monitorowania wygenerowanych przez zasoby platformy Azure oraz sposób korzystania z funkcji Azure monitor do analizowania i generowania alertów dotyczących tych danych. Można zbierać te same dane monitorowania z maszyn wirtualnych platformy Azure i wykonywać na nich działania z następującymi różnicami:

-  [Metryki platformy](../platform/data-platform-metrics.md) są zbierane automatycznie dla maszyn wirtualnych, ale tylko dla [hosta maszyny wirtualnej](#monitoring-data). Potrzebujesz agenta, aby zbierać dane dotyczące wydajności z systemu operacyjnego gościa. 
- Maszyny wirtualne nie generują [dzienników zasobów](../platform/platform-logs-overview.md) , które zapewniają wgląd w operacje wykonywane w ramach zasobu platformy Azure. Do zbierania danych dziennika z systemu operacyjnego gościa służy Agent.
- Można utworzyć [Ustawienia diagnostyczne](../platform/diagnostic-settings.md) dla maszyny wirtualnej w celu wysyłania metryk platformy do innych miejsc docelowych, takich jak magazyn i centra zdarzeń, ale nie można skonfigurować tych ustawień diagnostycznych w Azure Portal. 

## <a name="monitoring-data"></a>Dane monitorowania
Maszyny wirtualne na platformie Azure generują [dzienniki](../platform/data-platform-logs.md) i [metryki](../platform/data-platform-metrics.md) , jak pokazano na poniższym diagramie.

![Omówienie](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host maszyny wirtualnej
Maszyny wirtualne na platformie Azure generują następujące dane dla hosta maszyny wirtualnej tak samo jak inne zasoby platformy Azure, zgodnie z opisem w temacie [dane monitorowania](monitor-azure-resource.md#monitoring-data).

- [Metryki platformy](../platform/data-platform-metrics.md) — wartości liczbowe, które są automatycznie zbierane w regularnych odstępach czasu i opisują niektóre aspekty zasobu w określonym czasie. Metryki platformy są zbierane dla hosta maszyny wirtualnej, ale wymagane jest rozszerzenie diagnostyki do zbierania metryk dla systemu operacyjnego gościa.
- [Dziennik aktywności](../platform/platform-logs-overview.md) — umożliwia wgląd w operacje dotyczące poszczególnych zasobów platformy Azure w ramach subskrypcji z zewnątrz (płaszczyzny zarządzania). W przypadku maszyny wirtualnej obejmuje to takie informacje, jak podczas jego uruchamiania oraz zmiany w konfiguracji.


### <a name="guest-operating-system"></a>System operacyjny gościa
Aby zbierać dane z systemu operacyjnego gościa maszyny wirtualnej, musisz mieć agenta, który działa lokalnie na poszczególnych maszynach wirtualnych i wysyła dane do Azure Monitor. Wielu agentów jest dostępnych dla Azure Monitor, z których każdy zbiera różne dane i zapisuje dane w różnych lokalizacjach. Zapoznaj się ze szczegółowym porównaniem różnych agentów na stronie [Omówienie agentów Azure monitor](../platform/agents-overview.md). 

- [Log Analytics](../platform/agents-overview.md#log-analytics-agent) dostępne dla maszyn wirtualnych na platformie Azure, w innych środowiskach w chmurze i lokalnie. Zbiera dane do dzienników Azure Monitor. Obsługuje rozwiązania Azure Monitor dla maszyn wirtualnych i monitorowania. Jest to ten sam Agent, który służy do System Center Operations Manager.
- [Agent zależności](../platform/agents-overview.md#dependency-agent) — zbiera dane dotyczące procesów uruchomionych na maszynie wirtualnej i ich zależności. Opiera się na agencie Log Analytics do przesyłania danych do platformy Azure i obsługuje rozwiązania Azure Monitor dla maszyn wirtualnych, Service Map i Wire Data 2.0.
- [Rozszerzenie diagnostyki platformy Azure](../platform/agents-overview.md#azure-diagnostics-extension) — dostępne tylko dla Azure monitor maszyn wirtualnych. Może zbierać dane do wielu lokalizacji, ale przede wszystkim są używane do zbierania danych o wydajności gościa w Azure Monitor metryki dla maszyn wirtualnych z systemem Windows.
- [Agent telegraf](../platform/collect-custom-metrics-linux-telegraf.md) — zbiera dane o wydajności z maszyn wirtualnych systemu Linux do metryk Azure monitor.


## <a name="configuration-requirements"></a>Wymagania dotyczące konfiguracji
Aby włączyć wszystkie funkcje Azure Monitor na potrzeby monitorowania maszyny wirtualnej, należy zebrać dane monitorowania z hosta maszyny wirtualnej i systemu operacyjnego gościa do obu [Azure monitor metryk](../platform/data-platform-logs.md) i [dzienników Azure monitor](../platform/data-platform-logs.md). Poniższa tabela zawiera listę konfiguracji, które należy wykonać, aby włączyć tę kolekcję. W zależności od konkretnych wymagań można wykonać wszystkie te czynności.

| Krok konfiguracji | Wykonane akcje | Funkcje włączone |
|:---|:---|:---|
| Brak konfiguracji | — Metryki platformy hosta zbierane do metryk.<br>-Zebrano dziennik aktywności. | — Eksplorator metryk dla hosta.<br>-Metryki alertów dla hosta.<br>-Alerty dziennika aktywności. |
| [Włącz Azure Monitor dla maszyn wirtualnych](#enable-azure-monitor-for-vms) | -Log Analytics zainstalowany agent.<br>Agent zależności został zainstalowany.<br>-Dane wydajności gościa zbierane do dzienników.<br>-Szczegóły procesu i zależności zbierane do dzienników. | -Schematy wydajności i skoroszyty dla danych wydajności gościa.<br>-Rejestruje zapytania dotyczące danych wydajności gościa.<br>-Rejestrowanie alertów dotyczących danych dotyczących wydajności gościa.<br>— Mapa zależności. |
| [Zainstaluj rozszerzenie diagnostyki i agenta telegraf](#enable-diagnostics-extension-and-telegraf-agent) | -Dane wydajności gościa zbierane do metryk. | — Eksplorator metryk dla gościa.<br>-Metryki alertów dla gościa.  |
| [Konfigurowanie obszaru roboczego Log Analytics](#configure-log-analytics-workspace) | -Zdarzenia zbierane z gościa. | -Rejestruj zapytania dla zdarzeń gościa.<br>-Rejestrowanie alertów dotyczących zdarzeń gościa. |
| [Utwórz ustawienie diagnostyczne dla maszyny wirtualnej](#collect-platform-metrics-and-activity-log) | — Metryki platformy zbierane do dzienników.<br>-Dziennik aktywności zebrany do dzienników. | -Rejestruje zapytania dotyczące metryk hosta.<br>-Rejestrowanie alertów dotyczących metryk hosta.<br>-Rejestruje zapytania dotyczące dziennika aktywności.

Każdy z tych kroków konfiguracyjnych został opisany w poniższych sekcjach.

### <a name="enable-azure-monitor-for-vms"></a>Włącz Azure Monitor dla maszyn wirtualnych
[Azure monitor dla maszyn wirtualnych](vminsights-overview.md) to [wgląd](insights-overview.md) w Azure monitor, który jest podstawowym narzędziem do monitorowania maszyn wirtualnych w Azure monitor. Zapewnia ona następującą dodatkową wartość za pośrednictwem standardowych funkcji Azure Monitor.

- Uproszczone dołączanie Log Analytics agenta i agenta zależności, aby umożliwić monitorowanie systemu operacyjnego gościa maszyny wirtualnej i obciążeń. 
- Wstępnie zdefiniowane wykresy wydajności trendów i skoroszyty umożliwiające analizowanie podstawowych metryk wydajności z systemu operacyjnego gościa maszyny wirtualnej.
- Mapa zależności, która wyświetla procesy działające na poszczególnych maszynach wirtualnych i połączonych składnikach z innymi maszynami i źródłami zewnętrznymi.

![Widok wydajności Azure Monitor dla maszyn wirtualnych](media/monitor-vm-azure/vminsights-01.png)

![Widok Azure Monitor dla maszyn wirtualnych Maps](media/monitor-vm-azure/vminsights-02.png)


Włącz Azure Monitor dla maszyn wirtualnych z poziomu usługi **Insights** w menu maszyny wirtualnej Azure Portal. Szczegóły i inne metody konfiguracji można znaleźć w temacie [włączanie Azure monitor dla maszyn wirtualnych przegląd](vminsights-enable-overview.md) .

![Włącz Azure Monitor dla maszyn wirtualnych](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego Log Analytics
Agent Log Analytics używany przez Azure Monitor dla maszyn wirtualnych wysyła dane do [obszaru roboczego log Analytics](../platform/data-platform-logs.md). Można włączyć zbieranie dodatkowych danych dotyczących wydajności, zdarzeń i innych danych monitorowania z agenta przez skonfigurowanie obszaru roboczego Log Analytics. Należy je skonfigurować tylko raz, ponieważ każdy agent łączący się z obszarem roboczym automatycznie pobierze konfigurację i natychmiast zacznie zbierać zdefiniowane dane. 

Możesz uzyskać dostęp do konfiguracji obszaru roboczego bezpośrednio z Azure Monitor dla maszyn wirtualnych, wybierając pozycję **Konfiguracja obszaru roboczego** z **okna Wprowadzenie**. Kliknij nazwę obszaru roboczego, aby otworzyć jego menu.

![Konfiguracja obszaru roboczego](media/monitor-vm-azure/workspace-configuration.png)

Wybierz pozycję **Ustawienia zaawansowane** z menu obszar roboczy, a następnie **dane** , aby skonfigurować źródła danych. W przypadku agentów systemu Windows wybierz pozycję **dzienniki zdarzeń systemu Windows** i Dodaj typowe dzienniki zdarzeń, takie jak *system* i *aplikacja*. W przypadku agentów systemu Linux wybierz pozycję **Dziennik** systemowy i Dodaj typowe obiekty, takie jak *kerning* i *demon*. Zobacz [źródła danych agentów w Azure monitor](../platform/agent-data-sources.md) , aby uzyskać listę dostępnych źródeł danych i szczegółowe informacje na temat ich konfigurowania. 

![Konfigurowanie zdarzeń](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Można skonfigurować liczniki wydajności, które mają być zbierane z konfiguracji obszaru roboczego, ale mogą być nadmiarowe przy użyciu liczników wydajności zbieranych przez Azure Monitor dla maszyn wirtualnych. Azure Monitor dla maszyn wirtualnych zbiera najbardziej typowy zestaw liczników z częstotliwością raz na minutę. Skonfiguruj tylko liczniki wydajności, które mają być zbierane przez obszar roboczy, jeśli chcesz zbierać liczniki, które nie zostały jeszcze zebrane przez Azure Monitor dla maszyn wirtualnych lub istniejące zapytania przy użyciu danych wydajności.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Włącz rozszerzenie diagnostyki i agenta telegraf
Azure Monitor dla maszyn wirtualnych jest oparta na agencie Log Analytics, który wysyła dane do obszaru roboczego Log Analytics. Obsługuje to wiele funkcji Azure Monitor, takich jak [zapytania dziennika](../log-query/log-query-overview.md), [alerty dzienników](../platform/alerts-log.md)i [skoroszyty](../platform/workbooks-overview.md). [Rozszerzenie Diagnostics](../platform/diagnostics-extension-overview.md) zbiera dane dotyczące wydajności z systemu operacyjnego gościa maszyn wirtualnych z systemem Windows w usłudze Azure Storage i opcjonalnie wysyła dane dotyczące wydajności do [metryk Azure monitor](../platform/data-platform-metrics.md). W przypadku maszyn wirtualnych z systemem Linux [Agent telegraf](../platform/collect-custom-metrics-linux-telegraf.md) jest wymagany do wysyłania danych do metryk platformy Azure.  Dzięki temu inne funkcje Azure Monitor, takie jak [Eksplorator metryk](../platform/metrics-getting-started.md) i [alerty metryki](../platform/alerts-metric.md). Można również skonfigurować rozszerzenie diagnostyki do wysyłania zdarzeń i danych wydajności poza Azure Monitor przy użyciu usługi Azure Event Hubs.

Zainstaluj rozszerzenie Diagnostyka dla pojedynczej maszyny wirtualnej z systemem Windows w Azure Portal z opcji **Ustawienia diagnostyki** w menu maszyny wirtualnej. Wybierz opcję Włącz **Azure monitor** na karcie **ujścia** . Aby włączyć rozszerzenie z szablonu lub wiersza polecenia dla wielu maszyn wirtualnych, zobacz [Instalowanie i Konfigurowanie](../platform/diagnostics-extension-overview.md#installation-and-configuration). W przeciwieństwie do agenta Log Analytics dane do zebrania są zdefiniowane w konfiguracji rozszerzenia na każdej maszynie wirtualnej.

![Ustawienie diagnostyczne](media/monitor-vm-azure/diagnostic-setting.png)

Aby uzyskać szczegółowe informacje na temat konfigurowania agentów telegraf na maszynach wirtualnych z systemem Linux, zobacz temat [Instalowanie i Konfigurowanie telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) . Opcja menu **Ustawienia diagnostycznego** jest dostępna w systemie Linux, ale umożliwia wysyłanie danych tylko do usługi Azure Storage.

### <a name="collect-platform-metrics-and-activity-log"></a>Zbieranie metryk platformy i dziennika aktywności
Można wyświetlić metryki platformy i dziennik aktywności zebrane dla każdego hosta maszyny wirtualnej w Azure Portal. Zbierz te dane do tego samego obszaru roboczego Log Analytics, co Azure Monitor dla maszyn wirtualnych, aby przeanalizować je przy użyciu innych danych monitorowania zbieranych dla maszyny wirtualnej. Dla tej kolekcji skonfigurowano [ustawienie diagnostyczne](../platform/diagnostic-settings.md). Zbierz dziennik aktywności z [ustawieniem diagnostycznym dla subskrypcji](../platform/diagnostic-settings.md#create-in-azure-portal).

Zbieraj metryki platformy z ustawieniem diagnostycznym dla maszyny wirtualnej. W przeciwieństwie do innych zasobów platformy Azure, nie można utworzyć ustawienia diagnostycznego dla maszyny wirtualnej w Azure Portal, ale musi użyć [innej metody](../platform/diagnostic-settings.md#create-using-powershell). W poniższych przykładach pokazano, jak zbierać metryki dla maszyny wirtualnej przy użyciu programu PowerShell i interfejsu wiersza polecenia.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Monitorowanie w Azure Portal 
Po skonfigurowaniu kolekcji danych monitorowania dla maszyny wirtualnej masz wiele opcji dostępu do nich w Azure Portal:

- Użyj menu **Azure monitor** , aby uzyskać dostęp do danych ze wszystkich monitorowanych zasobów. 
- Użyj Azure Monitor dla maszyn wirtualnych do monitorowania zestawów maszyn wirtualnych w odpowiedniej skali.
- Analizuj dane dla pojedynczej maszyny wirtualnej z jej menu w Azure Portal. W poniższej tabeli wymieniono różne opcje monitorowania menu maszyny wirtualne.

![Monitorowanie w Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Opcja menu | Opis |
|:---|:---|
| Omówienie | Przedstawia [metryki platformy](../platform/data-platform-metrics.md) dla hosta maszyny wirtualnej. Kliknij wykres, aby korzystać z tych danych w [Eksploratorze metryk](../platform/metrics-getting-started.md). |
| Dziennik aktywności | Wpisy [dziennika aktywności](../platform/activity-log.md#view-the-activity-log) odfiltrowane dla bieżącej maszyny wirtualnej. |
| Insights | Otwiera [Azure monitor dla maszyn wirtualnych](./vminsights-overview.md) z mapą dla zaznaczonej bieżącej maszyny wirtualnej. |
| Alerty | Wyświetla [alerty](../platform/alerts-overview.md) dla bieżącej maszyny wirtualnej.  |
| Metryki | Otwórz [Eksploratora metryk](../platform/metrics-getting-started.md) z zakresem ustawionym na bieżącą maszynę wirtualną. |
| Ustawienia diagnostyczne | Włącz i skonfiguruj [rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) dla bieżącej maszyny wirtualnej. |
| Zalecenia doradcy | Zalecenia dotyczące bieżącej maszyny wirtualnej z [Azure Advisor](../../advisor/index.yml). |
| Dzienniki | Otwórz [log Analytics](../log-query/log-analytics-overview.md) z [zakresem](../log-query/scope.md) ustawionym na bieżącą maszynę wirtualną. |
| Monitor połączeń | Otwórz [monitor połączeń Network Watcher](../../network-watcher/connection-monitor-preview.md) , aby monitorować połączenia między bieżącą maszyną wirtualną a innymi maszynami wirtualnymi. |


## <a name="analyzing-metric-data"></a>Analizowanie danych metryki
Metryki maszyn wirtualnych można analizować za pomocą Eksploratora metryk, otwierając **metryki** z menu maszyny wirtualnej. Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../platform/metrics-getting-started.md) . 

Istnieją trzy przestrzenie nazw używane przez maszyny wirtualne do metryk:

| Przestrzeń nazw | Opis | Wymaganie |
|:---|:---|:---|
| Host maszyny wirtualnej | Metryki hostów zbierane automatycznie dla wszystkich maszyn wirtualnych platformy Azure. Szczegółowa lista metryk w [firmie Microsoft. COMPUTE/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Zbierane automatycznie bez konieczności konfigurowania. |
| Gość (klasyczny) | Ograniczony zestaw danych dotyczących systemu operacyjnego gościa i wydajności aplikacji. Dostępne w Eksploratorze metryk, ale nie inne funkcje Azure Monitor, takie jak alerty metryki.  | [Rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) zostało zainstalowane. Dane są odczytywane z usługi Azure Storage.  |
| Gość maszyny wirtualnej | Dane dotyczące systemu operacyjnego gościa i wydajności aplikacji są dostępne dla wszystkich funkcji Azure Monitor przy użyciu metryk. | Dla systemu Windows [zainstalowane rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) zainstalowane z włączonym Azure monitor ujścia. W przypadku systemu Linux [zainstalowano agenta telegraf](../platform/collect-custom-metrics-linux-telegraf.md). |

![Eksplorator metryk w Azure Portal](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika
Usługi Azure Virtual Machines będą zbierać następujące dane do dzienników Azure Monitor. 

Azure Monitor dla maszyn wirtualnych umożliwia zbieranie wstępnie określonego zestawu liczników wydajności, które są zapisywane w tabeli *InsightsMetrics* . Jest to ta sama tabela, która jest używana przez [Azure monitor dla kontenerów](container-insights-overview.md). 

| Źródło danych | Wymagania | Tabele |
|:---|:---|:---|
| Usługa Azure Monitor dla maszyn wirtualnych | Włącz na każdej maszynie wirtualnej. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Aby uzyskać szczegółowe informacje [, zobacz jak wykonywać zapytania dotyczące dzienników z Azure monitor dla maszyn wirtualnych](vminsights-log-search.md) . |
| Dziennik aktywności | Ustawienie diagnostyczne dla subskrypcji. | AzureActivity |
| Metryki hosta | Ustawienie diagnostyczne dla maszyny wirtualnej. | AzureMetrics |
| Źródła danych z systemu operacyjnego gościa | Włącz agenta Log Analytics i skonfiguruj źródła danych. | Zapoznaj się z dokumentacją dla każdego źródła danych. |


> [!NOTE]
> Dane wydajności zbierane przez agenta Log Analytics są zapisywane w tabeli *wydajności* , podczas gdy Azure monitor dla maszyn wirtualnych zbiera je do tabeli *InsightsMetrics* . Są to te same dane, ale tabele mają inną strukturę. Jeśli masz istniejące zapytania na podstawie *wydajności* , trzeba będzie je ponownie napisać, aby użyć *InsightsMetrics*.


## <a name="alerts"></a>Alerty
[Alerty](../platform/alerts-overview.md) w usłudze Azure monitor aktywnie powiadamiają użytkownika, gdy w danych monitorowania zostaną znalezione ważne warunki i mogą oni uruchamiać akcje, takie jak uruchamianie aplikacji logiki lub wywoływanie elementu webhook. Reguły alertów definiują logikę używaną do określania, kiedy należy utworzyć alert. Azure Monitor zbiera dane używane przez reguły alertów, ale należy utworzyć reguły definiowania warunków alertów w ramach subskrypcji platformy Azure.

W poniższych sekcjach opisano typy reguł alertów i zaleceń, które należy wykonać w przypadku każdej z nich. To zalecenie jest zależne od funkcjonalności i kosztu typu reguły alertu. Aby uzyskać szczegółowe informacje na temat cennika alertów, zobacz [Cennik usługi Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Reguły alertów dziennika aktywności
[Reguły alertów dziennika aktywności](../platform/alerts-activity-log.md) są wyzwalane, gdy w dzienniku aktywności zostanie utworzony wpis zgodny z określonymi kryteriami. Nie są one kosztami, więc powinny być pierwszym wyborem, jeśli wymagana logika znajduje się w dzienniku aktywności. 

Zasób docelowy dla alertów dziennika aktywności może być określoną maszyną wirtualną, wszystkimi maszynami wirtualnymi w grupie zasobów lub wszystkimi maszynami wirtualnymi w ramach subskrypcji.

Na przykład utwórz alert w przypadku zatrzymania krytycznej maszyny wirtualnej, wybierając *maszynę wirtualną z wyłączeniem* dla nazwy sygnału.

![Alert dziennika aktywności](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Reguły alertów metryk
[Reguły alertów metryki](../platform/alerts-metric.md) są wyzwalane, gdy wartość metryki przekroczy próg. Można zdefiniować określoną wartość progową lub zezwolić Azure Monitor na dynamiczne określanie progu na podstawie danych historycznych.  W miarę możliwości można używać alertów metryk, ponieważ są one tańsze i są wydajniejsze niż reguły alertów dzienników. Są one również stanowe, co rozwiąże się, gdy Metryka spadnie poniżej wartości progowej.

Zasób docelowy dla alertów dziennika aktywności może być określoną maszyną wirtualną lub wszystkimi maszynami wirtualnymi w grupie zasobów.

Na przykład aby utworzyć alert, gdy procesor maszyny wirtualnej przekroczy określoną wartość, Utwórz regułę alertu metryki przy użyciu *procentowego czasu procesora* jako typu sygnału. Ustaw określoną wartość progową lub Zezwól Azure Monitor na ustawienie progu dynamicznego. 

![Alert dotyczący metryki](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alerty dotyczące dzienników
[Reguły alertów dziennika](../platform/alerts-log.md) są wyzwalane, gdy wyniki zaplanowanego zapytania dziennika są zgodne z określonymi kryteriami. Alerty zapytań dzienników są najbardziej kosztowne i najmniej odpowiadać regułom alertów, ale mają dostęp do najbardziej różnorodnych danych i mogą wykonywać złożone logiki, które nie mogą być wykonywane przez inne reguły alertów. 

Zasób docelowy dla kwerendy dziennika jest obszarem roboczym Log Analytics. Odfiltruj określone komputery w zapytaniu.

Na przykład, aby utworzyć alert, który sprawdza, czy maszyny wirtualne w danej grupie zasobów są w trybie offline, użyj następującego zapytania, które zwraca rekord dla każdego komputera, który nie został pominięty w ciągu ostatnich dziesięciu minut. Użyj wartości progowej 1, która jest uruchamiana, jeśli co najmniej jeden komputer ma pominięty puls.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alert dotyczący dziennika dla pominiętego pulsu](media/monitor-vm-azure/log-alert-01.png)

Aby utworzyć alert, jeśli wystąpiła zbyt wiele nieudanych logowań na wszystkich maszynach wirtualnych z systemem Windows w ramach subskrypcji, użyj następującego zapytania, które zwraca rekord dla każdego nieudanego zdarzenia logowania w ciągu ostatniej godziny. Użyj progu ustawionego na liczbę nieudanych logowań, na które zezwolisz. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Alert dziennika dla nieudanych logowań](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager zapewnia szczegółowe monitorowanie obciążeń na maszynach wirtualnych. Zapoznaj się z [przewodnikiem monitorowania chmury](/azure/cloud-adoption-framework/manage/monitor/) , aby zapoznać się z porównaniem platform monitorowania i różnych strategii wdrażania.

Jeśli masz istniejące środowisko Operations Manager, które ma być używane, możesz je zintegrować z Azure Monitor, aby zapewnić dodatkową funkcjonalność. Agent Log Analytics używany przez Azure Monitor jest taki sam, jak używany w Operations Manager, aby monitorowane maszyny wirtualne mogły wysyłać dane do obu tych programów. Nadal musisz dodać agenta do Azure Monitor dla maszyn wirtualnych i skonfigurować obszar roboczy do zbierania dodatkowych danych, jak określono powyżej, ale maszyny wirtualne mogą nadal uruchamiać istniejące pakiety administracyjne w środowisku Operations Manager bez modyfikacji.

Funkcje Azure Monitor rozszerzające istniejące funkcje Operations Manager obejmują następujące elementy:

- Użyj Log Analytics, aby interaktywnie analizować dane dziennika i wydajności.
- Alerty dzienników umożliwiają definiowanie warunków alertów na wielu maszynach wirtualnych i korzystanie z krótkoterminowych trendów, które nie są możliwe przy użyciu alertów w Operations Manager.   

Aby uzyskać szczegółowe informacje na temat łączenia istniejącej grupy zarządzania Operations Manager z obszarem roboczym Log Analytics, zobacz [Connect Azure Monitor Operations Manager](../platform/om-agents.md) .


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak analizować dane w dziennikach Azure Monitor przy użyciu zapytań dzienników.](../log-query/get-started-queries.md)
* [Dowiedz się więcej o alertach korzystających z metryk i dzienników w Azure Monitor.](../platform/alerts-overview.md)

