---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Informacje na temat partycjonowania, logicznego, partycji fizycznych w Azure Cosmos DB, najlepszych rozwiązań w przypadku wybierania klucza partycji oraz zarządzania partycjami logicznymi
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: ab1b7028ce5f1afef861e696c98f25b56e78ef36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772471"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB używa partycjonowania do skalowania poszczególnych kontenerów w bazie danych w celu spełnienia wymagań dotyczących wydajności aplikacji. W przypadku partycjonowania elementy w kontenerze są podzielone na odrębne podzestawy o nazwie *partycje logiczne*. Partycje logiczne są tworzone na podstawie wartości *klucza partycji* , który jest skojarzony z każdym elementem w kontenerze. Wszystkie elementy w partycji logicznej mają tę samą wartość klucza partycji.

Na przykład kontener zawiera elementy. Każdy element ma unikatową wartość `UserID` właściwości. Jeśli `UserID` program służy jako klucz partycji dla elementów w kontenerze, a istnieją 1 000 wartości unikatowych `UserID` , dla kontenera są tworzone partycje logiczne 1 000.

Oprócz klucza partycji, który określa partycję logiczną elementu, każdy element w kontenerze ma *Identyfikator elementu* (unikatowy w obrębie partycji logicznej). Połączenie klucza partycji i *identyfikatora elementu* tworzy *indeks* elementu, który jednoznacznie identyfikuje element. [Wybór klucza partycji](#choose-partitionkey) to ważna decyzja, która będzie miała wpływ na wydajność aplikacji.

W tym artykule opisano relację między partycjami logicznymi a fizycznymi. Omówiono w nim również najlepsze rozwiązania dotyczące partycjonowania i udostępniają szczegółowy widok, w jaki sposób skalowanie w poziomie działa w Azure Cosmos DB. Nie trzeba zrozumieć tych wewnętrznych szczegółów, aby wybrać klucz partycji, ale zostały one omówione, aby mieć świadomość, jak działa Azure Cosmos DB.

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna składa się z zestawu elementów, które mają ten sam klucz partycji. Na przykład w kontenerze zawierającym dane dotyczące żywienia żywności wszystkie elementy zawierają `foodGroup` Właściwość. Można użyć `foodGroup` jako klucza partycji dla kontenera. Grupy elementów, dla których określone wartości `foodGroup` , takie jak `Beef Products` , `Baked Products` , i `Sausages and Luncheon Meats` , tworzą odrębne partycje logiczne. Nie trzeba martwić się o usunięcie partycji logicznej, gdy dane podstawowe są usuwane.

Partycja logiczna definiuje również zakres transakcji bazy danych. Można aktualizować elementy w ramach partycji logicznej przy użyciu [transakcji z izolacją migawki](database-transactions-optimistic-concurrency.md). Gdy nowe elementy są dodawane do kontenera, nowe partycje logiczne są w sposób niewidoczny dla użytkownika tworzone przez system.

W kontenerze nie ma żadnego limitu liczby partycji logicznych. Każda partycja logiczna może przechowywać do 20 GB danych. Dobre opcje kluczy partycji mają szeroką gamę możliwych wartości. Na przykład w kontenerze, w którym wszystkie elementy zawierają `foodGroup` Właściwość, dane w ramach `Beef Products` partycji logicznej mogą rosnąć do 20 GB. [Wybór klucza partycji](#choose-partitionkey) z szerokim zakresem możliwych wartości zapewnia możliwość skalowania kontenera.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener jest skalowany przez dystrybuowanie danych i przepływności między partycjami fizycznymi. Wewnętrznie co najmniej jedna partycja logiczna jest mapowana na jedną partycję fizyczną. Zazwyczaj mniejsze kontenery mają wiele partycji logicznych, ale wymagają tylko pojedynczej partycji fizycznej. W przeciwieństwie do partycji logicznych, partycje fizyczne są wewnętrzną implementacją systemu i są w całości zarządzane przez Azure Cosmos DB.

Liczba partycji fizycznych w kontenerze zależy od następujących elementów:

* Liczba zainicjowanych przepływności (dla każdej pojedynczej partycji fizycznej można zapewnić przepustowość do 10 000 jednostek żądań na sekundę).
* Łączny magazyn danych (poszczególne partycje fizyczne mogą przechowywać do 50 GB danych).

> [!NOTE]
> Partycje fizyczne są wewnętrzną implementacją systemu i są w całości zarządzane przez Azure Cosmos DB. Podczas opracowywania rozwiązań nie należy skoncentrować się na partycjach fizycznych, ponieważ nie można ich kontrolować. Zamiast tego należy skoncentrować się na kluczach partycji. W przypadku wybrania klucza partycji, który równomiernie dystrybuuje zużycie przepływności między partycjami logicznymi, należy zapewnić zrównoważenie zużycia przepływności w ramach partycji fizycznych.

Brak limitu całkowitej liczby partycji fizycznych w kontenerze. W miarę zwiększania się przepustowości lub rozmiaru danych Azure Cosmos DB automatycznie utworzy nowe partycje fizyczne, dzieląc istniejące. Podział partycji fizycznych nie ma wpływu na dostępność aplikacji. Po podziale partycji fizycznej wszystkie dane w jednej partycji logicznej nadal będą przechowywane na tej samej partycji fizycznej. Podział partycji fizycznej po prostu tworzy nowe mapowanie partycji logicznych na partycje fizyczne.

Obsługa przepływności dla kontenera jest dzielona równomiernie między partycjami fizycznymi. Projekt klucza partycji, który nie dystrybuuje żądań nawet może spowodować, że zbyt wiele żądań skierowanych do małego podzestawu partycji, które staną się "gorąca". Partycje gorącą prowadzą do niewydajnego korzystania z zainicjowanej przepływności, co może spowodować zmniejszenie stawki i zwiększenie kosztów.

Partycje fizyczne kontenera można zobaczyć w sekcji **Magazyn** w **bloku metryki** Azure Portal:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Wyświetlanie liczby partycji fizycznych" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Na powyższym zrzucie ekranu kontener ma `/foodGroup` jako klucz partycji. Każdy z trzech słupków na grafie reprezentuje partycję fizyczną. W obrazie **zakres kluczy partycji** jest taki sam jak w przypadku partycji fizycznej. Wybrana partycja fizyczna zawiera pierwsze trzy partycje logiczne o najbardziej znaczącym rozmiarze: `Beef Products` , `Vegetable and Vegetable Products` , i `Soups, Sauces, and Gravies` .

W przypadku udostępnienia przepływności 18 000 jednostek żądań na sekundę (RU/s), każda z tych trzech partycji fizycznych może korzystać z 1/3 całkowitej alokowanej przepływności. W ramach wybranej partycji fizycznej klucze logicznej partycji, `Beef Products` `Vegetable and Vegetable Products` i `Soups, Sauces, and Gravies` mogą wspólnie korzystać z 6 000 o zainicjowaniu jednostki ru na partycji fizycznej. Ponieważ zainicjowana przepływność jest równomiernie podzielona na partycje fizyczne kontenera, należy wybrać klucz partycji, który równomiernie dystrybuuje zużycie przepływności przez wybranie odpowiedniego [klucza partycji logicznej](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Zarządzanie partycjami logicznymi

Azure Cosmos DB w sposób przezroczysty i automatycznie zarządza umieszczaniem partycji logicznych w partycjach fizycznych, aby skutecznie spełnić wymagania dotyczące skalowalności i wydajności kontenera. W miarę wzrostu wymagań dotyczących przepływności i magazynowania aplikacji Azure Cosmos DB przenosi partycje logiczne, aby automatycznie rozłożyć obciążenie na większą liczbę partycji fizycznych. Więcej informacji o [partycjach fizycznych](partitioning-overview.md#physical-partitions)można znaleźć w części.

Azure Cosmos DB używa partycjonowania opartego na skrócie, aby rozłożyć partycje logiczne między partycjami fizycznymi. Azure Cosmos DB skrót wartości klucza partycji elementu. Wynik zmieszany Określa partycję fizyczną. Następnie Azure Cosmos DB przydzieli kluczowe miejsce skrótów kluczy partycji równomiernie między partycjami fizycznymi.

Transakcje (w procedurach składowanych lub wyzwalaczach) są dozwolone tylko dla elementów w pojedynczej partycji logicznej.

## <a name="replica-sets"></a>Zestawy replik

Każda partycja fizyczna składa się z zestawu replik, nazywanych również [*zestawem replik*](global-dist-under-the-hood.md). Każdy zestaw replik obsługuje wystąpienie aparatu bazy danych. Zestaw replik sprawia, że dane przechowywane w partycji fizycznej są trwałe, wysoce dostępne i spójne. Każda replika tworząca partycję fizyczną dziedziczy przydział magazynowania partycji. Wszystkie repliki partycji fizycznej w sposób zbiorczy obsługują przepływność przydzieloną do partycji fizycznej. Azure Cosmos DB automatycznie zarządza zestawami replik.

Zazwyczaj mniejsze kontenery wymagają tylko pojedynczej partycji fizycznej, ale nadal będą mieć co najmniej 4 repliki.

Na poniższej ilustracji przedstawiono, jak partycje logiczne są mapowane na partycje fizyczne dystrybuowane globalnie:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Obraz, który demonstruje Azure Cosmos DB partycjonowanie" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Wybieranie klucza partycji

Klucz partycji ma dwa składniki: **ścieżkę klucza partycji** i **wartość klucza partycji**. Rozważmy na przykład element {"userId": "Andrew", "worksFor": "Microsoft"} Jeśli wybierzesz "userId" jako klucz partycji, poniżej przedstawiono dwa składniki kluczy partycji:

* Ścieżka klucza partycji (na przykład: "/userId"). Ścieżka klucza partycji akceptuje znaki alfanumeryczne i podkreślenia (_). Można również użyć zagnieżdżonych obiektów przy użyciu notacji ścieżki standardowej (/).

* Wartość klucza partycji (na przykład: "Andrew"). Wartość klucza partycji może być typu String lub numeric.

Aby dowiedzieć się więcej na temat limitów przepływności, magazynu i długości klucza partycji, zobacz artykuł dotyczący [przydziałów usługi Azure Cosmos DB](concepts-limits.md) .

Wybór klucza partycji to prosty, ale istotny wybór w Azure Cosmos DB. Po wybraniu klucza partycji nie można go zmienić w miejscu. Jeśli musisz zmienić klucz partycji, Przenieś dane do nowego kontenera przy użyciu nowego żądanego klucza partycji.

W przypadku **wszystkich** kontenerów klucz partycji powinien:

* Należy do właściwości, która ma wartość, która nie jest zmieniana. Jeśli właściwość jest kluczem partycji, nie można zaktualizować wartości tej właściwości.

* Ma wysoką kardynalność. Innymi słowy właściwość powinna mieć szeroką gamę możliwych wartości.

* Rozproszone użycie jednostek żądań (RU) i magazyn danych przez wszystkie partycje logiczne. Zapewnia to nawet użycie RU i dystrybucję magazynu dla partycji fizycznych.

Jeśli potrzebujesz [wieloelementowych transakcji kwasowych](database-transactions-optimistic-concurrency.md#multi-item-transactions) w Azure Cosmos DB, musisz użyć [procedur składowanych i wyzwalaczy](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Wszystkie procedury składowane oparte na języku JavaScript i wyzwalacze są objęte zakresem pojedynczej partycji logicznej.

## <a name="partition-keys-for-read-heavy-containers"></a>Klucze partycji dla kontenerów z dużą ilością odczytu

W przypadku większości kontenerów powyższe kryteria należy wziąć pod uwagę podczas wybierania klucza partycji. W przypadku dużych kontenerów z możliwością odczytu można jednak wybrać klucz partycji, który często pojawia się jako filtr w zapytaniach. Zapytania mogą być [efektywnie kierowane tylko do odpowiednich partycji fizycznych](how-to-query-container.md#in-partition-query) poprzez dołączenie klucza partycji do predykatu filtru.

Jeśli większość żądań obciążeń jest kwerendami i większość zapytań ma filtr równości dla tej samej właściwości, ta właściwość może być dobrym kluczem partycji. Na przykład, jeśli często uruchamiasz zapytanie, które jest filtrowane `UserID` , a następnie wybranie `UserID` klucza partycji zmniejsza liczbę [zapytań między partycjami](how-to-query-container.md#avoiding-cross-partition-queries).

Jeśli jednak kontener jest mały, prawdopodobnie nie masz wystarczającej liczby partycji fizycznych, aby nie trzeba było zajmować się wpływem na wydajność zapytań między partycjami. Większość małych kontenerów w Azure Cosmos DB wymaga tylko jednej lub dwóch partycji fizycznych.

Jeśli kontener może się zwiększać do więcej niż kilku partycji fizycznych, należy upewnić się, że wybierasz klucz partycji, który minimalizuje zapytania między partycjami. Kontener będzie wymagał więcej niż kilku partycji fizycznych, jeśli spełniony jest jeden z następujących warunków:

* Kontener będzie miał ponad 30 000 RU

* Kontener będzie przechowywać ponad 100 GB danych

## <a name="using-item-id-as-the-partition-key"></a>Używanie identyfikatora elementu jako klucza partycji

Jeśli kontener ma właściwość, która ma szeroką gamę możliwych wartości, prawdopodobnie jest wybór doskonałej partycji. Jednym z możliwych przykładowych właściwości jest *Identyfikator elementu*. W przypadku małych kontenerów z możliwością odczytu i dużych kontenerów o dowolnym rozmiarze *Identyfikator elementu* jest naturalnie doskonałym wyborem dla klucza partycji.

*Identyfikator elementu* właściwości systemu istnieje w każdym elemencie w kontenerze. Mogą istnieć inne właściwości reprezentujące identyfikator logiczny elementu. W wielu przypadkach są one również doskonałymi opcjami kluczy partycji z tego samego powodu, co *Identyfikator elementu*.

*Identyfikator elementu* to doskonały wybór klucza partycji z następujących powodów:

* Istnieje szeroki zakres możliwych wartości (jeden unikatowy *Identyfikator elementu* dla każdego elementu).
* Ponieważ istnieje unikatowy *Identyfikator elementu* dla każdego elementu, *Identyfikator elementu* wykonuje doskonałe zadanie w przypadku równomiernego zrównoważenia zużycia ru i magazynu danych.
* Możesz łatwo wykonywać efektywne odczyty punktów, ponieważ zawsze znasz klucz partycji elementu, jeśli znasz jego *Identyfikator elementu*.

Niektóre kwestie, które należy wziąć pod uwagę podczas wybierania *identyfikatora elementu* jako klucza partycji, obejmują:

* Jeśli *Identyfikator elementu* jest kluczem partycji, będzie on unikatowym identyfikatorem w całym kontenerze. Nie będzie możliwe posiadanie elementów mających zduplikowany *Identyfikator elementu*.
* Jeśli masz kontener do odczytu, który ma wiele [partycji fizycznych](partitioning-overview.md#physical-partitions), zapytania będą bardziej wydajne, jeśli mają filtr równości z *identyfikatorem elementu*.
* Nie można uruchomić procedur składowanych ani wyzwalaczy na wielu partycjach logicznych.

## <a name="next-steps"></a>Następne kroki

* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
