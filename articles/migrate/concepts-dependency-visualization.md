---
title: Analiza zależności w ocenie serwera Azure Migrate
description: Opisuje sposób używania analizy zależności do oceny przy użyciu Azure Migrate oceny serwera.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024765"
---
# <a name="dependency-analysis"></a>Analiza zależności

W tym artykule opisano analizę zależności w Azure Migrate: Ocena serwera.

## <a name="overview"></a>Omówienie

Analiza zależności pomaga identyfikować zależności między maszynami lokalnymi, które mają zostać poddane ocenie i zmigrować na platformę Azure. 

- W Azure Migrate: Ocena serwera, zbierasz maszyny w grupie, a następnie oceniasz grupę. Analiza zależności ułatwia precyzyjne grupowanie maszyn z wysokim poziomem zaufania do oceny.
- Analiza zależności pozwala identyfikować maszyny, które muszą zostać zmigrowane wspólnie. Możesz określić, czy maszyny są używane, czy mogą zostać zlikwidowane zamiast migrować.
- Analizowanie zależności pomaga upewnić się, że nic nie zostało pozostawione, i uniknąć awarii podczas migracji.
- Analiza jest szczególnie przydatna, jeśli nie masz pewności, czy maszyny są częścią wdrożenia aplikacji, które chcesz zmigrować na platformę Azure.
- [Zapoznaj](common-questions-discovery-assessment.md#what-is-dependency-visualization) się z typowymi pytaniami dotyczącymi analizy zależności.

Istnieją dwie opcje wdrażania analizy zależności

- **Oparta na agentach**: analiza zależności oparta na agentach wymaga zainstalowania agentów na każdej maszynie lokalnej, która ma zostać poddana analizie.
- Bez **agenta**: dzięki analizie bez agentów nie trzeba instalować agentów na maszynach, które mają być sprawdzane krzyżowo. Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware.

> [!NOTE]
> Analiza zależności oparta na agentach nie jest dostępna w Azure Government. Można użyć analizy zależności bez agenta.

## <a name="agentless-analysis"></a>Analiza bez agentów

Analiza zależności bez agenta działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona. Na maszynach, które mają zostać poddane analizie, nie zainstalowano żadnych agentów.

### <a name="collected-data"></a>Zebrane dane

Po rozpoczęciu odnajdywania zależności urządzenie sonduje dane z maszyn co pięć minut, aby zebrać dane. Te dane są zbierane z maszyn wirtualnych gościa za pośrednictwem vCenter Server przy użyciu interfejsów API vSphere. Zebrane dane są przetwarzane na urządzeniu Azure Migrate, do wywnioskowania informacji o tożsamości i są wysyłane do Azure Migrate co sześć godzin.

Sondowanie zbiera te dane z maszyn: 
- Nazwa procesów z aktywnymi połączeniami.
- Nazwa aplikacji, która uruchamia procesy z aktywnymi połączeniami.
- Port docelowy dla aktywnych połączeń.

## <a name="agent-based-analysis"></a>Analiza oparta na agentach

W przypadku analiz opartych na agentach Ocena serwera używa [rozwiązania Service map](../azure-monitor/insights/service-map.md) w Azure monitor do włączania wizualizacji i analizy zależności. [Agent Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) i [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent)programu muszą być zainstalowane na każdej maszynie, która ma zostać przeanalizowana.

### <a name="collected-data"></a>Zebrane dane

W przypadku analiz opartych na agentach zbierane są następujące dane:

- Nazwa serwera źródłowego, proces, nazwa aplikacji.
- Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.
- Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Porównanie bez agentów i opartych na agentach

Różnice między wizualizacją bez agenta i wizualizacją opartą na agentach zostały podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
Pomoc techniczna | Ta opcja jest obecnie w wersji zapoznawczej i jest dostępna tylko dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) obsługiwane systemy operacyjne. | Ogólnie dostępna.
Agent | Nie trzeba instalować agentów na maszynach, które mają być sprawdzane krzyżowo. | Agenci do zainstalowania na każdej maszynie lokalnej, którą chcesz analizować: [program Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)i [Agent zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Niewymagane. | Azure Migrate używa rozwiązania [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) na potrzeby analizy zależności. 
Jak to działa | Przechwytuje dane połączenia TCP na maszynach z włączoną funkcją wizualizacji zależności. Po odnajdywaniu dane są zbierane w odstępach pięciu minut. | Service Map agenci zainstalowani na komputerze zbierają dane dotyczące procesów TCP oraz połączeń przychodzących/wychodzących dla każdego procesu.
Dane | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port. | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 
Wizualizacja | Mapę zależności pojedynczego serwera można wyświetlać w czasie trwania z przedziału od godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapę można wyświetlać tylko w ciągu godziny.<br/><br/> Mapa zależności grupy serwerów.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksportowanie danych | Nie można obecnie pobrać w formacie tabelarycznym. | Dane można badać przy użyciu Log Analytics.



## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z wymaganiami dotyczącymi konfigurowania analiz opartych na agentach dla [maszyn wirtualnych VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [serwerów fizycznych](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)i [maszyn wirtualnych funkcji Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Zapoznaj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) się z wymaganiami dotyczącymi analizy bez agentów maszyn wirtualnych VMware.
- [Konfigurowanie](how-to-create-group-machine-dependencies.md) wizualizacji zależności opartej na agentach
- [Wypróbuj](how-to-create-group-machine-dependencies-agentless.md) wizualizację zależności bez agenta dla maszyn wirtualnych VMware.
- Przejrzyj [typowe pytania](common-questions-discovery-assessment.md#what-is-dependency-visualization) dotyczące wizualizacji zależności.


