---
title: Planowanie pojemności klastra Service Fabric
description: Zagadnienia dotyczące planowania pojemności klastra Service Fabric. Elementów NodeType, operacje, warstwy trwałości i niezawodności
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258917"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Zagadnienia dotyczące planowania pojemności klastra Service Fabric
W przypadku wszystkich wdrożeń produkcyjnych planowanie pojemności jest ważnym krokiem. Poniżej przedstawiono niektóre elementy, które należy wziąć pod uwagę w ramach tego procesu.

* Liczba typów węzłów, których klaster musi się rozpocząć
* Właściwości każdego typu węzła (rozmiar, podstawowe, dostęp z Internetu, liczba maszyn wirtualnych itp.)
* Charakterystyka niezawodności i trwałości klastra

> [!NOTE]
> Podczas planowania należy w minimalny sposób przejrzeć wszystkie **niedozwolone** wartości zasad uaktualniania. Ma to na celu upewnienie się, że odpowiednio ustawisz odpowiednie wartości, a następnie w celu zmniejszenia liczby ponownych prób spalania klastra z powodu niezmienionych ustawień konfiguracji systemu. 
> 

Powiadom nas o każdej z tych elementów.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Liczba typów węzłów, których klaster musi się rozpocząć
Najpierw należy ustalić, co tworzony klaster ma być używany dla programu.  Jakiego rodzaju aplikacje są planowane do wdrożenia w tym klastrze? Jeśli przeznaczenie ma nie potrwać, najprawdopodobniej nie masz jeszcze gotowości do wprowadzenia procesu planowania pojemności.

Ustal liczbę typów węzłów, z którymi klaster musi się rozpocząć.  Każdy typ węzła jest mapowany na zestaw skalowania maszyn wirtualnych. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. W związku z tym decyzja o liczbie typów węzłów zasadniczo uwzględnia następujące zagadnienia:

* Czy Twoja aplikacja ma wiele usług i czy wszystkie z nich muszą być dostępne publicznie czy z Internetu? Typowe aplikacje zawierają usługę bramy frontonu, która odbiera dane wejściowe od klienta i co najmniej jednej usługi zaplecza, która komunikuje się z usługami frontonu. Tak więc w tym przypadku można zakończyć posiadanie co najmniej dwóch typów węzłów.
* Czy usługi (które tworzą aplikację) mają różne potrzeby związane z infrastrukturą, takie jak większa ilość pamięci RAM czy więcej cykli procesora CPU? Załóżmy na przykład, że aplikacja, którą chcesz wdrożyć, zawiera usługę frontonu i usługę zaplecza. Usługa frontonu może być uruchamiana na mniejszych maszynach wirtualnych (takich jak D2), które mają otwarte porty w Internecie.  Usługa zaplecza, jednak jest intensywnie obliczeniowa i musi działać na większych maszynach wirtualnych (z rozmiarami maszyn wirtualnych takimi jak D4, D6, D15), które nie są połączone z Internetem.
  
  W tym przykładzie, chociaż możesz zdecydować się na umieszczenie wszystkich usług na jednym typie węzła, zalecamy umieszczenie ich w klastrze przy użyciu dwóch typów węzłów.  Dzięki temu każdy typ węzła może mieć odrębne właściwości, takie jak łączność z Internetem lub rozmiar maszyny wirtualnej. Liczba maszyn wirtualnych może być również skalowana niezależnie.  
* Ze względu na to, że nie możesz przewidzieć przyszłości, zapoznaj się z poznanymi faktami i wybierz liczbę typów węzłów, z którymi aplikacje muszą się rozpoczynać. Można zawsze dodawać lub usuwać typy węzłów w późniejszym czasie. Klaster Service Fabric musi mieć co najmniej jeden typ węzła.

## <a name="the-properties-of-each-node-type"></a>Właściwości każdego typu węzła
**Typ węzła** może być traktowany jako odpowiednik ról w Cloud Services. Typy węzłów definiują rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Każdy typ węzła zdefiniowany w klastrze Service Fabric jest mapowany na [zestaw skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Każdy typ węzła jest odrębnym zestawem skalowania i może być niezależnie skalowany w górę lub w dół, mieć otwarte różne zestawy portów i mieć różne metryki pojemności. Aby uzyskać więcej informacji na temat relacji między typami węzłów i zestawami skalowania maszyn wirtualnych, jak włączyć protokół RDP w jednym z wystąpień, jak otworzyć nowe porty i tak dalej, zobacz [Service Fabric typów węzłów klastra](service-fabric-cluster-nodetypes.md).

Klaster Service Fabric może składać się z więcej niż jednego typu węzła. W tym zdarzeniu klaster składa się z jednego typu węzła podstawowego i jednego lub więcej typów węzłów innych niż podstawowe.

Typ pojedynczego węzła nie może być niezawodny w skali ponad 100 węzłów na zestaw skalowania maszyn wirtualnych dla aplikacji SF; Niezawodna większa niż 100 węzłów będzie wymagała dodania dodatkowych zestawów skalowania maszyn wirtualnych.

### <a name="primary-node-type"></a>Typ węzła podstawowego

Usługi systemowe Service Fabric (na przykład usługa Menedżera klastra lub usługa Magazyn obrazów) są umieszczane na podstawowym typie węzła. 

![Zrzut ekranu klastra z dwoma typami węzłów][SystemServices]

* **Minimalny rozmiar maszyn wirtualnych** dla typu węzła podstawowego jest określany na podstawie wybranej **warstwy trwałości** . Domyślna warstwa trwałości to brązowy. Aby uzyskać więcej informacji [, zobacz charakterystyki trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) .  
* **Minimalna liczba maszyn wirtualnych** dla typu węzła podstawowego jest określana na podstawie wybranej **warstwy niezawodności** . Domyślna warstwa niezawodności to Silver. Aby uzyskać więcej informacji [, zobacz Charakterystyka niezawodności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .  

Z szablonu Azure Resource Manager typ węzła podstawowego jest konfigurowany przy użyciu `isPrimary` atrybutu w obszarze [definicja typu węzła](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ węzła innego niż podstawowy

W klastrze z wieloma typami węzłów istnieje jeden typ węzła podstawowego, a reszta nie jest podstawowa.

* **Minimalny rozmiar maszyn wirtualnych** dla typów węzłów innych niż podstawowe jest określany na podstawie wybranej **warstwy trwałości** . Domyślna warstwa trwałości to brązowy. Aby uzyskać więcej informacji, zobacz [charakterystyki trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* **Minimalna liczba maszyn wirtualnych** dla typów węzłów innych niż podstawowe to jeden. Należy jednak wybrać tę wartość na podstawie liczby replik aplikacji/usług, które mają być uruchamiane w tym typie węzła. Liczbę maszyn wirtualnych w typie węzła można zwiększyć po wdrożeniu klastra.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakterystyki trwałości klastra
Warstwa trwałości służy do wskazywania, że system ma uprawnienia do programu z podstawową infrastrukturą platformy Azure. W przypadku typu węzła podstawowego to uprawnienie umożliwia Service Fabric wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny wirtualnej (takich jak ponowne uruchomienie maszyny wirtualnej, odtwarzanie maszyn wirtualnych lub migracja maszyny wirtualnej), które mają wpływ na wymagania dotyczące kworum usług systemowych i usług stanowych. W przypadku typów węzłów innych niż podstawowe to uprawnienie umożliwia Service Fabric wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny wirtualnej (takich jak ponowne uruchomienie maszyny wirtualnej, odtwarzanie maszyn wirtualnych i migracja maszyn wirtualnych), które mają wpływ na wymagania dotyczące kworum dla usług stanowych.

| Warstwa trwałości  | Wymagana minimalna liczba maszyn wirtualnych | Obsługiwane jednostki SKU maszyny wirtualnej                                                                  | Aktualizacje wprowadzane do zestawu skalowania maszyn wirtualnych                               | Aktualizacje i konserwacja zainicjowane przez platformę Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Złoty             | 5                              | Jednostki SKU w całym węźle przeznaczone dla pojedynczego klienta (na przykład L32s, GS5, G5, DS15_v2, D15_v2) | Może zostać opóźnione do zatwierdzenia przez klaster Service Fabric | Można wstrzymywać przez 2 godziny na UD w celu dodatkowego czasu na odzyskanie replik przed wcześniejszymi błędami |
| Srebrny           | 5                              | Maszyny wirtualne jednego rdzenia lub nowszego z co najmniej 50 GB lokalnego dysku SSD                      | Może zostać opóźnione do zatwierdzenia przez klaster Service Fabric | Nie może być opóźniony przez dowolny znaczny okres czasu                                                    |
| Bron           | 1                              | Maszyny wirtualne z co najmniej 50 GB lokalnego dysku SSD                                              | Nie zostanie opóźniony przez klaster Service Fabric           | Nie może być opóźniony przez dowolny znaczny okres czasu                                                    |

> [!WARNING]
> Typy węzłów działające z trwałością Bronze nie uzyskują _żadnych uprawnień_. Oznacza to, że zadania infrastruktury mające wpływ na obciążenia stanowe nie zostaną zatrzymane ani opóźnione, co może wpłynąć na Twoje obciążenia. Używaj tylko Bronze dla typów węzłów, które uruchamiają tylko obciążenia bezstanowe. W przypadku obciążeń produkcyjnych zaleca się używanie srebra lub wyższej. 
> 
> Niezależnie od dowolnego poziomu trwałości operacja [cofnięcia alokacji](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) w zestawie skalowania maszyn wirtualnych spowoduje zniszczenie klastra

**Zalety korzystania z poziomu trwałości Silver lub Gold**
 
- Zmniejsza liczbę wymaganych kroków operacji skalowania (to oznacza, że dezaktywacja węzła i polecenie Remove-ServiceFabricNodeState są wywoływane automatycznie).
- Zmniejsza ryzyko utraty danych z powodu zainicjowanej przez klienta operacji zmiany jednostki SKU maszyny wirtualnej lub operacji infrastruktury platformy Azure.

**Wady korzystania z poziomu trwałości Silver lub Gold**
 
- Wdrożenia do zestawu skalowania maszyn wirtualnych i innych powiązanych zasobów platformy Azure mogą być opóźnione, mogą przekroczyć limit czasu lub mogą być blokowane w całości przez problemy w klastrze lub na poziomie infrastruktury. 
- Zwiększa liczbę [zdarzeń związanych z cyklem życia repliki](service-fabric-reliable-services-lifecycle.md) (na przykład podstawowych wymian) z powodu zautomatyzowanej aktywacji węzłów podczas operacji infrastruktury platformy Azure.
- Pobiera węzły z usługi przez okres czasu, gdy są wykonywane aktualizacje oprogramowania platformy Azure lub działania związane z konserwacją sprzętu. W tych działaniach mogą pojawić się węzły o stanie wyłączania/wyłączania. Pozwala to na tymczasowe zmniejszenie pojemności klastra, ale nie ma to wpływu na dostępność klastra lub aplikacji.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Zalecenia dotyczące sytuacji, w których należy używać poziomu trwałości Silver lub Gold

Użyj trwałości Silver lub Gold dla wszystkich typów węzłów, które obsługują usługi stanowe, przewidywane skalowanie w górę (Zmniejsz liczbę wystąpień maszyn wirtualnych), a także wolisz, aby operacje wdrażania były opóźnione i zmniejszyć wydajność, aby uprościć te operacje skalowania w poziomie. Scenariusze skalowania w poziomie (Dodawanie wystąpień maszyn wirtualnych) nie są odtwarzane w wybranym obszarze warstwy trwałości, tylko w ramach skalowania w poziomie.

### <a name="changing-durability-levels"></a>Zmienianie poziomów trwałości
- Typów węzłów o poziomach trwałości Silver lub Gold nie można zmienić na starszą wersję.
- Uaktualnienie z wersji Bronze do Silver lub Gold może potrwać kilka godzin.
- W przypadku zmiany poziomu trwałości należy zaktualizować go zarówno w konfiguracji rozszerzenia Service Fabric w ramach zasobu zestawu skalowania maszyn wirtualnych, jak i w definicji typu węzła w zasobie klastra Service Fabric. Te wartości muszą być zgodne.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Zalecenia operacyjne dla typu węzła, który został ustawiony na poziom trwałości Silver lub Gold.

- Przechowuj klaster i aplikacje przez cały czas i upewnij się, że aplikacje odpowiadają na wszystkie [zdarzenia cyklu życia repliki usługi](service-fabric-reliable-services-lifecycle.md) (takie jak replika w kompilacji jest zablokowana) w odpowiednim czasie.
- Wdrażaj bezpieczniejsze metody zmiany jednostki SKU maszyny wirtualnej (skalowanie w górę/w dół): Zmiana jednostki SKU maszyny wirtualnej zestawu skalowania maszyn wirtualnych wymaga kilku kroków i kwestii. Oto proces, który można wykonać, aby uniknąć typowych problemów.
    - **Dla typów węzłów innych niż podstawowe:** Zaleca się utworzenie nowego zestawu skalowania maszyn wirtualnych, zmodyfikowanie ograniczenia umieszczania usługi w celu uwzględnienia nowego typu węzła zestawu skalowania maszyn wirtualnych, a następnie zredukowanie liczby wystąpień starego zestawu skalowania maszyn wirtualnych do zera, po jednym węźle (w tym celu należy upewnić się, że usunięcie węzłów nie ma wpływu na niezawodność klastra).
    - **Dla typu węzła podstawowego:** Jeśli wybrana jednostka SKU maszyny wirtualnej ma pojemność i chcesz zmienić ją na większą jednostkę SKU maszyny wirtualnej, postępuj zgodnie z naszymi wskazówkami dotyczącymi [skalowania w pionie dla typu węzła podstawowego](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Należy zachować minimalną liczbę pięciu węzłów dla dowolnego zestawu skalowania maszyn wirtualnych z włączonym poziomem trwałości Gold lub Silver.
- Każdy zestaw skalowania maszyn wirtualnych z poziomem trwałości Silver lub Gold musi być mapowany na własny typ węzła w klastrze Service Fabric. Mapowanie wielu zestawów skalowania maszyn wirtualnych na jeden typ węzła uniemożliwi prawidłowe działanie koordynacji między klastrem Service Fabric i infrastrukturą platformy Azure.
- Nie usuwaj losowych wystąpień maszyn wirtualnych, zawsze używaj funkcji skalowania w dół zestawu skalowania maszyn wirtualnych. Usuwanie losowych wystąpień maszyn wirtualnych ma potencjalne możliwości tworzenia nierównowagi w rozmieszczenia wystąpień maszyn wirtualnych w ramach UD i FD. Takie niezrównoważone może mieć negatywny wpływ na zdolność systemów do prawidłowego równoważenia obciążenia między wystąpieniami usługi/replikami usługi.
- Jeśli jest używana funkcja automatycznego skalowania, należy ustawić reguły, takie jak skalowanie w górę (usuwanie wystąpień maszyn wirtualnych), tylko jeden węzeł w danym momencie. Skalowanie w dół więcej niż jedno wystąpienie w czasie jest niebezpieczne.
- W przypadku usuwania lub cofania przydziału maszyn wirtualnych na podstawowym typie węzła nigdy nie należy zmniejszać liczby przyznanych maszyn wirtualnych poniżej tego, co jest wymagane w przypadku warstwy niezawodności. Te operacje będą blokowane w nieskończoność w zestawie skalowania z poziomem trwałości Silver lub Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Charakterystyka niezawodności klastra
Warstwa niezawodności służy do ustawiania liczby replik usług systemowych, które mają być uruchamiane w tym klastrze w typie węzła podstawowego. Im większa liczba replik, tym bardziej niezawodna usługa systemowa znajduje się w klastrze.  

Warstwa niezawodności może przyjmować następujące wartości:

* Platinum — uruchamianie usług systemowych z docelowym zestawem replik liczba dziewięciu
* Złoty — uruchamianie usług systemowych z docelową liczbą zestawów replik siedmiu
* Silver-uruchom usługi systemowe z liczbą docelowych zestawów replik równą pięć 
* Bronze — uruchomienie usług systemowych z docelową liczbą zestawów replik trzech

> [!NOTE]
> Wybrana warstwa niezawodności określa minimalną liczbę węzłów, które musi mieć typ węzła podstawowego. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Zalecenia dotyczące warstwy niezawodności

W przypadku zwiększenia lub zmniejszenia rozmiaru klastra (suma wystąpień maszyn wirtualnych we wszystkich typach węzłów) należy zaktualizować niezawodność klastra z jednej warstwy do innej. Spowoduje to wyzwolenie uaktualnień klastra wymaganych do zmiany liczby zestawów replik usług systemowych. Poczekaj na zakończenie uaktualniania przed wprowadzeniem jakichkolwiek innych zmian w klastrze, takich jak Dodawanie węzłów.  Postęp uaktualniania można monitorować na Service Fabric Explorer lub przez uruchomienie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Poniżej przedstawiono zalecenia dotyczące wyboru warstwy niezawodności.  Liczba węzłów inicjatora jest również ustawiona na minimalną liczbę węzłów dla warstwy niezawodności.  Na przykład w przypadku klastra z złotej niezawodności istnieje 7 węzłów inicjatora.

| **Liczba węzłów klastra** | **Warstwa niezawodności** |
| --- | --- |
| 1 |Nie określaj parametru warstwy niezawodności system oblicza |
| 3 |Bron |
| 5 lub 6|Srebrny |
| 7 lub 8 |Złoty |
| 9 i w górę |Platyn |

## <a name="primary-node-type---capacity-guidance"></a>Typ węzła podstawowego — wskazówki dotyczące pojemności

Poniżej przedstawiono wskazówki dotyczące planowania pojemności typu węzła podstawowego:

- **Liczba wystąpień maszyn wirtualnych do uruchamiania dowolnych obciążeń produkcyjnych na platformie Azure:** Należy określić minimalny rozmiar typu węzła podstawowego wynoszący 5 i warstwę niezawodności Silver.  
- **Liczba wystąpień maszyn wirtualnych do uruchomienia obciążeń testowych na platformie Azure** Można określić minimalny rozmiar typu węzła podstawowego wynoszący 1 lub 3. Klaster z jednym węzłem, działa z konfiguracją specjalną, dlatego skalowanie w poziomie tego klastra nie jest obsługiwane. Klaster z jednym węzłem nie ma niezawodności i dlatego w szablonie Menedżer zasobów musisz usunąć lub nie określić, że konfiguracja (Nieustawienie wartości konfiguracji jest za mało). W przypadku skonfigurowania klastra z jednym węzłem skonfigurowanego za pośrednictwem portalu, konfiguracja jest automatycznie traktowana. Jeden i trzy klastry węzłów nie są obsługiwane w przypadku uruchamiania obciążeń produkcyjnych. 
- **jednostka SKU maszyny wirtualnej:** Typ węzła podstawowego to miejsce, w którym uruchamiane są usługi systemowe, więc wybrana dla niego jednostka SKU maszyny wirtualnej musi uwzględniać ogólne obciążenie szczytowe, które planujesz umieścić w klastrze. Poniżej przedstawiono analogowe przedstawienie znaczenia, co tu znaczy — jako "płuc" można myśleć o typie węzła podstawowego, a tym samym jest to, co zapewnia tlen do mózgów, a więc mózg nie pobiera wystarczającej ilości tlenu. 

Ponieważ wymagania dotyczące pojemności klastra są określane przez obciążenie, które planujesz uruchomić w klastrze, nie możemy zapewnić jakościowej wskazówki dotyczącej konkretnego obciążenia, jednak poniżej przedstawiono szerokie wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych: 

- Zaleca się, aby przeznaczyć podstawowe NodeType klastrów na usługi systemowe, a następnie użyć ograniczeń umieszczania w celu wdrożenia aplikacji w pomocniczej elementów NodeType.
- Zalecana jednostka SKU maszyny wirtualnej jest standardowa D2_V2 lub równoważna z co najmniej 50 GB lokalnego dysku SSD.
- Minimalna obsługiwana wersja jednostki SKU maszyny wirtualnej to Standard_D2_V3 lub standardowa D1_V2 lub równoważna z co najmniej 50 GB lokalnego dysku SSD. 
- Nasze rekomendacje to co najmniej 50 GB. W przypadku obciążeń, zwłaszcza w przypadku korzystania z kontenerów systemu Windows, wymagane są większe dyski. 
- Częściowe podstawowe jednostki SKU maszyny wirtualnej, takie jak standardowa a0, nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane w przypadku obciążeń produkcyjnych ze względu na wydajność.
- Maszyny wirtualne o niskim priorytecie nie są obsługiwane.

> [!WARNING]
> Zmiana rozmiaru jednostki SKU maszyny wirtualnej węzła podstawowego w uruchomionym klastrze, jest operacją skalowania i udokumentowana w dokumentacji [skalowania w poziomie zestawu skalowania maszyn wirtualnych](virtual-machine-scale-set-scale-node-type-scale-out.md) .

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Wskazówki dotyczące typu węzła innego niż podstawowe w przypadku obciążeń stanowych

Te wskazówki dotyczą obciążeń stanowych przy użyciu [niezawodnych kolekcji usługi Service Fabric lub niezawodnych aktorów](service-fabric-choose-framework.md) , które są uruchamiane w typie węzła innego niż podstawowy.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są stanowe, zalecane jest uruchomienie ich z minimalną i docelową liczbą replik wynoszącą 5. Oznacza to, że w stanie stałym się z repliką (z zestawu replik) w każdej domenie błędów i domenie uaktualnienia. Cała koncepcja warstwy niezawodności dla typu węzła podstawowego umożliwia określenie tego ustawienia dla usług systemowych. To samo zagadnienie dotyczy również usług stanowych.

W przypadku obciążeń produkcyjnych minimalny zalecany rozmiar typu węzła innego niż podstawowy to 5, jeśli używasz obciążeń stanowych.

**jednostka SKU maszyny wirtualnej:** Jest to typ węzła, w którym działają usługi aplikacji, więc wybrana dla niego jednostka SKU maszyny wirtualnej musi uwzględniać szczytowe obciążenie, które planujesz umieścić w każdym węźle. Wymagania dotyczące pojemności typu węzła są określane przez obciążenie, które planujesz uruchomić w klastrze, dlatego nie możemy zapewnić jakościową wskazówkę dotyczącą konkretnego obciążenia, ale poniżej przedstawiono szerokie wskazówki ułatwiające rozpoczęcie pracy

Dla obciążeń produkcyjnych 

- Zalecana jednostka SKU maszyny wirtualnej jest standardowa D2_V2 lub równoważna z co najmniej 50 GB lokalnego dysku SSD.
- Minimalna obsługiwana wersja jednostki SKU maszyny wirtualnej to Standard_D2_V3 lub standardowa D1_V2 lub równoważna z co najmniej 50 GB lokalnego dysku SSD. 
- Częściowe podstawowe jednostki SKU maszyny wirtualnej, takie jak standardowa a0, nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane w przypadku obciążeń produkcyjnych ze względu na wydajność.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Wskazówki dotyczące typu węzła innego niż podstawowe dla bezstanowych obciążeń

Te wskazówki dotyczące obciążeń bezstanowych, które są uruchamiane w ramach typu węzła innego niż podstawowy.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są bezstanowe, minimalny obsługiwany typ węzła innego niż podstawowy to 2. Dzięki temu można uruchamiać dwa bezstanowe wystąpienia aplikacji i zezwalać usłudze na przegranie utraconego wystąpienia maszyny wirtualnej. 

**jednostka SKU maszyny wirtualnej:** Jest to typ węzła, w którym działają usługi aplikacji, więc wybrana dla niego jednostka SKU maszyny wirtualnej musi uwzględniać szczytowe obciążenie, które planujesz umieścić w każdym węźle. Wymagania dotyczące pojemności typu węzła są określane przez obciążenie planowane do uruchomienia w klastrze. Nie możemy zapewnić jakościowej wskazówki dotyczącej konkretnego obciążenia.  Oto szerokie wskazówki ułatwiające rozpoczęcie pracy.

Dla obciążeń produkcyjnych 

- Zalecana jednostka SKU maszyny wirtualnej jest standardowa D2_V2 lub równoważna. 
- Minimalna obsługiwana użycie jednostki SKU maszyny wirtualnej to standardowa D1 lub standardowa D1_V2 lub równoważne. 
- Częściowe podstawowe jednostki SKU maszyny wirtualnej, takie jak standardowa a0, nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane w przypadku obciążeń produkcyjnych ze względu na wydajność.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Następne kroki
Po zakończeniu planowania pojemności i skonfigurowaniu klastra zapoznaj się z następującymi tematami:

* [Service Fabric zabezpieczenia klastra](service-fabric-cluster-security.md)
* [Skalowanie klastra Service Fabric](service-fabric-cluster-scaling.md)
* [Planowanie odzyskiwania po awarii](service-fabric-disaster-recovery.md)
* [Relacja elementów NodeType z zestawem skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
