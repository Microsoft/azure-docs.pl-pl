---
title: Wprowadzenie do Service Fabric klastra Menedżer zasobów
description: Dowiedz się więcej na temat Menedżer zasobów klastrów Service Fabric, jak zarządzać aranżacją usług aplikacji.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75563330"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Wprowadzenie do Service Fabric Menedżera zasobów klastra
Tradycyjnie zarządza systemami IT lub Usługi online przeznaczonymi do dedykowanych maszyn fizycznych lub wirtualnych do tych konkretnych usług lub systemów. Usługi zostały zaprojektowane jako warstwy. Byłaby to warstwa "Web" i warstwa "Data" lub "Storage". Aplikacje będą mieć warstwę obsługi komunikatów, w której żądania przepływają i wychodzące, a także zestaw maszyn przeznaczony do buforowania. Dla każdej warstwy lub typu obciążenia odnoszą się do nich określone maszyny: baza danych ma kilka komputerów przeznaczonych dla niego, a także kilka serwerów sieci Web. Jeśli określony typ obciążenia spowodował, że maszyny były uruchomione zbyt gorące, dodaliśmy więcej maszyn z tą samą konfiguracją do tej warstwy. Nie wszystkie obciążenia mogą jednak być skalowane w taki sposób, aby w szczególności z warstwy danych były zazwyczaj zastępowane maszyny z większą ilością maszyn. Łatwe. Jeśli wystąpił błąd maszyny, ta część ogólnej aplikacji działała o mniejszej pojemności do momentu przywrócenia maszyny. Nadal dość łatwo (jeśli nie jest to konieczne).

Teraz jednak świat usługi i architektury oprogramowania uległy zmianie. Jest to bardziej powszechne, gdy aplikacje przyjęły projekt skalowalny w poziomie. Często trwa Kompilowanie aplikacji z kontenerami lub mikrousługami. Teraz można nadal korzystać tylko z kilku maszyn, ale nie są one uruchomione tylko jedno wystąpienie obciążenia. Mogą nawet jednocześnie uruchamiać wiele różnych obciążeń. Masz teraz dziesiątki różnych typów usług (brak zużywanych zasobów pełnej maszyny), a nawet setki różnych wystąpień tych usług. Każde nazwane wystąpienie ma jedno lub więcej wystąpień lub replik wysokiej dostępności (HA). W zależności od rozmiarów obciążeń i sposobu ich zajętości można znaleźć setki lub tysiące maszyn. 

Nagle zarządzanie środowiskiem nie jest tak proste jak zarządzanie kilkoma maszynami przeznaczonymi dla pojedynczych typów obciążeń. Serwery są wirtualne i nie mają już nazw (mindsets od [zwierząt domowych do bydła](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po wszystkich). Konfiguracja jest mniej o maszynach i więcej informacji na temat samych usług. Sprzęt, który jest przeznaczony dla jednego wystąpienia obciążenia, jest w dużym stopniu w przeszłości. Same usługi stają się małymi systemami rozproszonymi, które obejmują wiele mniejszych elementów sprzętu.

Ponieważ aplikacja nie jest już serią monolitów w wielu warstwach, teraz masz wiele większej liczby kombinacji. Kto decyduje, jakie typy obciążeń mogą być uruchamiane na tym sprzęcie lub ile? Które obciążenia pracują na tym samym sprzęcie i który jest w konflikcie? Kiedy na komputerze nie ma informacji o tym, co było uruchomione na tym komputerze? Kto jest odpowiedzialny za upewnienie się, że obciążenie zacznie działać ponownie? Czy czekaj, aż maszyna (wirtualna?) zostanie wycofana lub że obciążenia są automatycznie przełączane do trybu failover na innych maszynach i nadal działają? Czy wymagana jest interwencja ludzka? Co z uaktualnieniami w tym środowisku?

Deweloperzy i operatorzy zajmujący się tym środowiskiem chcą pomóc w zarządzaniu tym skomplikowaniem. Podczas zatrudniania Bing i próby ukrycia złożoności z osobami prawdopodobnie nie jest to właściwa odpowiedź, co robimy?

## <a name="introducing-orchestrators"></a>Wprowadzenie do programu orchestrators
"Koordynator" jest ogólnym terminem dla wielu programów, które ułatwiają administratorom zarządzanie tymi typami środowisk. Koordynatorzy to składniki, które przyjmują żądania, takie jak "chcę, aby pięć kopii tej usługi działały w moim środowisku". Starają się, aby środowisko było zgodne z żądanym stanem, niezależnie od tego, co się stanie.

Koordynatorzy (nie ludzie) to działania podejmowane w przypadku awarii maszyny lub przerwania obciążenia z jakiegoś nieoczekiwanego powodu. Większość programów Orchestrator ma więcej niż tylko rozpatruje błędy. Inne funkcje, które mają Zarządzanie nowymi wdrożeniami, obsługują uaktualnienia i w zakresie wykorzystania zasobów i zarządzania nimi. Wszystkie Koordynatory w całości mają na celu zachowanie pewnych żądanych stanów konfiguracji w środowisku. Chcesz mieć możliwość poinformowania koordynatora o tym, czego potrzebujesz, i przeprowadzenia dużego podnoszenia. Aurora z góry Mesos, Docker Datacenter/Docker Swarm, Kubernetes i Service Fabric są wszystkie przykłady dla programu Orchestrator. Te Koordynatory są aktywnie opracowywane w celu zaspokajania potrzeb rzeczywistych obciążeń w środowiskach produkcyjnych. 

## <a name="orchestration-as-a-service"></a>Aranżacja jako usługa
Menedżer zasobów klastra to składnik systemowy, który obsługuje aranżację w programie Service Fabric. Zadanie Menedżer zasobów klastra jest podzielone na trzy części:

1. Wymuszanie reguł
2. Optymalizowanie środowiska
3. Pomoc w innych procesach

### <a name="what-it-isnt"></a>Co nie jest
W tradycyjnych aplikacjach warstwy N jest zawsze [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Zwykle jest to Load Balancer sieci (NLB) lub Load Balancer aplikacji (ALB) w zależności od tego, gdzie SAT stosu sieciowego. Niektóre usługi równoważenia obciążenia są oparte na sprzęcie, takie jak F5's BigIP, inne są oparte na oprogramowaniu, takim jak równoważenie obciążenia sieciowego firmy Microsoft. W innych środowiskach można zobaczyć, jak HAProxy, Nginx, Istio lub wysłannika w tej roli. W tych architekturach zadanie równoważenia obciążenia polega na tym, że obciążenia bezstanowe odbierają się (w przybliżeniu) o tej samej ilości pracy. Strategie równoważenia obciążenia są różne. Niektóre moduły równoważenia obciążenia będą wysyłać każde inne wywołanie do innego serwera. Inne osoby udostępniają Przypinanie sesji/lepkość. Bardziej zaawansowane moduły używają rzeczywistej oceny obciążenia lub raportowania w celu kierowania wywołania na podstawie oczekiwanego kosztu i bieżącego obciążenia maszynowego.

Moduły równoważenia sieci lub routery komunikatów próbowały zapewnić przybliżoną równowagę warstwy sieci Web/procesu roboczego. Strategie zrównoważenia warstwy danych różnią się od mechanizmu magazynowania danych. Równoważenie warstwy danych w oparciu o fragmentowania danych, buforowanie, widoki zarządzane, procedury składowane i inne mechanizmy specyficzne dla magazynu.

Niektóre z tych strategii są interesujące, jednak Menedżer zasobów klastra Service Fabric nie są podobne do usługi równoważenia obciążenia sieciowego ani do pamięci podręcznej. Sieć Load Balancer zrównoważy frontony Dzięki rozproszeniu ruchu między frontonami. Menedżer zasobów klastra Service Fabric ma inną strategię. W szczególności Service Fabric przenosi *usługi* do miejsca, w którym są one największe, oczekiwano ruchu lub obciążenia. Na przykład może przenosić usługi do węzłów, które są obecnie zimne, ponieważ usługi, które nie są dużo pracy. Węzły mogą być zimne, ponieważ usługi, które były obecne, zostały usunięte lub przeniesione w innym miejscu. Innym przykładem może być również przeniesienie usługi z komputera w Menedżer zasobów klastra. Być może maszyna zostanie uaktualniona lub przeciążona z powodu wzrostu zużycia przez uruchomione na niej usługi. Alternatywnie można zwiększyć wymagania dotyczące zasobów usługi. Z tego powodu nie ma wystarczających zasobów na tym komputerze, aby kontynuować działanie. 

Ponieważ klaster Menedżer zasobów jest odpowiedzialny za przenoszenie usług, zawiera różne zestawy funkcji w porównaniu z tym, co znajduje się w usłudze równoważenia obciążenia sieciowego. Wynika to z faktu, że usługi równoważenia obciążenia sieciowego dostarczają ruch sieciowy do usług, nawet jeśli ta lokalizacja nie jest idealna do uruchamiania samej usługi. Klaster Service Fabric Menedżer zasobów wykorzystuje zasadniczo różne strategie w celu zapewnienia efektywnego wykorzystania zasobów w klastrze.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać informacje na temat architektury i przepływu informacji w klastrze Menedżer zasobów, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-architecture.md)
- Klaster Menedżer zasobów ma wiele opcji opisywania klastra. Aby dowiedzieć się więcej o metrykach, zapoznaj się z tym artykułem na temat [opisywania Service Fabric klastra](service-fabric-cluster-resource-manager-cluster-description.md)
- Aby uzyskać więcej informacji na temat konfigurowania usług, [Dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Metryki to sposób, w jaki Menedżer zasobów klastra Service Fabric zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobach ich konfiguracji, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
- Klaster Menedżer zasobów współpracuje z możliwościami zarządzania Service Fabric. Aby dowiedzieć się więcej o tej integracji, Przeczytaj [ten artykuł](service-fabric-cluster-resource-manager-management-integration.md)
- Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
