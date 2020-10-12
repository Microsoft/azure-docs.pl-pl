---
title: Monitorowanie poziomu platformy Azure Service Fabric
description: Informacje na temat zdarzeń na poziomie platformy i dzienników używanych do monitorowania i diagnozowania klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 033b4967d3da382057c2651457f7792e760d8bc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247619"
---
# <a name="monitoring-the-cluster"></a>Monitorowanie klastra

Należy monitorować na poziomie klastra, aby określić, czy sprzęt i klaster działają zgodnie z oczekiwaniami. Mimo że Service Fabric mogą utrzymywać aplikacje działające w trakcie awarii sprzętu, ale nadal trzeba zdiagnozować, czy wystąpił błąd w aplikacji lub w podstawowej infrastrukturze. Należy również monitorować klastry, aby lepiej zaplanować pojemność, pomagając w podejmowaniu decyzji o dodawaniu lub usuwaniu sprzętu.

Service Fabric uwidacznia kilka zdarzeń platformy strukturalnej, jako [zdarzenia Service Fabric](service-fabric-diagnostics-events.md), za pomocą EventStore i różnych kanałów dzienników. 

W systemie Windows zdarzenia Service Fabric są dostępne dla jednego dostawcy ETW z zestawem przydatnym `logLevelKeywordFilters` do wybierania kanałów obsługi komunikatów operacyjnych i danych & — jest to sposób, w jaki oddzielamy wychodzące zdarzenia Service Fabric do filtrowania według potrzeb.

* **Działa** Operacje wysokiego poziomu wykonywane przez Service Fabric i klaster, w tym zdarzenia dotyczące węzła, nowej wdrażanej aplikacji lub wycofywania uaktualnienia itp. Zapoznaj się z pełną listą [zdarzeń.](service-fabric-diagnostics-event-generation-operational.md)  

* **Szczegóły operacyjne**  
Raporty kondycji i decyzje dotyczące równoważenia obciążenia.

Dostęp do kanału operacji można uzyskać za pomocą różnych sposobów, w tym dzienników zdarzeń ETW/Windows, [EventStore](service-fabric-diagnostics-eventstore.md) (dostępne w systemie Windows w wersji 6,2 i nowszych dla klastrów systemu Windows). EventStore zapewnia dostęp do zdarzeń klastra dla poszczególnych jednostek (w tym klastrów, węzłów, aplikacji, usług, partycji, replik i kontenerów) i udostępnia je za pośrednictwem interfejsów API REST i biblioteki klienta Service Fabric. Użyj EventStore do monitorowania klastrów deweloperskich i testowych oraz do uzyskiwania informacji o stanie klastrów produkcyjnych w danym momencie.

* **Komunikaty & danych**  
Krytyczne dzienniki i zdarzenia generowane w wiadomości (obecnie tylko elementu reverseproxy) i ścieżka danych (modele niezawodnych usług).

* **Dane & Messaging — szczegóły**  
Pełny kanał, który zawiera wszystkie niekrytyczne dzienniki z danych i komunikatów w klastrze (ten kanał ma bardzo duże ilości zdarzeń).

Oprócz tych, dostępne są dwa uporządkowane kanały EventSource, a także dzienniki zbierane do celów pomocy technicznej.

* [Zdarzenia interfejsu Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Zdarzenia specyficzne dla modelu programowania.

* [Zdarzenia interfejsu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Zdarzenia specyficzne dla modelu programowania i liczniki wydajności.

* Dzienniki pomocy technicznej  
Dzienniki systemu generowane przez Service Fabric mogą być używane przez nas tylko podczas świadczenia pomocy technicznej.

Te różne kanały obejmują większość zalecanych dzienników poziomu platformy. W celu usprawnienia rejestrowania na poziomie platformy należy rozważyć zainwestowanie w lepszy sposób zrozumieć model kondycji i dodanie niestandardowych raportów o kondycji oraz dodanie niestandardowych **liczników wydajności** w celu utworzenia zrozumienia wpływu usług i aplikacji w klastrze w czasie rzeczywistym.

Aby móc korzystać z tych dzienników, zdecydowanie zaleca się pozostawienie "diagnostyki" włączonej podczas tworzenia klastra w witrynie Azure Portal. Po włączeniu diagnostyki, gdy klaster jest wdrożony, system Windows Diagnostyka Azure jest w stanie potwierdzić kanały operacyjne, Reliable Services i niezawodne aktory, a następnie przechowywać dane w sposób dokładniejszy dla [agregacji zdarzeń za pomocą Diagnostyka Azure](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Raportowanie kondycji i obciążenia usługi Azure Service Fabric

Service Fabric ma własny model kondycji, który został szczegółowo opisany w następujących artykułach:

- [Wprowadzenie do monitorowania kondycji usługi Service Fabric](service-fabric-health-introduction.md)
- [Tworzenie raportów i sprawdzanie kondycji usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Dodawanie niestandardowych raportów o kondycji Service Fabric](service-fabric-report-health.md)
- [Wyświetlanie raportów o kondycji Service Fabric](service-fabric-view-entities-aggregated-health.md)

Monitorowanie kondycji ma kluczowe znaczenie dla wielu aspektów działania usługi, szczególnie podczas uaktualniania aplikacji. Po uaktualnieniu każdej domeny uaktualnienia usługi domena uaktualnienia musi przekazywać kontrolę kondycji, zanim wdrożenie przejdzie do następnej domeny uaktualnienia. Jeśli kondycja nie zostanie osiągnięta, wdrożenie zostanie wycofane, aby aplikacja pozostawała w znanym stanie prawidłowym. Mimo że niektórzy klienci mogą mieć problemy, zanim usługi zostaną wycofane, większość klientów nie napotyka problemu. Ponadto rozwiązanie jest wykonywane stosunkowo szybko bez konieczności oczekiwania na akcję od operatora ludzkiego. Im więcej kontroli kondycji, które są włączone w kodzie, tym bardziej odporne na problemy związane z wdrażaniem usługi.

Innym aspektem kondycji usługi jest raportowanie metryk z usługi. Metryki są ważne w Service Fabric, ponieważ służą do zrównoważenia użycia zasobów. Metryki mogą również stanowić wskaźnik kondycji systemu. Przykładowo może istnieć aplikacja, która ma wiele usług, a każde wystąpienie raportuje metrykę żądań na sekundę (RPS pliku). Jeśli jedna usługa korzysta z większej liczby zasobów niż inna usługa, Service Fabric przenosi wystąpienia usługi wokół klastra, aby spróbować zachować nawet wykorzystanie zasobów. Bardziej szczegółowy opis sposobu działania wykorzystania zasobów znajduje się [w temacie Zarządzanie użyciem zasobów i ładowaniem Service Fabric za pomocą metryk](service-fabric-cluster-resource-manager-metrics.md).

Metryki mogą również pomóc w przeprowadzeniu wglądu w sposób działania usługi. W miarę upływu czasu można użyć metryk, aby sprawdzić, czy usługa działa w oczekiwanych parametrach. Jeśli na przykład trendy pokazują, że w dniu 9 w poniedziałek rano RPS pliku jest 1 000, możesz skonfigurować raport kondycji z alertami, jeśli RPS pliku jest niższy niż 500 lub wyższy 1 500. Wszystko może być doskonale precyzyjne, ale warto się upewnić, że klienci mają doskonałe doświadczenie. Usługa może zdefiniować zestaw metryk, które mogą być zgłaszane do celów kontroli kondycji, ale nie ma wpływu na równoważenie zasobów klastra. W tym celu ustaw wagę metryki na zero. Zalecamy rozpoczęcie wszystkich metryk o wadze zero i nie zwiększenie wagi, dopóki nie masz pewności, że zrozumiesz, jak waga metryk wpływa na równoważenie zasobów dla klastra.

> [!TIP]
> Nie używaj zbyt wielu metryk ważonych. Może być trudne, aby zrozumieć, dlaczego wystąpienia usługi są przenoszone do równoważenia. Niektóre metryki mogą być czasochłonne.

Wszelkie informacje, które mogą wskazywać na kondycję i wydajność aplikacji, to kandydat dla metryk i raportów kondycji. **Licznik wydajności procesora CPU może określić, w jaki sposób jest używany dany węzeł, ale nie informuje o tym, czy dana usługa jest w dobrej kondycji, ponieważ wiele usług może być uruchomionych w jednym węźle.** Jednak metryki takie jak RPS pliku, elementy przetworzone i opóźnienie żądania mogą wskazywać prawidłowość określonej usługi.

## <a name="service-fabric-support-logs"></a>Dzienniki pomocy technicznej Service Fabric

Jeśli musisz skontaktować się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy dotyczącej klastra usługi Azure Service Fabric, dzienniki pomocy technicznej są prawie zawsze wymagane. Jeśli klaster jest hostowany na platformie Azure, dzienniki pomocy technicznej są automatycznie konfigurowane i zbierane w ramach tworzenia klastra. Dzienniki są przechowywane na dedykowanym koncie magazynu w grupie zasobów klastra. Konto magazynu nie ma ustalonej nazwy, ale na koncie są wyświetlane kontenery obiektów blob i tabele o nazwach rozpoczynających się od *sieci szkieletowej*. Aby uzyskać informacje na temat konfigurowania kolekcji dzienników dla klastra autonomicznego, zobacz [Tworzenie autonomicznego klastra Service Fabric platformy Azure](service-fabric-cluster-creation-for-windows-server.md) i [ustawień konfiguracji dla autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md). W przypadku autonomicznych wystąpień Service Fabric dzienniki powinny być wysyłane do lokalnego udziału plików. Te dzienniki są **wymagane** do obsługi, ale nie są przeznaczone do użytku przez żadną osobę spoza zespołu pomocy technicznej firmy Microsoft.

## <a name="measuring-performance"></a>Mierzenie wydajności

Pomiar wydajności klastra pomoże zrozumieć, jak można obsłużyć obciążenia i rozwiązywać decyzje dotyczące skalowania klastra (Zobacz więcej na temat skalowania klastra [na platformie Azure](service-fabric-cluster-scale-in-out.md)lub [lokalnego](service-fabric-cluster-windows-server-add-remove-nodes.md)). Dane dotyczące wydajności są również przydatne w porównaniu z akcjami, które mogły zostać wykonane przez użytkownika lub aplikacje i usługi, podczas analizowania dzienników w przyszłości. 

Aby uzyskać listę liczników wydajności zbieranych podczas korzystania z Service Fabric, zobacz [liczniki wydajności w Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Poniżej przedstawiono dwa typowe sposoby konfigurowania zbierania danych wydajności dla klastra:

* **Korzystanie z agenta**  
Jest to preferowany sposób zbierania wydajności z komputera, ponieważ agenci zazwyczaj mają listę możliwych metryk wydajności, które mogą być zbierane, i jest stosunkowo prostym procesem do wybierania metryk, które mają być zbierane lub zmieniane. Zapoznaj się z informacjami o Azure Monitor oferowaniu Azure Monitor dzienników w Service Fabric Azure Monitor zapoznaj się z [integracją](service-fabric-diagnostics-event-analysis-oms.md) i [skonfigurować agenta log Analytics](../azure-monitor/platform/agent-windows.md) , aby dowiedzieć się więcej o agencie log Analytics, który jest jednym z agentów monitorujących, który może pobrać dane wydajności dla maszyn wirtualnych klastra i wdrożonych kontenerów.

* **Liczniki wydajności do Table Storage platformy Azure**  
Metryki wydajności można także wysyłać do tego samego magazynu tabel co zdarzenia. Wymaga to zmiany konfiguracji Diagnostyka Azure w celu pobrania odpowiednich liczników wydajności z maszyn wirtualnych w klastrze i włączenia statystyk platformy Docker, jeśli zostaną wdrożone dowolne kontenery. Przeczytaj informacje na temat konfigurowania [liczników wydajności w programie funkcji wad](service-fabric-diagnostics-event-aggregation-wad.md) w Service Fabric, aby skonfigurować zbieranie danych licznika wydajności.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj informacje o [integracji dzienników Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) Service Fabric w celu zbierania danych diagnostycznych klastra i tworzenia niestandardowych zapytań i alertów
* Dowiedz się więcej na temat Service Fabric wbudowanych funkcji diagnostycznych [EventStore](service-fabric-diagnostics-eventstore.md)
* Zapoznaj się z [typowymi scenariuszami diagnostycznymi](service-fabric-diagnostics-common-scenarios.md) w Service Fabric
