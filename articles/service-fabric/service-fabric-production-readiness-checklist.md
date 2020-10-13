---
title: Lista kontrolna gotowości produkcyjnej platformy Azure Service Fabric
description: Zapoznaj się z Service Fabric aplikacji i środowiska produkcyjnego klastra, wykonując następujące najlepsze rozwiązania.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835871"
---
# <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji

Czy Twoja aplikacja i klaster są gotowe do przetworzenia ruchu produkcyjnego? Uruchamianie i testowanie aplikacji oraz klaster nie musi oznaczać, że jest gotowe do użycia w środowisku produkcyjnym. Utrzymuj bezproblemowe działanie aplikacji i klastra, przechodząc przez następującą listę kontrolną. Zdecydowanie zalecamy, aby wszystkie te elementy były wyszukiwane. Oczywiście można wybrać opcję użycia alternatywnych rozwiązań dla konkretnego elementu wiersza (na przykład własnych struktur diagnostyki).


## <a name="prerequisites-for-production"></a>Wymagania wstępne dotyczące produkcji
1. Najlepsze rozwiązania dotyczące platformy Azure Service Fabric: [projektowanie aplikacji](./service-fabric-best-practices-applications.md), [zabezpieczenia](./service-fabric-best-practices-security.md), [Sieć](./service-fabric-best-practices-networking.md), [Planowanie pojemności i skalowanie](./service-fabric-best-practices-capacity-scaling.md), [infrastruktura jako kod](./service-fabric-best-practices-infrastructure-as-code.md)oraz [monitorowanie i Diagnostyka](./service-fabric-best-practices-monitoring.md). 
1. [Skonfiguruj ustawienia FabricTransport](./service-fabric-reliable-actors-fabrictransportsettings.md) w przypadku korzystania z modelu programowania Reliable Actors i wymagaj bezpiecznej komunikacji między usługami.
1. W przypadku klastrów mających więcej niż 20 rdzeni lub 10 węzłów Utwórz dedykowany typ węzła podstawowego dla usług systemowych. Dodaj [ograniczenia umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) w celu zarezerwowania typu węzła podstawowego dla usług systemowych.
1. Użyj jednostki SKU D2v2 lub wyższej dla typu węzła podstawowego. Zalecane jest wybranie jednostki SKU z co najmniej 50 GB miejsca na dysku twardym.
1. Klastry produkcyjne muszą być [bezpieczne](service-fabric-cluster-security.md). Aby zapoznać się z przykładem konfigurowania bezpiecznego klastra, zapoznaj się z tym [szablonem klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Używaj typowych nazw dla certyfikatów i Unikaj używania certyfikatów z podpisem własnym.
1. Dodaj [ograniczenia zasobów do kontenerów i usług](service-fabric-resource-governance.md), aby nie korzystały z więcej niż 75% zasobów węzła. 
1. Zrozumienie i Ustawianie [poziomu trwałości](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster). Zalecany jest poziom trwałości srebra lub wyższej dla typów węzłów uruchamiających obciążenia stanowe.
1. Zrozumienie i wybór [poziomu niezawodności](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) typu węzła. Zalecana jest niezawodność Silver lub wyższą.
1. Załaduj i Skaluj swoje obciążenia, aby identyfikować [wymagania dotyczące pojemności](service-fabric-cluster-capacity.md) dla klastra. 
1. Twoje usługi i aplikacje są monitorowane, a Dzienniki aplikacji są generowane i przechowywane z alertami. Na przykład zobacz [Dodawanie rejestrowania do aplikacji Service Fabric](service-fabric-how-to-diagnostics-log.md) i [monitorowanie kontenerów za pomocą dzienników Azure monitor](service-fabric-diagnostics-oms-containers.md).
1. Klaster jest monitorowany z alertami (na przykład z [dziennikami Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Podstawowa infrastruktura zestawu skalowania maszyn wirtualnych jest monitorowana przy użyciu alertów (na przykład z [dziennikami Azure monitor](service-fabric-diagnostics-oms-agent.md).
1. Klaster ma zawsze [podstawowe i pomocnicze certyfikaty](service-fabric-cluster-security-update-certs-azure.md) (więc nie można go zablokować).
1. Obsługa oddzielnych klastrów na potrzeby projektowania, przemieszczania i produkcji. 
1. [Uaktualnienia aplikacji](service-fabric-application-upgrade.md) i [uaktualnienia klastra](service-fabric-tutorial-upgrade-cluster.md) są najpierw testowane w ramach projektowania i przygotowywania klastrów. 
1. Wyłącz automatyczne uaktualnianie w klastrach produkcyjnych i włącz je dla klastrów programistycznych i przejściowych (Wycofaj w razie potrzeby). 
1. Ustanów cel punktu odzyskiwania dla usługi i skonfiguruj [proces odzyskiwania po awarii](service-fabric-disaster-recovery.md) i przetestuj go.
1. Planuj ręcznie lub programowo [skalować](service-fabric-cluster-scaling.md) klaster.
1. Zaplanuj [stosowanie poprawek](service-fabric-patch-orchestration-application.md) do węzłów klastra. 
1. Ustanów potok ciągłej integracji/ciągłego dostarczania, aby najnowsze zmiany były stale testowane. Na przykład przy użyciu [usługi Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) lub [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster)
1. Przetestuj & tworzenia klastrów przejściowych w ramach obciążenia z [usługą analizy błędów](service-fabric-testability-overview.md) i wywołujące kontrolowane [chaos](service-fabric-controlled-chaos.md). 
1. Zaplanuj [skalowanie](service-fabric-concepts-scalability.md) aplikacji. 


W przypadku korzystania z Service Fabric Reliable Services lub modelu programowania Reliable Actors należy wyewidencjonować następujące elementy:
1. Uaktualnij aplikacje podczas lokalnego tworzenia, aby sprawdzić, czy kod usługi jest uznawany za token anulowania w `RunAsync` metodzie i zamykając niestandardowe odbiorniki komunikacji.
1. Unikaj [typowych pułapek](service-fabric-work-with-reliable-collections.md) podczas korzystania z niezawodnych kolekcji.
1. Monitoruj liczniki wydajności pamięci środowiska CLR platformy .NET podczas uruchamiania testów obciążenia i sprawdzaj, czy nie ma wysokiego współczynnika wyrzucania elementów bezużytecznych lub wzrostu sterty.
1. Zachowaj kopię zapasową offline [Reliable Services i Reliable Actors](service-fabric-reliable-services-backup-restore.md) i przetestuj proces przywracania.
1. Liczba wystąpień podstawowej maszyny wirtualnej NodeType powinna być taka sama jak minimalna dla warstwy niezawodności klastrów; warunki, które są niezbędne do przekroczenia minimalnej warstwy: tymczasowo podczas skalowania w pionie podstawowej jednostki SKU zestawu skalowania maszyn wirtualnych elementów NodeType.

## <a name="optional-best-practices"></a>Opcjonalne najlepsze rozwiązania

Chociaż powyższe listy są wymaganiami wstępnymi do przechodzenia do środowiska produkcyjnego, należy wziąć pod uwagę następujące elementy:
1. Dołącz do [modelu kondycji Service Fabric](service-fabric-health-introduction.md) , aby rozszerzyć wbudowaną ocenę kondycji i raportowanie.
1. Wdróż niestandardowy licznik alarmowy monitorujący aplikację i raportów [obciążenia](service-fabric-cluster-resource-manager-metrics.md) na potrzeby [równoważenia zasobów](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Następne kroki
* [Wdrażanie klastra systemu Windows Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrażanie klastra z systemem Service Fabric Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Uzyskaj informacje o [cyklu życia aplikacji](service-fabric-application-lifecycle.md) usługi Service Fabric.