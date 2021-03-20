---
title: Skalowanie klastra Service Fabric platformy Azure
description: Dowiedz się więcej na temat skalowania klastrów usługi Azure Service Fabric w programie lub w górę i w dół. Gdy aplikacja wymaga zmiany, może Service Fabric klastrów.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591789"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skalowanie klastrów Service Fabric platformy Azure
Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Klastry mogą zawierać potencjalnie tysiące węzłów. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie (zmienić liczbę węzłów) lub w pionie (zmienić zasoby węzłów).  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

Dlaczego warto skalować klaster? Wymagania aplikacji zmieniają się w miarę upływu czasu.  Może być konieczne zwiększenie zasobów klastra w celu spełnienia zwiększonych obciążeń aplikacji lub ruchu sieciowego albo zmniejszenie zasobów klastra po spadku popytu.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie
Zmienia liczbę węzłów w klastrze.  Po dołączeniu nowych węzłów do klastra [Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md) przenosi do nich usługi, co zmniejsza obciążenie istniejących węzłów.  Możesz również zmniejszyć liczbę węzłów, jeśli zasoby klastra nie są używane efektywnie.  Gdy węzły opuszczają klaster, usługi przechodzą z tych węzłów i obciążenia są zwiększane w pozostałych węzłach.  Zmniejszenie liczby węzłów w klastrze działającym na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę używanych maszyn wirtualnych, a nie obciążenia na tych maszynach wirtualnych.  

- Zalety: nieskończone skalowanie, teoretycznie.  Jeśli aplikacja jest przeznaczona do skalowalności, możesz włączyć nieograniczony wzrost, dodając więcej węzłów.  Narzędzia w środowiskach w chmurze ułatwiają dodawanie i usuwanie węzłów, dzięki czemu można łatwo dostosowywać pojemność i płacisz tylko za zasoby, których używasz.  
- Wady: aplikacje muszą być [zaprojektowane w celu zapewnienia skalowalności](service-fabric-concepts-scalability.md).  Bazy danych i trwałość aplikacji mogą wymagać dodatkowej pracy w architekturze.  [Niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md) w Service Fabricych usługach stanowych, jednak znacznie ułatwiają skalowanie danych aplikacji.

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła można następnie przeskalować lub wyróżnić niezależnie, mieć otwarte różne zestawy portów i może mieć różne metryki pojemności. 

Podczas skalowania klastra platformy Azure należy pamiętać o następujących kwestiach:
- typy węzła podstawowego z uruchomionymi obciążeniami produkcyjnymi powinny mieć zawsze pięć lub więcej węzłów.
- typy węzłów innych niż podstawowe działające stanowe obciążenia produkcyjne powinny mieć zawsze pięć lub więcej węzłów.
- typy węzłów innych niż podstawowe, które działają bezstanowe obciążenia produkcyjne, powinny zawsze mieć co najmniej dwa węzły.
- Każdy typ węzła [poziomu trwałości](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Gold lub Silver powinien mieć zawsze pięć lub więcej węzłów.
- Nie usuwaj losowych wystąpień maszyn wirtualnych/węzłów z typu węzła, zawsze używaj funkcji skalowania zestawu skalowania maszyn wirtualnych. Usunięcie losowych wystąpień maszyn wirtualnych może niekorzystnie wpłynąć na zdolność systemu do prawidłowego równoważenia obciążenia.
- W przypadku używania reguł skalowania automatycznego Ustaw reguły tak, aby skalowanie w poziomie (usuwanie wystąpień maszyn wirtualnych) było wykonywane po jednym węźle w danym momencie. Skalowanie w dół więcej niż jedno wystąpienie w czasie jest niebezpieczne.

Ponieważ typy węzłów Service Fabric w klastrze składają się z zestawów skalowania maszyn wirtualnych w zapleczu, można [skonfigurować reguły skalowania automatycznego lub ręcznie skalować](service-fabric-cluster-scale-in-out.md) każdy typ węzła/zestaw skalowania maszyn wirtualnych.

### <a name="programmatic-scaling"></a>Skalowanie programistyczne
W wielu scenariuszach [skalowanie klastra odbywa się ręcznie lub przy użyciu reguł skalowania automatycznego](service-fabric-cluster-scale-in-out.md) . W przypadku bardziej zaawansowanych scenariuszy może to nie być odpowiednie dopasowanie. Potencjalne wady do tych podejścia obejmują:

- Ręczne skalowanie wymaga, aby zalogować się i jawnie zażądać operacji skalowania. Jeśli operacje skalowania są wymagane często lub w nieprzewidywalnym czasie, to podejście może nie być dobrym rozwiązaniem.
- Gdy reguły automatycznego skalowania usuwają wystąpienie z zestawu skalowania maszyn wirtualnych, nie usuwają automatycznie informacji o tym węźle z skojarzonego klastra Service Fabric, chyba że typ węzła ma poziom trwałości Silver lub Gold. Ponieważ reguły automatycznego skalowania działają na poziomie zestawu skalowania (a nie na poziomie Service Fabric), reguły skalowania automatycznego mogą usuwać węzły Service Fabric bez ich bezpiecznego zamykania. Usunięcie węzła prosta spowoduje pozostawienie "Ghost" Service Fabric stanu węzła po operacji skalowania w poziomie. Osoba (lub usługa) musi okresowo czyścić stan usuniętych węzłów w klastrze Service Fabric.
- Typ węzła o poziomie trwałości Gold lub Silver automatycznie czyści usunięte węzły, dlatego nie jest konieczna żadna dodatkowa czyszczenie.
- Mimo że istnieje [wiele metryk](../azure-monitor/autoscale/autoscale-common-metrics.md) obsługiwanych przez reguły automatycznego skalowania, jest on nadal ograniczonym zestawem. Jeśli scenariusz wywołuje skalowanie na podstawie pewnej metryki, która nie jest objęta tym zestawem, reguły skalowania automatycznego mogą nie być dobrą opcją.

Sposób podejścia Service Fabric skalowania zależy od Twojego scenariusza. Jeśli skalowanie jest nietypowe, prawdopodobnie wystarczy dodać lub usunąć węzły ręcznie. W przypadku bardziej złożonych scenariuszy reguły automatycznego skalowania i zestawy SDK uwidaczniające możliwość skalowania programistycznie oferują zaawansowane alternatywy.

Istnieją interfejsy API platformy Azure, które umożliwiają aplikacjom programistyczne współpracują z zestawami skalowania maszyn wirtualnych i klastrami Service Fabric. Jeśli istniejące opcje automatycznego skalowania nie działają w danym scenariuszu, te interfejsy API umożliwiają implementację niestandardowej logiki skalowania. 

Jednym z metod implementowania tej funkcji automatycznego skalowania w domu jest dodanie nowej usługi bezstanowej do aplikacji Service Fabric w celu zarządzania operacjami skalowania. Tworzenie własnej usługi skalowania zapewnia najwyższy stopień kontroli i szerszym w porównaniu z zachowaniem skalowania aplikacji. Może to być przydatne w scenariuszach wymagających precyzyjnej kontroli nad tym, kiedy i w jaki sposób aplikacja skaluje się lub wypada. Jednak ta kontrolka zapewnia kompromis z wadą złożoności kodu. Użycie tej metody oznacza, że musisz mieć własny kod skalowania, który nie jest prosty. W ramach metody usługi `RunAsync` zestaw wyzwalaczy może określić, czy wymagane jest skalowanie (w tym sprawdzanie parametrów, takich jak maksymalny rozmiar klastra i skalowanie cooldowns).   

Interfejs API służący do interakcji z zestawem skalowania maszyn wirtualnych (zarówno w celu sprawdzenia bieżącej liczby wystąpień maszyn wirtualnych i zmodyfikowania jej) jest [usługą Fluent platformy Azure do zarządzania](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Biblioteka obliczeń Fluent oferuje łatwy w użyciu interfejs API do współpracy z zestawami skalowania maszyn wirtualnych.  Aby korzystać z klastra Service Fabric, użyj programu [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient).

Kod skalowania nie musi być uruchamiany jako usługa w klastrze, aby można było go skalować, chociaż. Zarówno, `IAzure` jak i `FabricClient` mogą łączyć się ze skojarzonymi zasobami platformy Azure, dzięki czemu usługa skalowania może łatwo być aplikacją konsolową lub usługą systemu Windows działającą spoza aplikacji Service Fabric.

Na podstawie tych ograniczeń warto [zaimplementować bardziej dostosowane modele skalowania automatycznego](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasoby (procesor CPU, pamięć lub magazyn) węzłów w klastrze.
- Zalety: architektura oprogramowania i aplikacji pozostaje taka sama.
- Wady: skończone skalowanie, ponieważ istnieje limit ilości zasobów w poszczególnych węzłach, które można zwiększyć. Przestój, ponieważ należy przełączyć maszyny fizyczne lub wirtualne w tryb offline, aby można było dodawać lub usuwać zasoby.

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno.  Skalowanie typu węzła w górę lub w dół obejmuje dodanie nowego typu węzła (ze zaktualizowaną jednostką SKU maszyny wirtualnej) i usunięcie starego typu węzła.

Podczas skalowania klastra platformy Azure należy pamiętać o następujących kwestiach:
- W przypadku skalowania w dół typu węzła podstawowego nigdy nie należy skalować go w dół więcej niż to, co zapewnia [warstwa niezawodności](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) .

Proces skalowania typu węzła w górę lub w dół jest różny w zależności od tego, czy jest to typ węzła innego niż podstawowy czy podstawowy.

### <a name="scaling-non-primary-node-types"></a>Skalowanie typów węzłów innych niż podstawowe
Utwórz nowy typ węzła z zasobami, które są potrzebne.  Zaktualizuj ograniczenia umieszczania uruchomionych usług w celu uwzględnienia nowego typu węzła.  Stopniowo (po jednym naraz) Zmniejsz liczbę wystąpień starego wystąpienia typu węzła do wartości zero, aby nie oddziaływać na niezawodność klastra.  Usługi zostaną stopniowo migrowane do nowego typu węzła, ponieważ stary typ węzła zostanie zlikwidowany.

### <a name="scaling-the-primary-node-type"></a>Skalowanie typu węzła podstawowego
Wdróż nowy typ węzła podstawowego ze zaktualizowaną jednostką SKU maszyny wirtualnej, a następnie wyłącz początkowe wystąpienia pierwotnego węzła podstawowego po jednej naraz, tak aby usługi systemowe zostały zmigrowane do nowego zestawu skalowania. Sprawdź, czy klaster i nowe węzły są w dobrej kondycji, a następnie usuń oryginalny zestaw skalowania i stan węzła dla usuniętych węzłów.

Jeśli to nie jest możliwe, można utworzyć nowy klaster i [przywrócić stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie trzeba przywracać żadnego stanu usługi systemowej, zostaną one odtworzone po wdrożeniu aplikacji do nowego klastra. Jeśli w klastrze uruchomiono tylko bezstanowe aplikacje, wszystkie te aplikacje są wdrażane w nowym klastrze, nie ma niczego do przywrócenia.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

