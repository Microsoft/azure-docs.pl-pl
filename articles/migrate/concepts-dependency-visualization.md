---
title: Analiza zależności w ocenie serwera Azure Migrate
description: Opisuje sposób używania analizy zależności do oceny przy użyciu Azure Migrate oceny serwera.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: 1f198d47191e7893e74b072ae8fd10546e3a6ee7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752213"
---
# <a name="dependency-analysis"></a>Analiza zależności

W tym artykule opisano analizę zależności w Azure Migrate: Ocena serwera.


Analiza zależności identyfikuje zależności między wykrytymi maszynami lokalnymi. Zapewnia następujące korzyści: 

- Można zbierać maszyny do grup w celu oceny, dokładniej, z większą pewnością.
- Można zidentyfikować komputery, które muszą zostać zmigrowane wspólnie. Jest to szczególnie przydatne, jeśli nie masz pewności, które komputery są częścią wdrożenia aplikacji, które chcesz zmigrować na platformę Azure.
- Możesz określić, czy maszyny są używane, a które maszyny mogą zostać zlikwidowane zamiast migrować.
- Analizowanie zależności pomaga upewnić się, że nic nie zostanie pozostawione i w ten sposób unika awarii po migracji.
- [Zapoznaj](common-questions-discovery-assessment.md#what-is-dependency-visualization) się z typowymi pytaniami dotyczącymi analizy zależności.


## <a name="analysis-types"></a>Typy analiz

Istnieją dwie opcje wdrażania analizy zależności

**Opcja** | **Szczegóły** | **Chmura publiczna** | **Azure Government**
----  |---- | ---- 
**Bez agenta** | Sonduje dane z maszyn wirtualnych VMware przy użyciu interfejsów API vSphere.<br/><br/> Nie musisz instalować agentów na maszynach wirtualnych.<br/><br/> Ta opcja jest obecnie dostępna w wersji zapoznawczej tylko w przypadku maszyn wirtualnych VMware. | Obsługiwane. | Obsługiwane.
**Analiza oparta na agentach** | Używa [rozwiązania Service map](../azure-monitor/insights/service-map.md) w Azure monitor, aby włączyć wizualizację zależności i analizę.<br/><br/> Należy zainstalować agentów na poszczególnych maszynach lokalnych, które mają być analizowane. | Obsługiwane | Nieobsługiwane.


## <a name="agentless-analysis"></a>Analiza bez agentów

Analiza zależności bez agenta działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona. Nie zainstalowano żadnych agentów na maszynach wirtualnych. Połączenia z tym samym serwerem źródłowym i procesem oraz serwer docelowy, proces i port są grupowane logicznie w zależności. Przechwycone dane zależności można wizualizować w widoku mapy lub wyeksportować jako wolumin CSV. Na maszynach, które mają zostać poddane analizie, nie zainstalowano żadnych agentów.

### <a name="dependency-data"></a>Dane zależności

Po rozpoczęciu odnajdywania danych zależności rozpocznie się sondowanie:

- Urządzenie Azure Migrate sonduje dane połączenia TCP z maszyn co pięć minut w celu zebrania danych.
- Dane są zbierane z maszyn wirtualnych gościa za pośrednictwem vCenter Server przy użyciu interfejsów API vSphere.
- Sondowanie zbiera te dane:

    - Nazwa procesów z aktywnymi połączeniami.
    - Nazwa aplikacji, która uruchamia procesy z aktywnymi połączeniami.
    - Port docelowy dla aktywnych połączeń.

- Zebrane dane są przetwarzane na urządzeniu Azure Migrate, do wywnioskowania informacji o tożsamości i są wysyłane do Azure Migrate co sześć godzin


## <a name="agent-based-analysis"></a>Analiza oparta na agentach

W przypadku analiz opartych na agentach Ocena serwera używa rozwiązania [Service map](../azure-monitor/insights/service-map.md) w Azure monitor. Na każdym komputerze, który ma zostać przeanalizowany, jest instalowany [agent Microsoft Monitoring Agent/log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) i [Agent zależności](../azure-monitor/platform/agents-overview.md#dependency-agent).

### <a name="dependency-data"></a>Dane zależności

Analiza oparta na agentach udostępnia następujące dane:

- Nazwa serwera źródłowego, proces, nazwa aplikacji.
- Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.
- Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 



## <a name="compare-agentless-and-agent-based"></a>Porównanie bez agentów i opartych na agentach

Różnice między wizualizacją bez agenta i wizualizacją opartą na agentach zostały podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
**Pomoc techniczna** | Tylko w wersji zapoznawczej dla maszyn wirtualnych VMware. [Przejrzyj](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) obsługiwane systemy operacyjne. | Ogólnie dostępna.
**Agent** | Na maszynach, które mają być analizowane, nie są wymagane żadne agenci. | Agenci zobowiązani na poszczególnych maszynach lokalnych, które mają zostać poddane analizie.
**Log Analytics** | Niewymagane. | Azure Migrate używa rozwiązania [Service map](../azure-monitor/insights/service-map.md) w [dziennikach Azure monitor](../azure-monitor/log-query/log-query-overview.md) na potrzeby analizy zależności.<br/><br/> Możesz skojarzyć obszar roboczy Log Analytics z projektem Azure Migrate. Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).
**Proces** | Przechwytuje dane połączenia TCP. Po odnajdywaniu dane są zbierane w odstępach pięciu minut. | Service Map agenci zainstalowani na komputerze zbierają dane dotyczące procesów TCP oraz połączeń przychodzących/wychodzących dla każdego procesu.
**Dane** | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port. | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera maszyny docelowej, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 
**Wizualizacja** | Mapę zależności pojedynczego serwera można wyświetlać w czasie trwania z przedziału od godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapa zależności grupy serwerów.<br/><br/>  Mapę można wyświetlać tylko w ciągu godziny.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksportowanie danych | Dane z ostatnich 30 dni można pobrać w formacie CSV. | Dane można badać przy użyciu Log Analytics.



## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie](how-to-create-group-machine-dependencies.md) wizualizacji zależności opartej na agentach.
- [Wypróbuj](how-to-create-group-machine-dependencies-agentless.md) wizualizację zależności bez agenta dla maszyn wirtualnych VMware.
- Przejrzyj [typowe pytania](common-questions-discovery-assessment.md#what-is-dependency-visualization) dotyczące wizualizacji zależności.
