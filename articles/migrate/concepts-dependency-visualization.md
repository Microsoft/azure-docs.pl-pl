---
title: Analiza zależności w ocenie serwera migracji platformy Azure
description: W tym artykule opisano sposób używania analizy zależności do oceny przy użyciu oceny serwera migracji platformy Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024765"
---
# <a name="dependency-analysis"></a>Analiza zależności

W tym artykule opisano analizę zależności w usłudze Azure Migrate:Server Assessment.

## <a name="overview"></a>Omówienie

Analiza zależności ułatwia identyfikowanie zależności między komputerami lokalnymi, które chcesz ocenić i przeprowadzić migrację na platformę Azure. 

- W usłudze Azure Migrate:Server Assessment zbierasz maszyny do grupy, a następnie oceniasz grupę. Analiza zależności ułatwia dokładniejszą grupowanie maszyn, z dużą pewnością do oceny.
- Analiza zależności umożliwia identyfikowanie maszyn, które muszą być migrowane razem. Można określić, czy maszyny są używane lub czy można je wycofać zamiast migrować.
- Analizowanie zależności pomaga upewnić się, że nic nie pozostaje w tyle i uniknąć niespodziewanych awarii podczas migracji.
- Analiza jest szczególnie przydatna, jeśli nie masz pewności, czy maszyny są częścią wdrożenia aplikacji, które chcesz przeprowadzić migrację na platformę Azure.
- [Przejrzyj](common-questions-discovery-assessment.md#what-is-dependency-visualization) typowe pytania dotyczące analizy zależności.

Istnieją dwie opcje wdrażania analizy zależności

- **Oparta na agentach:** Analiza zależności oparta na agentach wymaga zainstalowania agentów na każdym komputerze lokalnym, który chcesz analizować.
- **Bez agenta:** Dzięki analizie bez agenta nie trzeba instalować agentów na komputerach, które chcesz sprawdzić krzyżowo. Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware.

> [!NOTE]
> Analiza zależności oparta na agentach nie jest dostępna w usłudze Azure Government. Można użyć analizy zależności bez agenta.

## <a name="agentless-analysis"></a>Analiza bez agenta

Analiza zależności bez agenta działa poprzez przechwytywanie danych połączenia TCP z komputerów, dla których jest włączona. Na komputerach, które chcesz analizować, nie są instalowani żadne agenci.

### <a name="collected-data"></a>Zebrane dane

Po uruchomieniu odnajdowania zależności urządzenie sonduje dane z komputerów co pięć minut w celu zebrania danych. Te dane są zbierane z maszyn wirtualnych gości za pośrednictwem serwera vCenter, przy użyciu interfejsów API vSphere. Zebrane dane są przetwarzane na urządzeniu migracji platformy Azure, aby wywnić informacje o tożsamości i są wysyłane do usługi Azure Migrate co sześć godzin.

Sondowanie zbiera te dane z maszyn: 
- Nazwa procesów, które mają aktywne połączenia.
- Nazwa aplikacji, która uruchamia procesy, które mają aktywne połączenia.
- Port docelowy na aktywnych połączeniach.

## <a name="agent-based-analysis"></a>Analiza oparta na agentach

W przypadku analizy opartej na agentach ocena serwera używa [rozwiązania mapy usług](../azure-monitor/insights/service-map.md) w usłudze Azure Monitor, aby włączyć wizualizację i analizę zależności. [Agent monitorowania/analizy dzienników](../azure-monitor/platform/agents-overview.md#log-analytics-agent) firmy Microsoft i [agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent)muszą być zainstalowane na każdym komputerze, który chcesz analizować.

### <a name="collected-data"></a>Zebrane dane

W przypadku analizy opartej na agentach zbierane są następujące dane:

- Nazwa serwera komputera źródłowego, proces, nazwa aplikacji.
- Nazwa docelowego serwera komputera, proces, nazwa aplikacji i port.
- Liczba połączeń, opóźnienia i informacje o transferze danych są zbierane i dostępne dla zapytań usługi Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Porównanie agentów i agentów

Różnice między wizualizacją bez agenta a wizualizacją opartą na agentach są podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Oparte na agentach**
--- | --- | ---
Pomoc techniczna | Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) obsługiwane systemy operacyjne. | Ogólnie rzecz biorąc dostępność (GA).
Agent | Nie ma potrzeby instalowania agentów na komputerach, które chcesz sprawdzić krzyżowo. | Agenci, którzy mają być zainstalowani na każdym komputerze lokalnym, który chcesz przeanalizować: [Agent monitorowania firmy Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)i agent [zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Niewymagane. | Usługa Azure Migrate używa rozwiązania [mapy usług](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) do analizy zależności. 
Jak to działa | Przechwytuje dane połączenia TCP na komputerach włączonych do wizualizacji zależności. Po odnajdowaniu zbiera dane w odstępach pięciu minut. | Agenci mapy usług zainstalowane na komputerze zbierają dane o procesach TCP i połączeniach przychodzących/wychodzących dla każdego procesu.
Dane | Nazwa serwera komputera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa docelowego serwera komputera, proces, nazwa aplikacji i port. | Nazwa serwera komputera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa docelowego serwera komputera, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnienia i informacje o transferze danych są zbierane i dostępne dla zapytań usługi Log Analytics. 
Wizualizacja | Mapa zależności pojedynczego serwera może być oglądana przez okres od jednej godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapę można oglądać tylko przez godzinę.<br/><br/> Mapa zależności grupy serwerów.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksportowanie danych | Obecnie nie można pobrać w formacie tabelarycznym. | Dane można wyszukiwać za pomocą usługi Log Analytics.



## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z wymaganiami dotyczącymi konfigurowania analizy opartej na agentach dla [maszyn wirtualnych VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [serwerów fizycznych](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)i maszyn [wirtualnych z programem Hyper-V.](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) wymagania dotyczące analizy maszyn wirtualnych VMware bez agenta.
- [Konfigurowanie](how-to-create-group-machine-dependencies.md) wizualizacji zależności opartej na agentach
- [Wypróbuj](how-to-create-group-machine-dependencies-agentless.md) wizualizację zależności bez agenta dla maszyn wirtualnych VMware.
- Przejrzyj [typowe pytania](common-questions-discovery-assessment.md#what-is-dependency-visualization) dotyczące wizualizacji zależności.


