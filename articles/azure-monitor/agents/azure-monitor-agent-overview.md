---
title: Omówienie agenta Azure Monitor
description: Przegląd Azure Monitor Agent (AMA), który gromadzi dane monitorowania z systemu operacyjnego gościa maszyn wirtualnych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.custom: references_regions
ms.openlocfilehash: f1f1ea787406d900c8035c0462ef903b848d7e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608218"
---
# <a name="azure-monitor-agent-overview-preview"></a>Omówienie agenta Azure Monitor (wersja zapoznawcza)
Agent Azure Monitor (AMA) zbiera dane monitorowania z systemu operacyjnego gościa maszyn wirtualnych i dostarcza go do Azure Monitor. Ten artykuł zawiera omówienie agenta Azure Monitor, w tym sposobu instalowania go i konfigurowania zbierania danych.

## <a name="relationship-to-other-agents"></a>Relacja z innymi agentami
Agent Azure Monitor zastępuje następujących agentów, które są obecnie używane przez Azure Monitor do zbierania danych Gościa z maszyn wirtualnych:

- [Agent log Analytics](./log-analytics-agent.md) — wysyła dane do log Analytics obszaru roboczego i obsługuje rozwiązania do monitorowania maszyn wirtualnych.
- [Rozszerzenie diagnostyczne](./diagnostics-extension-overview.md) — wysyła dane do metryk Azure monitor (tylko system Windows), Azure Event Hubs i Azure Storage.
- [Telegraf Agent](../essentials/collect-custom-metrics-linux-telegraf.md) — wysyła dane do metryk Azure monitor (tylko system Linux).

Oprócz konsolidacji tej funkcji w ramach jednego agenta Agent Azure Monitor zapewnia następujące korzyści w stosunku do istniejących agentów:

- Zakres monitorowania. Centralna konfiguracja kolekcji dla różnych zestawów danych z różnych zestawów maszyn wirtualnych.  
- Linux multihostingu: wysyłanie danych z maszyn wirtualnych systemu Linux do wielu obszarów roboczych.
- Filtrowanie zdarzeń systemu Windows: Użyj zapytań XPATH do filtrowania, które zdarzenia systemu Windows są zbierane.
- Ulepszone Zarządzanie rozszerzeniami: Agent Azure Monitor korzysta z nowej metody obsługi rozszerzalności, która jest bardziej przejrzysta i kontrolowanych niż pakiety administracyjne i wtyczki systemu Linux w bieżącym agencie Log Analytics.

### <a name="changes-in-data-collection"></a>Zmiany w zbieraniu danych
Metody definiowania zbierania danych dla istniejących agentów różnią się od siebie nawzajem, a każdy z nich ma wyzwania, które są rozłączone z agentem Azure Monitor.

- Agent Log Analytics pobiera swoją konfigurację z Log Analytics obszaru roboczego. Jest to łatwe do scentralizowanego konfigurowania, ale trudno definiować definicje niezależne dla różnych maszyn wirtualnych. Dane można wysyłać tylko do obszaru roboczego Log Analytics.

- Rozszerzenie diagnostyczne ma konfigurację dla każdej maszyny wirtualnej. Jest to łatwe definiowanie niezależnych definicji dla różnych maszyn wirtualnych, ale trudno jest centralnie zarządzać. Może on wysyłać tylko dane do Azure Monitor metryk, Azure Event Hubs lub Azure Storage. W przypadku agentów systemu Linux Agent telegraf Open Source jest wymagany do wysyłania danych do metryk Azure Monitor.

Agent Azure Monitor używa [reguł zbierania danych (DCR)](data-collection-rule-overview.md) do konfigurowania danych zbieranych z poszczególnych agentów. Reguły zbierania danych umożliwiają zarządzanie ustawieniami kolekcji na dużą skalę, jednocześnie umożliwiając jednocześnie korzystanie z unikatowych, w zakresie konfiguracji dla podzestawów maszyn. Są one niezależne od obszaru roboczego i niezależne od maszyny wirtualnej, dzięki czemu można je definiować raz i ponownie używać między maszynami i środowiskami. Zobacz [Konfigurowanie zbierania danych dla agenta Azure monitor (wersja zapoznawcza)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Czy należy przełączyć się na Azure Monitor agenta?
Azure Monitor Agent współistnieje z [ogólnie dostępnymi agentami dla Azure monitor](agents-overview.md), ale możesz rozważyć przechodzenie maszyn wirtualnych do bieżącego agenta w okresie publicznej wersji zapoznawczej agenta programu Azure monitor. Podczas dokonywania tego ustalenia należy wziąć pod uwagę następujące czynniki.

- **Wymagania dotyczące środowiska.** Agent Azure Monitor ma bardziej ograniczony zestaw obsługiwanych systemów operacyjnych, środowisk i wymagań sieci niż bieżący agenci. Przyszła obsługa środowiska, taka jak nowe wersje systemu operacyjnego i typy wymagań sieci, najprawdopodobniej będzie dostępna tylko w agencie Azure Monitor. Należy ocenić, czy środowisko jest obsługiwane przez Azure Monitor agenta. W przeciwnym razie trzeba będzie pozostać z bieżącym agentem. Jeśli Agent Azure Monitor obsługuje bieżące środowisko, należy rozważyć przejście do niego.
- **Tolerancja ryzyka dla publicznej wersji zapoznawczej.** Mimo że Agent Azure Monitor został dokładnie przetestowany dla obecnie obsługiwanych scenariuszy, Agent nadal jest w publicznej wersji zapoznawczej. Aktualizacje wersji i ulepszenia funkcjonalności będą często wykonywane i mogą wprowadzać usterki. Należy ocenić ryzyko wystąpienia usterki agenta na maszynach wirtualnych, które mogą zatrzymać zbieranie danych. Jeśli przerwy w zbieraniu danych nie mają znaczącego wpływu na usługi, przejdź Azure Monitor agenta. Jeśli masz małą tolerancję dla każdej niestabilności, należy pozostawać z ogólnie dostępnymi agentami, dopóki Azure Monitor Agent osiągnie ten stan.
- **Bieżące i nowe wymagania dotyczące funkcji.** Azure Monitor Agent wprowadza kilka nowych funkcji, takich jak filtrowanie, określanie zakresu i wiele multihostingu, ale nie jest jeszcze parzysta z bieżącymi agentami do obsługi innych funkcji, takich jak kolekcja dzienników niestandardowych i integracja z rozwiązaniami. Większość nowych funkcji w Azure Monitor zostanie udostępniona tylko z agentem Azure Monitor, dzięki czemu większa funkcjonalność będzie dostępna tylko w nowym agencie. Należy wziąć pod uwagę, czy Agent Azure Monitor ma wymagane funkcje, a jeśli istnieją pewne funkcje, które można tymczasowo wykonać bez konieczności uzyskiwania innych ważnych funkcji w nowym agencie. Jeśli Agent Azure Monitor ma wszystkie wymagane funkcje podstawowe, należy rozważyć przejście do niego. Jeśli istnieją kluczowe funkcje, których potrzebujesz, Kontynuuj od bieżącego agenta, dopóki Azure Monitor Agent osiągnie parzystość.
- **Tolerancja dla pozostałej pracy.** W przypadku konfigurowania nowego środowiska z zasobami, takimi jak skrypty wdrażania i szablony dołączania, należy rozważyć, czy będzie można je obsłużyć, gdy Agent Azure Monitor stanie się ogólnie dostępny. Jeśli nakład pracy związany z tą pracą będzie minimalny, należy teraz pozostać na bieżąco z bieżącymi agentami. Jeśli zajmie to znaczną ilość pracy, rozważ skonfigurowanie nowego środowiska z nowym agentem. Agent Azure Monitor powinien stać się ogólnie dostępny i Data wycofania opublikowana dla agentów Log Analytics w 2021. Bieżący agenci będą obsługiwani przez kilka lat po rozpoczęciu wycofania.



## <a name="current-limitations"></a>Bieżące ograniczenia
W publicznej wersji zapoznawczej agenta Azure Monitor są stosowane następujące ograniczenia:

- Agent Azure Monitor nie obsługuje rozwiązań i szczegółowych informacji, takich jak usługa VM Insights i Azure Security Center. Jedynym scenariuszem obsługiwanym obecnie jest zbieranie danych przy użyciu skonfigurowanych reguł zbierania danych. 
- Reguły zbierania danych muszą zostać utworzone w tym samym regionie, w którym znajduje się obszar roboczy Log Analytics używany jako miejsce docelowe.
- Obecnie obsługiwane są usługi Azure Virtual Machines, Virtual Machine Scale Sets i serwery z włączonym funkcją Arc platformy Azure. Usługa Azure Kubernetes i inne typy zasobów obliczeniowych nie są obecnie obsługiwane.
- Maszyna wirtualna musi mieć dostęp do następujących punktów końcowych HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="supported-regions"></a>Obsługiwane regiony
Agent Azure Monitor obecnie obsługuje zasoby w następujących regionach:

- Azja Wschodnia
- Southeast Asia
- Australia Środkowa
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Kanada Środkowa
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Niemcy Środkowo-Zachodnie
- Indie Środkowe
- Japonia Wschodnia
- Korea Środkowa
- Północna Republika Południowej Afryki
- Szwajcaria Północna
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Central US
- East US
- Wschodnie stany USA 2
- Północno-środkowe stany USA
- South Central US
- Zachodnie stany USA
- Zachodnie stany USA 2
- Zachodnio-środkowe stany USA

## <a name="coexistence-with-other-agents"></a>Współistnienie z innymi agentami
Agent Azure Monitor może współistnieć z istniejącymi agentami, aby można było nadal korzystać z istniejących funkcji podczas oceny lub migracji. Jest to szczególnie ważne ze względu na ograniczenia w publicznej wersji zapoznawczej w obsłudze istniejących rozwiązań. Należy zachować ostrożność podczas zbierania zduplikowanych danych, ponieważ może to spowodować pochylenie wyników zapytania i pozyskanie dodatkowych opłat za pozyskiwanie i przechowywanie danych.

Na przykład w usłudze VM Insights Agent Log Analytics wysyła dane wydajności do Log Analytics obszaru roboczego. Możesz również skonfigurować obszar roboczy do zbierania zdarzeń systemu Windows i zdarzeń dziennika systemowego z agentów. W przypadku zainstalowania agenta Azure Monitor i utworzenia reguły zbierania danych dla tych samych zdarzeń i danych wydajności spowoduje to zduplikowanie danych.


## <a name="costs"></a>Koszty
Nie ma kosztu dla agenta Azure Monitor, ale opłaty za dane pozyskiwane mogą być naliczane. Zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) , aby uzyskać szczegółowe informacje na temat log Analytics zbierania i przechowywania danych oraz metryki klientów.

## <a name="data-sources-and-destinations"></a>Źródła danych i miejsca docelowe
Poniższa tabela zawiera listę typów danych, które można obecnie zbierać z agentem Azure Monitor przy użyciu reguł zbierania danych i skąd można wysłać te dane. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby zapoznać się z listą szczegółowych informacji, rozwiązań i innych rozwiązań, które używają agenta Azure monitor do zbierania innych rodzajów danych.


Agent Azure Monitor wysyła dane do metryk Azure Monitor lub obszaru roboczego Log Analytics obsługi dzienników Azure Monitor.

| Źródło danych | Miejsca docelowe | Opis |
|:---|:---|:---|
| Wydajność        | Metryki usługi Azure Monitor<br>Obszar roboczy usługi Log Analytics | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Dzienniki zdarzeń systemu Windows | Obszar roboczy usługi Log Analytics | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| Dziennik systemu             | Obszar roboczy usługi Log Analytics | Informacje wysyłane do systemu rejestrowania zdarzeń w systemie Linux. |


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Zobacz [obsługiwane systemy operacyjne](agents-overview.md#supported-operating-systems) , aby uzyskać listę wersji systemu operacyjnego Windows i Linux, które są obecnie obsługiwane przez agenta Azure monitor.



## <a name="security"></a>Zabezpieczenia
Agent Azure Monitor nie wymaga żadnych kluczy, ale wymaga [tożsamości zarządzanej przypisanej do systemu](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Przed wdrożeniem agenta na każdej maszynie wirtualnej musi być włączona tożsamość zarządzana przypisana przez system.

## <a name="networking"></a>Sieć
Agent Azure Monitor obsługuje Tagi usług platformy Azure (wymagane są Tagi AzureMonitor i AzureResourceManager), ale nie działa ona jeszcze z Azure Monitor prywatnymi zakresami linków lub bezpośrednimi serwerami proxy.


## <a name="next-steps"></a>Następne kroki

- [Zainstaluj agenta Azure monitor](azure-monitor-agent-install.md) na maszynach wirtualnych z systemem Windows i Linux.
- [Utwórz regułę zbierania danych](data-collection-rule-azure-monitor-agent.md) , aby zbierać dane z agenta i wysyłać je do Azure monitor.