---
title: Omówienie agentów monitorowania platformy Azure | Microsoft Docs
description: Ten artykuł zawiera szczegółowe omówienie dostępnych agentów platformy Azure, które obsługują monitorowanie maszyn wirtualnych hostowanych na platformie Azure lub w środowisku hybrydowym.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a9786c1f596a9f59e63886fa503bddac58ee7a8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325342"
---
# <a name="overview-of-azure-monitor-agents"></a>Omówienie agentów Azure Monitor

Maszyny wirtualne i inne zasoby obliczeniowe wymagają od agenta zebrania danych monitorowania w celu mierzenia wydajności i dostępności systemu operacyjnego gościa oraz obciążeń. W tym artykule opisano agentów używanych przez Azure Monitor i można określić, które z nich muszą spełniać wymagania dotyczące konkretnego środowiska.

> [!NOTE]
> Azure Monitor ma obecnie wielu agentów z powodu ostatniej konsolidacji Azure Monitor i Log Analytics. Chociaż może się nakładać na swoje funkcje, każdy z nich ma unikatowe możliwości. W zależności od wymagań może być potrzebny co najmniej jeden Agent na maszynach wirtualnych. 

Może istnieć określony zestaw wymagań, które nie mogą być w pełni spełnione przy użyciu jednego agenta dla określonej maszyny wirtualnej. Można na przykład użyć alertów metryk, które wymagają rozszerzenia diagnostyki platformy Azure, ale również skorzystać z funkcjonalności Azure Monitor dla maszyn wirtualnych, która wymaga agenta Log Analytics i agenta zależności. W takich przypadkach można używać wielu agentów i jest to typowy scenariusz dla klientów, którzy wymagają funkcjonalności z każdego z nich.

## <a name="summary-of-agents"></a>Podsumowanie agentów

W poniższych tabelach przedstawiono krótkie porównanie Azure Monitor agentów dla systemów Windows i Linux. Więcej szczegółowych informacji na ten temat znajduje się w sekcji poniżej. 

### <a name="windows-agents"></a>Agenci dla systemu Windows

| | Diagnostyka<br>rozszerzenie (funkcji wad) | Log Analytics<br>agent | Zależność<br>agent |
|:---|:---|:---|:---|
| **Obsługiwane środowiska** | Azure | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Środowiska lokalne | 
| **Wymagania dotyczące agenta**  | Brak | Brak | Wymaga agenta Log Analytics |
| **Zbierane dane** | Dzienniki zdarzeń<br>zdarzenia ETW<br>Wydajność<br>Dzienniki na podstawie plików<br>Dzienniki usług IIS<br>Dzienniki aplikacji .NET<br>Zrzuty awaryjne<br>Dzienniki diagnostyki agentów | Dzienniki zdarzeń<br>Wydajność<IIS logs><br>Dzienniki na podstawie plików<br>Szczegółowe informacje i rozwiązania<br>Inne usługi | Szczegóły procesu i zależności<br>Metryki połączenia sieciowego |
| **Dane wysyłane do** | Azure Storage<br>Metryki Azure Monitor<br>Centrum zdarzeń | Dzienniki usługi Azure Monitor | Dzienniki usługi Azure Monitor |


### <a name="linux-agents"></a>Agenci dla systemu Linux

| | Diagnostyka<br>rozszerzenie (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Zależność<br>agent |
|:---|:---|:---|:---|:---|
| **Obsługiwane środowiska** | Azure | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Środowiska lokalne |
| **Wymagania dotyczące agenta**  | Brak | Brak | Brak | Wymaga agenta Log Analytics |
| **Zbierane dane** | Dziennik systemu<br>Wydajność | Wydajność | Dziennik systemu<br>Wydajność| Szczegóły procesu i zależności<br>Metryki połączenia sieciowego |
| **Dane wysyłane do** | Azure Storage<br>Centrum zdarzeń | Metryki Azure Monitor | Dzienniki usługi Azure Monitor | Dzienniki usługi Azure Monitor |

## <a name="log-analytics-agent"></a>Agent usługi Log Analytics

[Agent log Analytics](log-analytics-agent.md) zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych na platformie Azure, innych dostawców chmury i lokalnie. Gromadzi dane w obszarze roboczym Log Analytics. Agent Log Analytics jest tym samym agentem, który jest używany przez System Center Operations Manager, i można korzystać z agentów wieloznacznych, aby komunikować się z grupą zarządzania i Azure Monitor jednocześnie. Ten agent jest również wymagany przez niektóre szczegółowe informacje i rozwiązania w Azure Monitor.


> [!NOTE]
> Agent Log Analytics dla systemu Windows jest często określany jako Microsoft Monitoring Agent (MMA). Agent Log Analytics dla systemu Linux jest często nazywany agentem pakietu OMS.



Użyj agenta Log Analytics, jeśli zachodzi taka potrzeba:

* Zbieraj dzienniki i dane wydajności z maszyn wirtualnych lub fizycznych poza platformą Azure. 
* Wyślij dane do obszaru roboczego Log Analytics, aby korzystać z funkcji obsługiwanych przez [dzienniki Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , takie jak [zapytania dziennika](../log-query/log-query-overview.md).
* Użyj [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) , która pozwala monitorować maszyny wirtualne w odpowiedniej skali i monitoruje ich procesy oraz zależności od innych zasobów i procesów zewnętrznych.  
* Zarządzaj zabezpieczeniami maszyn wirtualnych przy użyciu [Azure Security Center](../../security-center/security-center-intro.md) lub [platformy Azure](../../sentinel/overview.md).
* Użyj [Azure Automation zarządzania aktualizacjami](../../automation/automation-update-management.md), [Azure Automation konfiguracji stanu](../../automation/automation-dsc-overview.md)lub [Azure Automation Change Tracking i spisu](../../automation/change-tracking.md) , aby zapewnić kompleksowe zarządzanie maszynami wirtualnymi platformy Azure
* Użyj różnych [rozwiązań](../monitor-reference.md#insights-and-core-solutions) do monitorowania określonej usługi lub aplikacji.

Ograniczenia Log Analytics agenta obejmują:

- Nie można wysyłać danych do metryk Azure Monitor, usługi Azure Storage ani Event Hubs platformy Azure.

## <a name="azure-diagnostics-extension"></a>Rozszerzenie diagnostyki platformy Azure

[Rozszerzenie Diagnostyka Azure](diagnostics-extension-overview.md) zbiera dane monitorowania z systemu operacyjnego gościa oraz obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. Polega ona głównie na zbieraniu danych do usługi Azure Storage, ale również umożliwia zdefiniowanie ujścia danych w celu wysyłania danych do innych miejsc docelowych, takich jak metryki Azure Monitor i Event Hubs platformy Azure.

Użyj rozszerzenia diagnostyki platformy Azure, jeśli zachodzi taka potrzeba:

- Wyślij dane do usługi Azure Storage w celu archiwizacji lub Przeanalizuj je za pomocą narzędzi, takich jak [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Wyślij dane do [metryk Azure monitor](data-platform-metrics.md) , aby przeanalizować je za pomocą [Eksploratora metryk](metrics-getting-started.md) i korzystać z funkcji, takich jak [alerty metryk](./alerts-metric-overview.md) niemal w czasie rzeczywistym i [Automatyczne skalowanie](autoscale-overview.md) (tylko system Windows).
- Wyślij dane do narzędzi innych firm przy użyciu [usługi Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Zbierz [diagnostykę rozruchu](../../virtual-machines/troubleshooting/boot-diagnostics.md) , aby zbadać problemy z rozruchem maszyny wirtualnej.

Ograniczenia rozszerzenia usługi Azure Diagnostics obejmują:

- Może być używany tylko z zasobami platformy Azure.
- Ograniczona możliwość wysyłania danych do dzienników Azure Monitor.

## <a name="telegraf-agent"></a>Agent telegraf

[Agent InfluxData telegraf](collect-custom-metrics-linux-telegraf.md) jest używany do zbierania danych wydajności z komputerów z systemem Linux do metryk Azure monitor.

Użyj agenta telegraf, jeśli chcesz:

* Wyślij dane do [metryk Azure monitor](data-platform-metrics.md) , aby przeanalizować je za pomocą [Eksploratora metryk](metrics-getting-started.md) i korzystać z funkcji, takich jak [alerty metryk](./alerts-metric-overview.md) niemal w czasie rzeczywistym i [Automatyczne skalowanie](autoscale-overview.md) (tylko system Linux). 



## <a name="dependency-agent"></a>Agent zależności

Agent zależności zbiera odnalezione dane dotyczące procesów uruchomionych na maszynie wirtualnej i zewnętrznych zależności procesów. 

Użyj agenta zależności, jeśli zachodzi taka potrzeba:

* Użyj funkcji map [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) lub [Service map](../insights/service-map.md) rozwiązanie.

Podczas korzystania z agenta zależności należy wziąć pod uwagę następujące kwestie:

- Agent zależności wymaga zainstalowania agenta Log Analytics na tej samej maszynie wirtualnej.
- Na maszynach wirtualnych z systemem Linux należy zainstalować agenta Log Analytics przed rozszerzeniem diagnostyki platformy Azure.

## <a name="extensions-compared-to-agents"></a>Rozszerzenia w porównaniu z agentami

Log Analytics rozszerzenie dla [systemów Windows](../../virtual-machines/extensions/oms-windows.md) i [Linux](../../virtual-machines/extensions/oms-linux.md) Zainstaluj agenta log Analytics na maszynach wirtualnych platformy Azure. Rozszerzenie zależności Azure Monitor dla [systemu Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) Zainstaluj agenta zależności na maszynach wirtualnych platformy Azure. Są to te same agenci, ale umożliwiają zarządzanie nimi za pomocą [rozszerzeń maszyny wirtualnej](../../virtual-machines/extensions/overview.md). Jeśli to możliwe, należy używać rozszerzeń do instalowania agentów i zarządzania nimi.


## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat każdego z agentów znajduje się w następujących tematach:

- [Przegląd agenta Log Analytics](log-analytics-agent.md)
- [Przegląd rozszerzenia Diagnostyka Azure](diagnostics-extension-overview.md)
- [Zbieranie niestandardowych metryk dla maszyny wirtualnej z systemem Linux za pomocą agenta InfluxData telegraf](collect-custom-metrics-linux-telegraf.md)

