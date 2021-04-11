---
title: Analiza zależności w Azure Migrate odnajdywania i oceny
description: Opisuje sposób używania analizy zależności do oceny przy użyciu Azure Migrate odnajdywania i oceny.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778393"
---
# <a name="dependency-analysis"></a>Analiza zależności

W tym artykule opisano analizę zależności w Azure Migrate: odnajdywanie i ocenianie.

Analiza zależności identyfikuje zależności między odnalezionymi serwerami lokalnymi. Zapewnia następujące korzyści:

- Serwery można gromadzić w grupach w celu oceny, dokładniej, z większą pewnością.
- Można zidentyfikować serwery, które muszą być migrowane ze sobą. Jest to szczególnie przydatne, jeśli nie masz pewności, które serwery są częścią wdrożenia aplikacji, które chcesz zmigrować na platformę Azure.
- Możesz określić, czy serwery są używane, a które serwery mogą zostać zlikwidowane zamiast migrować.
- Analizowanie zależności pomaga upewnić się, że nic nie zostanie pozostawione i w ten sposób unika awarii po migracji.
- [Zapoznaj](common-questions-discovery-assessment.md#what-is-dependency-visualization) się z typowymi pytaniami dotyczącymi analizy zależności.

## <a name="analysis-types"></a>Typy analiz

Istnieją dwie opcje wdrażania analizy zależności

**Opcja** | **Szczegóły** | **Chmura publiczna** | **Azure Government**
----  |---- | ----
**Bez agenta** | Sonduje dane z serwerów w oprogramowaniu VMware przy użyciu interfejsów API vSphere.<br/><br/> Nie musisz instalować agentów na serwerach.<br/><br/> Ta opcja jest obecnie dostępna w wersji zapoznawczej tylko w przypadku serwerów w oprogramowaniu VMware. | Obsługiwane. | Obsługiwane.
**Analiza oparta na agentach** | Używa [rozwiązania Service map](../azure-monitor/vm/service-map.md) w Azure monitor, aby włączyć wizualizację zależności i analizę.<br/><br/> Należy zainstalować agentów na każdym serwerze lokalnym, który ma zostać przeanalizowany. | Obsługiwane | Nieobsługiwane.

## <a name="agentless-analysis"></a>Analiza bez agentów

Analiza zależności bez agenta działa przez przechwytywanie danych połączenia TCP z serwerów, dla których jest włączona. Na serwerach nie zainstalowano żadnych agentów. Połączenia z tym samym serwerem źródłowym i procesem oraz serwer docelowy, proces i port są grupowane logicznie w zależności. Przechwycone dane zależności można wizualizować w widoku mapy lub wyeksportować jako wolumin CSV. Na serwerach, które mają zostać poddane analizie, nie zainstalowano żadnych agentów.

### <a name="dependency-data"></a>Dane zależności

Po rozpoczęciu odnajdywania danych zależności rozpocznie się sondowanie:

- Urządzenie Azure Migrate sonduje dane połączenia TCP z serwerów co pięć minut w celu zebrania danych.
- Dane są zbierane z serwerów Gości za pośrednictwem vCenter Server przy użyciu interfejsów API vSphere.
- Sondowanie zbiera te dane:

    - Nazwa procesów z aktywnymi połączeniami.
    - Nazwa aplikacji, która uruchamia procesy z aktywnymi połączeniami.
    - Port docelowy dla aktywnych połączeń.

- Zebrane dane są przetwarzane na urządzeniu Azure Migrate, do wywnioskowania informacji o tożsamości i są wysyłane do Azure Migrate co sześć godzin


## <a name="agent-based-analysis"></a>Analiza oparta na agentach

W przypadku analizy opartej na agentach Azure Migrate: odnajdywanie i ocenianie korzysta z rozwiązania [Service map](../azure-monitor/vm/service-map.md) w Azure monitor. Na każdym serwerze, który ma zostać przeanalizowany, należy zainstalować [agenta Microsoft Monitoring Agent/log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent) i [agenta zależności](../azure-monitor/agents/agents-overview.md#dependency-agent).

### <a name="dependency-data"></a>Dane zależności

Analiza oparta na agentach udostępnia następujące dane:

- Nazwa serwera źródłowego, proces, nazwa aplikacji.
- Nazwa serwera docelowego, proces, nazwa aplikacji i port.
- Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics.

## <a name="compare-agentless-and-agent-based"></a>Porównanie bez agentów i opartych na agentach

Różnice między wizualizacją bez agenta i wizualizacją opartą na agentach zostały podsumowane w tabeli.

**Wymaganie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
**Pomoc techniczna** | W wersji zapoznawczej dla serwerów tylko w oprogramowaniu VMware. [Przejrzyj](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) obsługiwane systemy operacyjne. | Ogólnie dostępna.
**Odczynnik** | Na serwerach, które mają być analizowane, nie są wymagane żadne agenci. | Agenci zobowiązani na każdym serwerze lokalnym, który ma zostać poddany analizie.
**Log Analytics** | Niewymagane. | Azure Migrate używa rozwiązania [Service map](../azure-monitor/vm/service-map.md) w [dziennikach Azure monitor](../azure-monitor/logs/log-query-overview.md) na potrzeby analizy zależności.<br/><br/> Możesz skojarzyć obszar roboczy Log Analytics z projektem. Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Proces** | Przechwytuje dane połączenia TCP. Po odnajdywaniu dane są zbierane w odstępach pięciu minut. | Service Map agenci zainstalowani na serwerze zbierają dane dotyczące procesów TCP, a połączenia przychodzące/wychodzące dla każdego procesu.
**Dane** | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera docelowego, proces, nazwa aplikacji i port. | Nazwa serwera źródłowego, proces, nazwa aplikacji.<br/><br/> Nazwa serwera docelowego, proces, nazwa aplikacji i port.<br/><br/> Liczba połączeń, opóźnień i informacji o przesyłaniu danych jest zbieranych i dostępnych dla zapytań Log Analytics. 
**Wizualizacja** | Mapę zależności pojedynczego serwera można wyświetlać w czasie trwania z przedziału od godziny do 30 dni. | Mapa zależności pojedynczego serwera.<br/><br/> Mapa zależności grupy serwerów.<br/><br/>  Mapę można wyświetlać tylko w ciągu godziny.<br/><br/> Dodawanie i usuwanie serwerów w grupie z widoku mapy.
Eksport danych | Dane z ostatnich 30 dni można pobrać w formacie CSV. | Dane można badać przy użyciu Log Analytics.



## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie](how-to-create-group-machine-dependencies.md) wizualizacji zależności opartej na agentach.
- [Wypróbuj](how-to-create-group-machine-dependencies-agentless.md) wizualizację zależności bez agenta dla serwerów w oprogramowaniu VMware.
- Przejrzyj [typowe pytania](common-questions-discovery-assessment.md#what-is-dependency-visualization) dotyczące wizualizacji zależności.
