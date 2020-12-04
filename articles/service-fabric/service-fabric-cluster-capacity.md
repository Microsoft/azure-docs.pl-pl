---
title: Zagadnienia dotyczące planowania pojemności klastra Service Fabric
description: Typy węzłów, trwałość, niezawodność i inne zagadnienia, które należy wziąć pod uwagę podczas planowania klastra Service Fabric.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: 731dcfdf25efc4b2f44669dacd8a400037ed47f4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576336"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Zagadnienia dotyczące planowania pojemności klastra Service Fabric

Planowanie pojemności klastra jest ważne dla każdego Service Fabric środowisku produkcyjnym. Kluczowe zagadnienia obejmują:

* **Początkowa liczba i właściwości *typów węzłów* klastra**

* **Poziom *trwałości* każdego typu węzła**, który określa Service Fabric uprawnienia maszyny wirtualnej w ramach infrastruktury platformy Azure

* **Poziom *niezawodności* klastra**, który określa stabilność Service Fabric usług systemowych i ogólnej funkcji klastra

Ten artykuł przeprowadzi Cię przez znaczące punkty decyzyjne dla każdego z tych obszarów.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Początkowa liczba i właściwości typów węzłów klastra

*Typ węzła* definiuje rozmiar, liczbę i właściwości zestawu węzłów (maszyn wirtualnych) w klastrze. Każdy typ węzła zdefiniowany w klastrze Service Fabric jest mapowany na [zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md).

Każdy typ węzła jest odrębnym zestawem skalowania, ale może być niezależnie skalowany w górę lub w dół, mieć różne zestawy portów otwarte i mieć różne metryki pojemności. Aby uzyskać więcej informacji na temat relacji między typami węzłów i zestawami skalowania maszyn wirtualnych, zobacz [Service Fabric typy węzłów klastra](service-fabric-cluster-nodetypes.md).

Każdy klaster wymaga jednego **podstawowego typu węzła**, który uruchamia krytyczne usługi systemowe zapewniające możliwości platformy Service Fabric. Chociaż można również używać typów węzłów podstawowych do uruchamiania aplikacji, zaleca się ich oddzielenie wyłącznie do uruchamiania usług systemowych.

**Typy węzłów innych niż podstawowe** mogą służyć do definiowania ról aplikacji (takich jak usługi *frontonu* i *zaplecza* ) oraz do fizycznego izolowania usług w ramach klastra. Klastry Service Fabric mogą mieć zero lub więcej typów węzłów innych niż podstawowe.

Typ węzła podstawowego jest konfigurowany przy użyciu `isPrimary` atrybutu w definicji typu węzła w szablonie wdrażania Azure Resource Manager. Zobacz [obiekt NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) , aby uzyskać pełną listę właściwości typu węzła. Na przykład, Otwórz dowolny *AzureDeploy.js* w pliku w [Service Fabric przykładach klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) i *Znajdź na stronie* wyszukiwanie `nodeTypes` obiektu.

### <a name="node-type-planning-considerations"></a>Zagadnienia dotyczące planowania typu węzła

Liczba typów węzłów początkowych zależy od celu klastra i działających na nim aplikacji i usług. Zastanów się nad następującymi pytaniami:

* *Czy **Twoja aplikacja ma wiele usług i czy wszystkie z nich muszą być dostępne publicznie czy z Internetu?** _

    Typowe aplikacje zawierają usługę bramy frontonu, która odbiera dane wejściowe od klienta oraz co najmniej jedną usługi zaplecza, która komunikuje się z usługami frontonu, z oddzielną siecią między usługami frontonu i zaplecza. Te przypadki wymagają zwykle trzech typów węzłów: jeden podstawowy typ węzła i dwa typy węzłów innych niż podstawowe (jeden dla usługi frontonu i zaplecza).

_ ***Czy usługi, które tworzą swoją aplikację, mają różne potrzeby związane z infrastrukturą, takie jak większa ilość pamięci RAM czy więcej cykli procesora CPU?** _

    Often, front-end service can run on smaller VMs (VM sizes like D2) that have ports open to the internet.  Computationally intensive back-end services might need to run on larger VMs (with VM sizes like D4, D6, D15) that are not internet-facing. Defining different node types for these services allow you to make more efficient and secure use of underlying Service Fabric VMs, and enables them to scale them independently. For more on estimating the amount of resources you'll need, see [Capacity planning for Service Fabric applications](service-fabric-capacity-planning.md)

_ * Czy **każda z usług aplikacji musi skalować w poziomie ponad 100 węzłów?** _

    A single node type can't reliably scale beyond 100 nodes per virtual machine scale set for Service Fabric applications. Running more than 100 nodes requires additional virtual machine scale sets (and therefore additional node types).

_ ***Czy klaster będzie obejmował między strefy dostępności?** _

    Service Fabric supports clusters that span across [Availability Zones](../availability-zones/az-overview.md) by deploying node types that are pinned to specific zones, ensuring high-availability of your applications. Availability Zones require additional node type planning and minimum requirements. For details, see [Recommended topology for primary node type of Service Fabric clusters spanning across Availability Zones](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

Podczas określania liczby i właściwości typów węzłów do wstępnego tworzenia klastra należy pamiętać, że po wdrożeniu klastra zawsze można dodawać, modyfikować lub usuwać typy węzłów niebędących podstawowymi. [Typy węzłów podstawowych można także modyfikować](service-fabric-scale-up-primary-node-type.md) w uruchomionych klastrach (chociaż operacje te wymagają dużej zamiaru planowania i przestrogi w środowiskach produkcyjnych).

Dalsze zagadnienia dotyczące właściwości typu węzła to poziom trwałości, który określa uprawnienia maszyn wirtualnych typu węzła w ramach infrastruktury platformy Azure. Użyj rozmiaru maszyn wirtualnych wybranych dla klastra i liczby wystąpień przypisanej do poszczególnych typów węzłów, aby pomóc w ustaleniu odpowiedniej warstwy trwałości dla każdego typu węzła, zgodnie z opisem w następnej kolejności.

## <a name="durability-characteristics-of-the-cluster"></a>Charakterystyki trwałości klastra

Poziom _durability * wyznacza uprawnienia Service Fabric maszyn wirtualnych z podstawową infrastrukturą platformy Azure. To uprawnienie umożliwia Service Fabric wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny wirtualnej (takich jak ponowny rozruch, odtwarzanie obrazu lub migracja), które mają wpływ na wymagania dotyczące kworum Service Fabric usług systemowych i usług stanowych.

> [!IMPORTANT]
> Poziom trwałości jest ustawiany na typ węzła. Jeśli nie określono, zostanie użyta warstwa *Bronów* , ale nie zostanie ona zastosowana do automatycznych uaktualnień systemu operacyjnego. W przypadku obciążeń produkcyjnych zaleca się trwałość *Silver* lub *Gold* .

W poniższej tabeli wymieniono Service Fabric warstw trwałości, ich wymagania i affordances.

| Warstwa trwałości  | Wymagana minimalna liczba maszyn wirtualnych | Obsługiwane rozmiary maszyn wirtualnych                                                                  | Aktualizacje wprowadzane do zestawu skalowania maszyn wirtualnych                               | Aktualizacje i konserwacja zainicjowane przez platformę Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Złoty             | 5                              | Rozmiary całego węzła przeznaczone dla pojedynczego klienta (na przykład L32s, GS5, G5, DS15_v2, D15_v2) | Może zostać opóźnione do zatwierdzenia przez klaster Service Fabric | Można wstrzymywać przez 2 godziny na domenę uaktualnienia, aby zapewnić dodatkowy czas odzyskiwania po wcześniejszych awariach |
| Srebrny           | 5                              | Maszyny wirtualne jednego rdzenia lub nowszego z co najmniej 50 GB lokalnego dysku SSD                      | Może zostać opóźnione do zatwierdzenia przez klaster Service Fabric | Nie może być opóźniony przez dowolny znaczny okres czasu                                                    |
| Bron          | 1                              | Maszyny wirtualne z co najmniej 50 GB lokalnego dysku SSD                                              | Nie zostanie opóźniony przez klaster Service Fabric           | Nie może być opóźniony przez dowolny znaczny okres czasu                                                    |

> [!WARNING]
> W przypadku trwałości Bronze automatyczne uaktualnianie obrazu systemu operacyjnego jest niedostępne. Mimo że [aplikacja aranżacji poprawek](service-fabric-patch-orchestration-application.md) (przeznaczona tylko dla klastrów hostowanych poza platformą Azure) *nie jest zalecana* dla poziomów trwałości Silver lub większej, jest to jedyna opcja automatyzacji aktualizacji systemu Windows w odniesieniu do domen uaktualnienia Service Fabric.

> [!IMPORTANT]
> Niezależnie od poziomu trwałości, uruchomienie operacji [cofania alokacji](/rest/api/compute/virtualmachinescalesets/deallocate) w zestawie skalowania maszyn wirtualnych spowoduje zniszczenie klastra.

### <a name="bronze"></a>Bron

Typy węzłów działające z trwałością Bronze nie uzyskują żadnych uprawnień. Oznacza to, że zadania infrastruktury mające wpływ na obciążenia stanowe nie zostaną zatrzymane ani opóźnione. Użyj trwałości Bronze dla typów węzłów, które obsługują tylko wykonywanie obciążeń bezstanowych. W przypadku obciążeń produkcyjnych zaleca się używanie srebra lub wyższej.

### <a name="silver-and-gold"></a>Silver i Gold

Należy stosować trwałość Srebrna lub złota dla wszystkich typów węzłów, które obsługują usługi stanowe, które mogą być często skalowane, i miejsce, w którym operacje wdrażania są opóźnione i zmniejszane zdolności do uproszczenia procesu. Scenariusze skalowania w poziomie nie powinny wpływać na wybór warstwy trwałości.

#### <a name="advantages"></a>Zalety

* Zmniejsza liczbę wymaganych kroków operacji skalowania w poziomie (dezaktywacja węzła i Remove-ServiceFabricNodeState są wywoływane automatycznie).
* Zmniejsza ryzyko utraty danych z powodu operacji zmiany rozmiaru maszyny wirtualnej w miejscu i operacji infrastruktury platformy Azure.

#### <a name="disadvantages"></a>Wady

* Wdrożenia do zestawów skalowania maszyn wirtualnych i innych powiązanych zasobów platformy Azure mogą przekroczyć limit czasu, zostać opóźnione lub zablokowane w całości przez problemy w klastrze lub na poziomie infrastruktury.
* Zwiększa liczbę [zdarzeń związanych z cyklem życia repliki](service-fabric-reliable-services-lifecycle.md) (na przykład podstawowych wymian) z powodu zautomatyzowanej aktywacji węzłów podczas operacji infrastruktury platformy Azure.
* Pobiera węzły z usługi przez okres czasu, gdy są wykonywane aktualizacje oprogramowania platformy Azure lub działania związane z konserwacją sprzętu. W tych działaniach mogą pojawić się węzły o stanie wyłączania/wyłączania. Pozwala to na tymczasowe zmniejszenie pojemności klastra, ale nie ma to wpływu na dostępność klastra lub aplikacji.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Najlepsze rozwiązania dotyczące typów węzłów trwałości Silver i Gold

Postępuj zgodnie z tymi zaleceniami, aby zarządzać typami węzłów o trwałości Silver lub Gold:

* Przechowuj klaster i aplikacje przez cały czas i upewnij się, że aplikacje odpowiadają na wszystkie [zdarzenia cyklu życia repliki usługi](service-fabric-reliable-services-lifecycle.md) (takie jak replika w kompilacji jest zablokowana) w odpowiednim czasie.
* Należy wprowadzić bezpieczniejsze metody zmiany rozmiaru maszyny wirtualnej (skalowanie w górę/w dół). Zmiana rozmiaru maszyny wirtualnej zestawu skalowania maszyn wirtualnych wymaga starannego planowania i przestrogi. Aby uzyskać szczegółowe informacje, zobacz [skalowanie w górę Service Fabric typu węzła](service-fabric-scale-up-primary-node-type.md)
* Należy zachować minimalną liczbę pięciu węzłów dla dowolnego zestawu skalowania maszyn wirtualnych z włączonym poziomem trwałości Gold lub Silver. W przypadku skalowania poniżej tego progu klaster przejdzie w stan błędu i trzeba będzie ręcznie oczyścić stan ( `Remove-ServiceFabricNodeState` ) dla usuniętych węzłów.
* Każdy zestaw skalowania maszyn wirtualnych z poziomem trwałości Silver lub Gold musi być mapowany na własny typ węzła w klastrze Service Fabric. Mapowanie wielu zestawów skalowania maszyn wirtualnych na jeden typ węzła uniemożliwi prawidłowe działanie koordynacji między klastrem Service Fabric i infrastrukturą platformy Azure.
* Nie usuwaj losowych wystąpień maszyn wirtualnych, zawsze używaj funkcji skalowania w ramach zestawu skalowania maszyn wirtualnych. Usuwanie losowych wystąpień maszyn wirtualnych ma potencjalne możliwości tworzenia nierównowagi w rozmieszczenia wystąpień maszyn wirtualnych w [domenach uaktualnienia](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) i [domenach błędów](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Takie niezrównoważone może mieć negatywny wpływ na zdolność systemów do prawidłowego równoważenia obciążenia między wystąpieniami usługi/replikami usługi.
* W przypadku korzystania z funkcji automatycznego skalowania należy ustawić reguły, takie jak skalowanie w górę (usuwanie wystąpień maszyn wirtualnych), tylko jeden węzeł w danym momencie. Skalowanie w dół więcej niż jedno wystąpienie w czasie jest niebezpieczne.
* W przypadku usuwania lub cofania przydziału maszyn wirtualnych w typie węzła podstawowego nigdy nie Zmniejsz liczby przyznanych maszyn wirtualnych poniżej tego, co jest wymagane przez warstwę niezawodności. Te operacje będą blokowane w nieskończoność w zestawie skalowania z poziomem trwałości Silver lub Gold.

### <a name="changing-durability-levels"></a>Zmienianie poziomów trwałości

W ramach pewnych ograniczeń można dostosować poziom trwałości typu węzła:

* Typów węzłów o poziomach trwałości Silver lub Gold nie można zmienić na starszą wersję.
* Uaktualnienie z wersji Bronze do Silver lub Gold może potrwać kilka godzin.
* W przypadku zmiany poziomu trwałości należy zaktualizować go zarówno w konfiguracji rozszerzenia Service Fabric w ramach zasobu zestawu skalowania maszyn wirtualnych, jak i w definicji typu węzła w zasobie klastra Service Fabric. Te wartości muszą być zgodne.

Należy wziąć pod uwagę, gdy planowanie wydajności jest poziomem niezawodności dla klastra, co określa stabilność usług systemowych i ogólnego klastra, zgodnie z opisem w następnej sekcji.

## <a name="reliability-characteristics-of-the-cluster"></a>Charakterystyka niezawodności klastra

*Poziom niezawodności* klastra określa liczbę replik usług systemowych uruchomionych w ramach podstawowego typu węzła klastra. Im większa jest liczba replik, tym bardziej niezawodna usługa systemowa (i w związku z tym klaster jako całość).

> [!IMPORTANT]
> Poziom niezawodności jest ustawiany na poziomie klastra i określa minimalną liczbę węzłów podstawowego typu węzła. Obciążenia produkcyjne wymagają poziomu niezawodności Silver (większy lub równy pięć węzłów) lub powyżej.  

Warstwa niezawodności może przyjmować następujące wartości:

* **Pakiet Platinum** — usługa systemowa jest uruchamiana z liczbą docelowych zestawu replik dziewięciu
* **Złote** usługi systemowe są uruchamiane z liczbą docelowych zestawu replik siedmiu
* **Silver** — usługi systemowe działają z liczbą docelowych zestawu replik 5
* **Brązowe** usługi systemowe są uruchamiane z liczbą docelowych zestawu replik trzech

Poniżej przedstawiono zalecenia dotyczące wyboru warstwy niezawodności. Liczba węzłów inicjatora jest również ustawiona na minimalną liczbę węzłów dla warstwy niezawodności.


| **Liczba węzłów** | **Warstwa niezawodności** |
| --- | --- |
| 1 | *Nie określaj `reliabilityLevel` parametru: system oblicza go.* |
| 3 | Bron |
| 5 lub 6| Srebrny |
| 7 lub 8 | Złoty |
| 9 i w górę | Platyn |

W przypadku zwiększenia lub zmniejszenia rozmiaru klastra (suma wystąpień maszyn wirtualnych we wszystkich typach węzłów) należy rozważyć aktualizację niezawodności klastra z jednej warstwy do innej. Spowoduje to wyzwolenie uaktualnień klastra wymaganych do zmiany liczby zestawów replik usług systemowych. Poczekaj na zakończenie uaktualniania przed wprowadzeniem jakichkolwiek innych zmian w klastrze, takich jak Dodawanie węzłów.  Postęp uaktualniania można monitorować na Service Fabric Explorer lub przez uruchomienie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="capacity-planning-for-reliability"></a>Planowanie wydajności dla niezawodności

Wymagania dotyczące pojemności klastra będą ustalane na podstawie konkretnych wymagań dotyczących obciążenia i niezawodności. Ta sekcja zawiera ogólne wskazówki ułatwiające rozpoczęcie planowania pojemności.

#### <a name="virtual-machine-sizing"></a>Rozmiar maszyny wirtualnej

**W przypadku obciążeń produkcyjnych zalecany rozmiar maszyny wirtualnej (SKU) to [standardowa D2_V2](../virtual-machines/dv2-dsv2-series.md) (lub równoważne) z co najmniej 50 GB lokalnego dysku SSD, 2 rdzenie i 4 GIB pamięci.** Zalecany jest co najmniej 50 GB lokalnego dysku SSD, ale niektóre obciążenia (na przykład te z uruchomionymi kontenerami systemu Windows) będą wymagały większych dysków. Wybierając inne [rozmiary maszyn wirtualnych](../virtual-machines/sizes-general.md) dla obciążeń produkcyjnych, należy pamiętać o następujących ograniczeniach:

- Częściowe podstawowe rozmiary maszyn wirtualnych, takie jak standardowa a0, nie są obsługiwane.
- *Seria A* Rozmiary maszyn wirtualnych nie są obsługiwane ze względu na wydajność.
- Maszyny wirtualne o niskim priorytecie nie są obsługiwane.

#### <a name="primary-node-type"></a>Typ węzła podstawowego

**Obciążenia produkcyjne** na platformie Azure wymagają co najmniej pięciu węzłów głównych (wystąpień maszyn wirtualnych) i warstwy niezawodności Silver. Zaleca się, aby przeznaczyć typ węzła podstawowego klastra na usługi systemowe, a następnie użyć ograniczeń umieszczania w celu wdrożenia aplikacji do typów węzła pomocniczego.

**Obciążenia testowe** na platformie Azure mogą uruchamiać co najmniej jeden węzeł podstawowy. Aby skonfigurować klaster z jednym węzłem, upewnij się, że to `reliabilityLevel` ustawienie zostało całkowicie pominięte w szablonie Menedżer zasobów (określenie pustej wartości ciągu dla `reliabilityLevel` nie jest wystarczające). W przypadku skonfigurowania klastra z jednym węzłem skonfigurowanym przy użyciu Azure Portal ta konfiguracja zostanie wykonana automatycznie.

> [!WARNING]
> Klastry z jednym węzłem są uruchamiane z specjalną konfiguracją bez niezawodności i gdzie skalowanie w poziomie nie jest obsługiwane.

#### <a name="non-primary-node-types"></a>Typy węzłów innych niż podstawowe

Minimalna liczba węzłów dla typu węzła innego niż podstawowy zależy od określonego [poziomu trwałości](#durability-characteristics-of-the-cluster) typu węzła. Należy zaplanować liczbę węzłów (i poziom trwałości) na podstawie liczby replik aplikacji lub usług, które mają być uruchamiane dla typu węzła, i w zależności od tego, czy obciążenie jest stanowe czy bezstanowe. Należy pamiętać, że w dowolnym momencie po wdrożeniu klastra można zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w typie węzła.

##### <a name="stateful-workloads"></a>Obciążenia stanowe

W przypadku obciążeń stanowych produkcji przy użyciu Service Fabric [niezawodnych kolekcji lub niezawodnych aktorów](service-fabric-choose-framework.md)zaleca się użycie minimalnej i docelowej liczby replik równej 5. W związku z tym w stanie stały się możliwe przeznaczenie repliki (z zestawu replik) w każdej domenie błędów i domenie uaktualnienia. Ogólnie rzecz biorąc, należy użyć poziomu niezawodności ustawionych dla usług systemowych jako wskazówki dla liczby replik używanej dla usług stanowych.

##### <a name="stateless-workloads"></a>Obciążenia bezstanowe

W przypadku bezstanowych obciążeń produkcyjnych minimalny obsługiwany typ węzła innego niż podstawowy to trzy do obsługi kworum, jednak zalecany jest rozmiar typu węzła 5.

## <a name="next-steps"></a>Następne kroki

Przed skonfigurowaniem klastra zapoznaj się z `Not Allowed` [zasadami uaktualniania klastra](service-fabric-cluster-fabric-settings.md) , aby uniknąć konieczności ponownego tworzenia klastra w przyszłości z powodu niezmienionych ustawień konfiguracji systemu.

Aby uzyskać więcej informacji na temat planowania klastrów, zobacz:

* [Planowanie i skalowanie obliczeń](service-fabric-best-practices-capacity-scaling.md)
* [Planowanie pojemności dla aplikacji Service Fabric](service-fabric-capacity-planning.md)
* [Planowanie odzyskiwania po awarii](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
