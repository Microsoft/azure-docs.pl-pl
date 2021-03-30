---
title: Planowanie pojemności dla aplikacji Service Fabric
description: Opisuje sposób identyfikowania liczby węzłów obliczeniowych wymaganych dla aplikacji Service Fabric
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75377212"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planowanie pojemności dla aplikacji Service Fabric
W tym dokumencie przedstawiono sposób oszacowania ilości zasobów (procesor CPU, pamięć RAM, magazyn dyskowy) potrzebnych do uruchamiania aplikacji Service Fabric platformy Azure. Często wymagania dotyczące zasobów mogą ulec zmianie w czasie. Zwykle wymaga to kilku zasobów podczas opracowywania lub testowania usługi, a następnie wymaga większej ilości zasobów podczas pracy w środowisku produkcyjnym, a aplikacja rośnie w popularności. Podczas projektowania aplikacji należy wziąć pod uwagę długoterminowe wymagania i wprowadzić opcje, które umożliwiają skalowanie usługi w celu sprostania wysokim zapotrzebowaniem klienta.

 Podczas tworzenia klastra Service Fabric należy zdecydować, jakie rodzaje maszyn wirtualnych tworzą klaster. Każda maszyna wirtualna obejmuje ograniczoną ilość zasobów w postaci procesorów CPU (rdzenie i szybkości), przepustowości sieci, pamięci RAM i magazynu dyskowego. Gdy usługa rośnie wraz z upływem czasu, możesz przeprowadzić uaktualnienie do maszyn wirtualnych, które oferują większe zasoby i/lub dodając więcej maszyn wirtualnych do klastra. Aby zrobić to ostatnie, należy najpierw utworzyć architekt usługi, aby móc korzystać z nowych maszyn wirtualnych, które są dynamicznie dodawane do klastra.

Niektóre usługi zarządzają niewielką ilością danych na maszynach wirtualnych. W związku z tym planowanie pojemności dla tych usług powinno być skoncentrowane głównie na wydajności, co oznacza wybranie odpowiednich procesorów CPU (rdzeni i szybkości) maszyn wirtualnych. Ponadto należy rozważyć przepustowość sieci, w tym częstotliwość wykonywania transferów sieci i ilości przesyłanych danych. Jeśli usługa musi działać dobrze, jak zwiększa się użycie usługi, można dodać więcej maszyn wirtualnych do klastra i obciążenie sieci na wszystkich maszynach wirtualnych.

W przypadku usług, które zarządzają dużymi ilościami danych na maszynach wirtualnych, planowanie pojemności powinno być skoncentrowane głównie na rozmiarze. W ten sposób należy uważnie rozważyć pojemność pamięci RAM i pamięci masowej maszyny wirtualnej. System zarządzania pamięcią wirtualną w systemie Windows sprawia, że miejsce na dysku będzie wyglądać podobnie do kodu aplikacji. Ponadto środowisko uruchomieniowe Service Fabric zapewnia inteligentne stronicowanie, dzięki czemu można tylko gorącą pamięć i przenosić zimne dane na dysk. Aplikacje mogą w ten sposób używać więcej pamięci niż jest to fizycznie dostępne na maszynie wirtualnej. Większa ilość pamięci RAM po prostu zwiększa wydajność, ponieważ maszyna wirtualna może utrzymywać więcej miejsca w pamięci RAM. Wybrana maszyna wirtualna powinna mieć wystarczająco duży dysk, aby można było przechowywać dane na maszynie wirtualnej. Podobnie maszyna wirtualna powinna mieć wystarczającą ilość pamięci RAM, aby zapewnić odpowiednią wydajność. Jeśli dane usługi rośnie wraz z upływem czasu, można dodać więcej maszyn wirtualnych do klastra i podzielić dane na wszystkie maszyny wirtualne.

## <a name="determine-how-many-nodes-you-need"></a>Określ liczbę potrzebnych węzłów
Partycjonowanie usługi pozwala na skalowanie danych usługi. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie Service Fabric](service-fabric-concepts-partitioning.md). Każda partycja musi być zgodna z jedną maszyną wirtualną, ale wiele partycji (małych) może być umieszczanych na jednej maszynie wirtualnej. Dlatego, że więcej małych partycji zapewnia większą elastyczność niż w przypadku kilku większych partycji. W ten sposób jest to, że duże ilości partycji zwiększają obciążenie Service Fabric i nie można wykonywać operacji transakcyjnych w różnych partycjach. Istnieje również większy ruch sieciowy, jeśli kod usługi często potrzebuje dostępu do fragmentów danych, które znajdują się w różnych partycjach. Podczas projektowania usługi należy uważnie rozważyć te specjaliści i wady, aby dotrzeć do skutecznej strategii partycjonowania.

Załóżmy, że aplikacja ma jedną usługę stanową, która ma rozmiar magazynu, który powinien zostać powiększony do DB_Size GB w roku. Użytkownik chce dodać więcej aplikacji (i partycji) w miarę wzrostu tego roku.  Współczynnik replikacji (RF), który określa liczbę replik dla usługi ma wpływ na łączną DB_Size. Łączna DB_Size wszystkich replik to współczynnik replikacji pomnożony przez DB_Size.  Node_Size reprezentuje miejsce na dysku/pamięć RAM dla każdego węzła, który ma być używany przez usługę. Aby uzyskać najlepszą wydajność, DB_Size powinien mieścić się w pamięci w klastrze, a następnie należy wybrać Node_Size, który należy do pamięci RAM maszyny wirtualnej. Przydzielenie Node_Size, który jest większy niż pojemność pamięci RAM, polega na stronicowaniu udostępnianym przez środowisko uruchomieniowe Service Fabric. W związku z tym wydajność może nie być optymalna, jeśli całe dane są uważane za gorącą (od momentu, gdy dane są stronicowane/wychodzące). Jednak w przypadku wielu usług, w których tylko część danych jest gorąca, jest bardziej opłacalna.

Liczba węzłów wymaganych do maksymalnej wydajności można obliczyć w następujący sposób:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Konto do wzrostu
Możesz chcieć obliczyć liczbę węzłów na podstawie DB_Size, do której będzie się zwiększać, do DB_Size, w którym się zaczynasz. Następnie Zwiększ liczbę węzłów w miarę rozwoju usługi, aby nie przekroczyć aprowizacji liczby węzłów. Jednak liczba partycji powinna być oparta na liczbie węzłów, które są potrzebne, gdy korzystasz z usługi przy maksymalnym wzroście.

W dowolnym momencie warto udostępnić kilka dodatkowych maszyn, aby można było obsłużyć wszelkie nieoczekiwane skoki lub błędy (na przykład jeśli kilka maszyn wirtualnych zostanie zamkniętych).  Mimo że dodatkowa pojemność powinna zostać ustalona przy użyciu oczekiwanych skoków, punkt początkowy ma zarezerwować kilka dodatkowych maszyn wirtualnych (5-10 procent dodatkowych).

Powyższym założono, że jest to jedna usługa stanowa. Jeśli masz więcej niż jedną usługę stanową, musisz dodać DB_Size skojarzoną z innymi usługami do równania. Alternatywnie można obliczyć liczbę węzłów oddzielnie dla każdej usługi stanowej.  Usługa może mieć repliki lub partycje, które nie są zrównoważone. Należy pamiętać, że partycje mogą także mieć więcej danych niż inne. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [artykuł partycjonowanie na temat najlepszych](service-fabric-concepts-partitioning.md)rozwiązań. Jednak poprzednie równanie to partycja i replika niezależny od, ponieważ Service Fabric zapewnia, że repliki są rozłożone między węzłami w sposób zoptymalizowany.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Używanie arkusza kalkulacyjnego do obliczania kosztów
Teraz Umieśćmy pewne liczby rzeczywiste w formule. [Przykład arkusza kalkulacyjnego](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) pokazuje, jak zaplanować pojemność aplikacji, która zawiera trzy typy obiektów danych. Dla każdego obiektu przybliżony rozmiar i liczba obiektów, których oczekujemy. Wybieramy również liczbę replik dla każdego typu obiektu. Arkusz kalkulacyjny oblicza łączną ilość pamięci, która ma być przechowywana w klastrze.

Następnie wprowadzimy rozmiar maszyny wirtualnej i miesięczny koszt. W zależności od rozmiaru maszyny wirtualnej, arkusz kalkulacyjny informuje o minimalnej liczbie partycji, których należy użyć, aby podzielić dane na fizycznie dopasowane do węzłów. Może być potrzebna większa liczba partycji w celu uwzględnienia konkretnych potrzeb związanych z obliczaniem i ruchem sieciowym aplikacji. W arkuszu kalkulacyjnym jest wyświetlana liczba partycji, w których zarządzanie obiektami profilu użytkownika zostało zwiększone z jednego do sześciu.

Teraz w oparciu o wszystkie te informacje, arkusz kalkulacyjny pokazuje, że można fizycznie uzyskać wszystkie dane z żądanymi partycjami i replikami w klastrze z 26 węzłami. Jednak ten klaster będzie gęsto spakowany, dzięki czemu niektóre dodatkowe węzły mogą obsługiwać awarie i uaktualnienia węzła. Arkusz kalkulacyjny pokazuje również, że posiadanie więcej niż 57 węzłów nie zapewnia żadnych dodatkowych wartości, ponieważ węzły są puste. Ponownie możesz chcieć znaleźć ponad 57 węzłów mimo to, aby obsłużyć awarie i uaktualnienia węzła. Arkusz kalkulacyjny można dostosować tak, aby był zgodny z konkretnymi potrzebami aplikacji.   

![Arkusz kalkulacyjny dla obliczeń kosztów][Image1]

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [partycjonowaniem Service Fabric usługach][10] , aby dowiedzieć się więcej na temat partycjonowania usługi.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
