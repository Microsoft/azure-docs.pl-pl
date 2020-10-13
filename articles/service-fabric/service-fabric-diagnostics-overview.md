---
title: Omówienie monitorowania i diagnostyki usługi Azure Service Fabric
description: Dowiedz się więcej na temat monitorowania i diagnostyki dla klastrów, aplikacji i usług platformy Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 25a50a2841a03929804be45be8012f9b5d0457ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357135"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorowanie i Diagnostyka dla Service Fabric platformy Azure

Ten artykuł zawiera omówienie monitorowania i diagnostyki dla Service Fabric platformy Azure. Monitorowanie i Diagnostyka mają kluczowe znaczenie dla opracowywania, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Można na przykład śledzić sposób używania aplikacji, działania podejmowane przez platformę Service Fabric, wykorzystanie zasobów z licznikami wydajności oraz ogólną kondycję klastra. Te informacje służą do diagnozowania i rozwiązywania problemów i zapobiegania ich występowaniu w przyszłości. W następnych sekcjach zostaną krótko wyjaśnione poszczególne obszary monitorowania Service Fabric, które należy wziąć pod uwagę w przypadku obciążeń produkcyjnych. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorowanie aplikacji
Monitorowanie aplikacji śledzi sposób używania funkcji i składników aplikacji. Chcesz monitorować aplikacje, aby upewnić się, że wystąpiły problemy wpływające na użytkowników. Odpowiedzialność za monitorowanie aplikacji polega na tworzeniu aplikacji i jej usług, ponieważ jest ona unikatowa dla logiki biznesowej aplikacji. Monitorowanie aplikacji może być przydatne w następujących scenariuszach:
* Jak dużo ruchu występuje moja aplikacja? -Czy potrzebujesz skalować swoje usługi, aby spełniały wymagania użytkowników, lub rozwiązać potencjalne wąskie gardło w aplikacji?
* Czy moja usługa może pomyślnie i śledzić wywołania usługi?
* Jakie akcje są wykonywane przez użytkowników mojej aplikacji? -Zbieranie danych telemetrycznych może ułatwić rozwój funkcji i lepszą diagnostykę błędów aplikacji
* Czy moja aplikacja zgłasza Nieobsłużone wyjątki? 
* Co się dzieje w ramach usług uruchomionych w ramach moich kontenerów?

Zaletą monitorowania aplikacji jest to, że deweloperzy mogą korzystać z dowolnych narzędzi i struktur, które są takie same, jak w kontekście aplikacji. Aby dowiedzieć się więcej na temat rozwiązania platformy Azure do monitorowania aplikacji z Azure Monitor-Application Insights w [analizie zdarzeń przy użyciu Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Mamy również samouczek dotyczący sposobu [konfigurowania tego programu dla aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md). W tym samouczku przedstawiono sposób instalowania odpowiednich narzędzi, przykład zapisywania niestandardowych danych telemetrycznych w aplikacji oraz wyświetlania diagnostyki aplikacji i telemetrii w Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Monitorowanie platformy (klastra)
Użytkownik jest w stanie kontrolować, jakie dane telemetryczne pochodzą z aplikacji od momentu, gdy użytkownik zapisuje sam kod, ale co to jest Diagnostyka z Service Fabric platformy? Jednym z celów Service Fabric jest utrzymywanie aplikacji odpornych na awarie sprzętu. Ten cel jest realizowany przez funkcję usługi systemowej platformy do wykrywania problemów z infrastrukturą oraz szybkiego obciążenia pracą w trybie failover z innymi węzłami w klastrze. Ale w tym konkretnym przypadku, co w przypadku, gdy usługi systemowe mają problemy? Czy w przypadku próby wdrożenia lub przeniesienia obciążenia reguły dotyczące rozmieszczenia usług są naruszane? Service Fabric zapewnia diagnostykę tych i nie tylko, aby upewnić się, że masz informacje o działaniach w klastrze. Poniżej przedstawiono przykładowe scenariusze monitorowania klastra:

Service Fabric zapewnia kompleksowy zestaw zdarzeń z pola. Do tych [zdarzeń Service Fabric](service-fabric-diagnostics-events.md) można uzyskać dostęp za pośrednictwem EventStore lub kanału operacyjnego (kanał zdarzenia uwidoczniony przez platformę). 

* Service Fabric kanałów zdarzeń — w systemie Windows zdarzenia Service Fabric są dostępne dla jednego dostawcy ETW z zestawem przydatnym `logLevelKeywordFilters` do wybierania kanałów operacyjnych i danych & komunikatów — jest to sposób, w jaki oddzielamy wychodzące zdarzenia Service Fabric do filtrowania według potrzeb. W systemie Linux zdarzenia Service Fabric przechodzą przez LTTng i są umieszczane w jednej tabeli magazynu, skąd można je filtrować zgodnie z wymaganiami. Kanały te zawierają zanadzorowane, strukturalne zdarzenia, które mogą być używane do lepszego zrozumienia stanu klastra. Diagnostyka jest domyślnie włączona w czasie tworzenia klastra, co umożliwia utworzenie tabeli usługi Azure Storage, w której są wysyłane zdarzenia z tych kanałów, aby można było wysyłać zapytania w przyszłości. 

* EventStore — EventStore to funkcja oferowana przez platformę, która zapewnia Service Fabric zdarzenia platformy dostępne w Service Fabric Explorer i za pomocą interfejsu API REST. Możesz zobaczyć widok migawki tego, co się dzieje w klastrze dla każdej jednostki, na przykład węzeł, usługa, aplikacja i zapytanie na podstawie czasu zdarzenia. Więcej informacji na temat EventStore można także znaleźć na stronie [Przegląd EventStore](service-fabric-diagnostics-eventstore.md).    

![Zrzut ekranu przedstawia kartę zdarzenia w okienku węzły kilka zdarzeń, w tym zdarzenia NodeDown.](media/service-fabric-diagnostics-overview/eventstore.png)

Dostarczona Diagnostyka ma postać kompleksowego zestawu zdarzeń z pola. Te [zdarzenia Service Fabric](service-fabric-diagnostics-events.md) ilustrują akcje wykonywane przez platformę w różnych jednostkach, takich jak węzły, aplikacje, usługi, partycje itd. W poprzednim scenariuszu, jeśli węzeł został przeszedł w dół, platforma wyemituje zdarzenie, `NodeDown` a użytkownik może otrzymywać powiadomienia natychmiast za pomocą wybranego przez siebie narzędzia do monitorowania. Inne typowe przykłady obejmują `ApplicationUpgradeRollbackStarted` lub `PartitionReconfigured` podczas pracy w trybie failover. **Te same zdarzenia są dostępne zarówno w klastrach systemu Windows, jak i Linux.**

Zdarzenia są wysyłane za pomocą standardowych kanałów w systemach Windows i Linux i mogą być odczytywane przez dowolne narzędzie monitorujące, które je obsługuje. Azure Monitor rozwiązanie to Azure Monitor dzienników. Możesz dowiedzieć się więcej na temat [integracji dzienników Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) , która zawiera niestandardowy, operacyjny pulpit nawigacyjny dla klastra oraz kilka przykładowych zapytań, z których można tworzyć alerty. Dodatkowe koncepcje dotyczące monitorowania klastra są dostępne w [przypadku zdarzeń na poziomie platformy i generowania dzienników](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorowanie kondycji
Platforma Service Fabric obejmuje model kondycji, który zapewnia rozszerzone Raportowanie kondycji dla stanu jednostek w klastrze. Każdy węzeł, aplikacja, usługa, partycja, replika lub wystąpienie ma ciągle aktualizowalny stan kondycji. Stan kondycji może mieć wartość "OK", "ostrzeżenie" lub "błąd". Zastanów się Service Fabric zdarzeń jako czasowników wykonywanych przez klaster w różnych jednostkach i kondycji jako przymiotnik dla każdej jednostki. Za każdym razem, gdy kondycja poszczególnych jednostek zostanie przejdzie, zdarzenie zostanie również wyemitowane. W ten sposób można skonfigurować zapytania i alerty dla zdarzeń dotyczących kondycji w wybranym przez siebie narzędziu do monitorowania, podobnie jak każde inne zdarzenie. 

Ponadto pozwalamy użytkownikom na zastępowanie kondycji jednostek. Jeśli aplikacja przechodzi przez uaktualnienie, a testy weryfikacyjne kończą się niepowodzeniem, można napisać w celu Service Fabric kondycji przy użyciu interfejsu API kondycji, aby wskazać, że aplikacja nie jest już w dobrej kondycji, a Service Fabric automatycznie wycofywanie uaktualnienia. Aby uzyskać więcej informacji na temat modelu kondycji, zapoznaj [się z wprowadzeniem do Service Fabric monitorowania kondycji](service-fabric-health-introduction.md)

![Pulpit nawigacyjny kondycji SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Licznik alarmy
Ogólnie rzecz biorąc, licznik alarm jest oddzielną usługą, która może obserwować kondycję i obciążenie między usługami, punktami końcowymi ping i raportować kondycję dla wszystkich elementów w klastrze. Może to pomóc zapobiec błędom, które nie zostaną wykryte w oparciu o Widok jednej usługi. Alarmy są również dobrym miejscem do kodu hosta, który wykonuje akcje zaradcze bez interakcji z użytkownikiem (na przykład czyszczenie plików dziennika w magazynie w określonych przedziałach czasowych). Przykładową implementację usługi strażnika można znaleźć [tutaj](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorowanie infrastruktury (wydajności)
Teraz, gdy poznamy diagnostykę w aplikacji i na platformie, jak wiemy, że sprzęt działa zgodnie z oczekiwaniami? Monitorowanie podstawowej infrastruktury jest najważniejszym elementem do poznania stanu klastra i użycia zasobów. Pomiar wydajności systemu zależy od wielu czynników, które mogą być subiektywne w zależności od obciążeń. Te czynniki są zwykle mierzone przy użyciu liczników wydajności. Te liczniki wydajności mogą pochodzić z różnych źródeł, w tym systemu operacyjnego, programu .NET Framework lub platformy Service Fabric. Niektóre scenariusze, w których byłyby przydatne, są

* Czy mogę efektywnie wykorzystać mój sprzęt? Czy chcesz korzystać z sprzętu na 90% procesora CPU lub 10% procesora CPU. Jest to przydatne podczas skalowania klastra lub optymalizacji procesów aplikacji.
* Czy mogę aktywnie przewidzieć problemy związane z infrastrukturą? — wiele problemów jest poprzedzonych nagłymi zmianami (spadnie), dzięki czemu można używać liczników wydajności, takich jak we/wy sieci i użycie procesora CPU, aby przewidzieć i zdiagnozować problemy z wyprzedzeniem.

Listę liczników wydajności, które powinny być zbierane na poziomie infrastruktury, można znaleźć na stronie [metryki wydajności](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric udostępnia również zestaw liczników wydajności dla modeli programowania Reliable Services i aktorów. W przypadku korzystania z jednego z tych modeli te liczniki wydajności mogą mieć pewność, że Twoje aktory są odpowiednio i działają prawidłowo lub że niezawodne żądania obsługi są obsługiwane szybko. Aby uzyskać więcej informacji, zobacz [monitorowanie komunikacji zdalnej usługi niezawodnej](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) i [monitorowanie wydajności dla Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Azure Monitor rozwiązanie do zbierania tych danych jest Azure Monitor dzienników, podobnie jak monitorowanie poziomu platformy. Należy użyć [agenta log Analytics](service-fabric-diagnostics-oms-agent.md) , aby zebrać odpowiednie liczniki wydajności i wyświetlić je w dziennikach Azure monitor.

## <a name="recommended-setup"></a>Zalecana konfiguracja
Teraz, gdy przejdziesz do każdego obszaru monitorowania i przykładowych scenariuszy, poniżej przedstawiono podsumowanie narzędzi monitorowania platformy Azure i konfiguracji wymaganych do monitorowania wszystkich powyższych obszarów. 

* Monitorowanie aplikacji za pomocą [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorowanie klastra przy użyciu [agenta diagnostyki](service-fabric-diagnostics-event-aggregation-wad.md) i [dzienników Azure monitor](service-fabric-diagnostics-oms-setup.md)
* Monitorowanie infrastruktury przy użyciu [dzienników Azure monitor](service-fabric-diagnostics-oms-agent.md)

Możesz również użyć i zmodyfikować przykładowy szablon ARM znajdujący się [tutaj](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) , aby zautomatyzować wdrażanie wszystkich wymaganych zasobów i agentów. 

## <a name="other-logging-solutions"></a>Inne rozwiązania rejestrowania

Chociaż te dwa zalecane rozwiązania, [Azure monitor dzienniki](service-fabric-diagnostics-event-analysis-oms.md) i [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zostały wbudowane w integrację z Service Fabric, wiele zdarzeń jest zapisywana przez dostawców ETW i rozszerzalne z innymi rozwiązaniami rejestrowania. Należy również zapoznać się ze [stosem elastycznym](https://www.elastic.co/products) (szczególnie w przypadku, gdy rozważasz uruchamianie klastra w środowisku offline), [dynaTrace](https://www.dynatrace.com/)lub dowolną inną platformę z preferencjami. Mamy [tutaj](service-fabric-diagnostics-partners.md)dostęp do listy zintegrowanych partnerów.

Kluczowe punkty dla każdej wybranej platformy powinny zawierać informacje o tym, jak najwygodniejszy jest interfejs użytkownika, możliwości zapytań, dostępne niestandardowe wizualizacje i pulpity nawigacyjne oraz dodatkowe narzędzia, które zapewniają, aby usprawnić środowisko monitorowania. 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat Instrumentacji aplikacji, zobacz [zdarzenia na poziomie aplikacji i generowanie dzienników](service-fabric-diagnostics-event-generation-app.md).
* Wykonaj kroki, aby skonfigurować Application Insights aplikacji przy użyciu [monitorowania i diagnozowania aplikacji ASP.NET Core w Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Dowiedz się więcej o monitorowaniu platformy i zdarzeniach, Service Fabric zapewnia w przypadku [zdarzeń na poziomie platformy i generowania dzienników](service-fabric-diagnostics-event-generation-infra.md).
* Skonfiguruj integrację dzienników Azure Monitor przy użyciu usługi Service Fabric w obszarze [Konfigurowanie dzienników Azure monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Informacje o konfigurowaniu Azure Monitor dzienników na potrzeby monitorowania kontenerów — [monitorowania i diagnostyki dla kontenerów systemu Windows w usłudze Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zobacz przykładowe problemy z diagnostyką i rozwiązania Service Fabric w [diagnozowaniu typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md)
* Zapoznaj się z innymi produktami diagnostycznymi, które integrują się z Service Fabric w [Service Fabric partnerów diagnostycznych](service-fabric-diagnostics-partners.md)
* Poznaj ogólne zalecenia dotyczące monitorowania zasobów platformy Azure — [najlepsze praktyki — monitorowanie i Diagnostyka](/azure/architecture/best-practices/monitoring). 
