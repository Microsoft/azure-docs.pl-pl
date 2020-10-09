---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB
description: Dowiedz się więcej o tym, jak partycjonowanie działa w Azure Cosmos DB, jak skonfigurować partycjonowanie i klucze partycji oraz jak wybrać właściwy klucz partycji dla aplikacji.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 98cd28e8b770ebfb7ab395fbe7fff16a078e3529
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826851"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB

W tym artykule opisano relację między partycjami logicznymi a fizycznymi. Omówiono w nim również najlepsze rozwiązania dotyczące partycjonowania i udostępniają szczegółowy widok, w jaki sposób skalowanie w poziomie działa w Azure Cosmos DB. Nie trzeba zrozumieć tych wewnętrznych szczegółów, aby [wybrać klucz partycji](partitioning-overview.md#choose-partitionkey) , ale zostały one omówione, aby mieć świadomość, jak działa Azure Cosmos DB.

## <a name="logical-partitions"></a>Partycje logiczne

Partycja logiczna składa się z zestawu elementów, które mają ten sam klucz partycji. Na przykład w kontenerze zawierającym dane dotyczące żywienia żywności wszystkie elementy zawierają `foodGroup` Właściwość. Można użyć `foodGroup` jako klucza partycji dla kontenera. Grupy elementów, dla których określone wartości `foodGroup` , takie jak `Beef Products` , `Baked Products` , i `Sausages and Luncheon Meats` , tworzą odrębne partycje logiczne. Nie trzeba martwić się o usunięcie partycji logicznej, gdy dane podstawowe są usuwane.

Partycja logiczna definiuje również zakres transakcji bazy danych. Można aktualizować elementy w ramach partycji logicznej przy użyciu [transakcji z izolacją migawki](database-transactions-optimistic-concurrency.md). Gdy nowe elementy są dodawane do kontenera, nowe partycje logiczne są w sposób niewidoczny dla użytkownika tworzone przez system.

W kontenerze nie ma żadnego limitu liczby partycji logicznych. Każda partycja logiczna może przechowywać do 20 GB danych. Dobre opcje kluczy partycji mają szeroką gamę możliwych wartości. Na przykład w kontenerze, w którym wszystkie elementy zawierają `foodGroup` Właściwość, dane w ramach `Beef Products` partycji logicznej mogą rosnąć do 20 GB. [Wybór klucza partycji](partitioning-overview.md#choose-partitionkey) z szerokim zakresem możliwych wartości zapewnia możliwość skalowania kontenera.

## <a name="physical-partitions"></a>Partycje fizyczne

Kontener usługi Azure Cosmos jest skalowany przez dystrybuowanie danych i przepływności między partycjami fizycznymi. Wewnętrznie co najmniej jedna partycja logiczna jest mapowana na jedną partycję fizyczną. Większość małych kontenerów Cosmos ma wiele partycji logicznych, ale wymaga tylko pojedynczej partycji fizycznej. W przeciwieństwie do partycji logicznych, partycje fizyczne są wewnętrzną implementacją systemu i są w całości zarządzane przez Azure Cosmos DB.

Liczba partycji fizycznych w kontenerze Cosmos zależy od następujących czynników:

- Ilość zainicjowanej przepływności (każda pojedyncza partycja fizyczna może zapewnić przepustowość do 10 000 jednostek żądań na sekundę)
- Łączny magazyn danych (poszczególne partycje fizyczne mogą przechowywać do 50 GB)

Brak limitu całkowitej liczby partycji fizycznych w kontenerze. W miarę zwiększania się przepustowości lub rozmiaru danych Azure Cosmos DB automatycznie utworzy nowe partycje fizyczne, dzieląc istniejące. Podział partycji fizycznych nie ma wpływu na dostępność aplikacji. Po podziale partycji fizycznej wszystkie dane w jednej partycji logicznej nadal będą przechowywane na tej samej partycji fizycznej. Podział partycji fizycznej po prostu tworzy nowe mapowanie partycji logicznych na partycje fizyczne.

Obsługa przepływności dla kontenera jest dzielona równomiernie między partycjami fizycznymi. Projekt klucza partycji, który nie dystrybuuje żądań nawet może spowodować, że zbyt wiele żądań skierowanych do małego podzestawu partycji, które staną się "gorąca". Partycje gorącą prowadzą do niewydajnego korzystania z zainicjowanej przepływności, co może spowodować zmniejszenie stawki i zwiększenie kosztów.

Partycje fizyczne kontenera można zobaczyć w sekcji **Magazyn** w **bloku metryki** Azure Portal:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Wyświetlanie liczby partycji fizycznych" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

W tym przykładowym kontenerze, który został wybrany `/foodGroup` jako nasz klucz partycji, każdy z trzech prostokątów reprezentuje partycję fizyczną. W obrazie **zakres kluczy partycji** jest taki sam jak w przypadku partycji fizycznej. Wybrana partycja fizyczna zawiera trzy partycje logiczne: `Beef Products` , `Vegetable and Vegetable Products` i `Soups, Sauces, and Gravies` .

W przypadku udostępnienia przepływności 18 000 jednostek żądań na sekundę (RU/s), każda z trzech partycji fizycznych może korzystać z 1/3 całkowitej alokowanej przepływności. W ramach wybranej partycji fizycznej klucze logicznej partycji, `Beef Products` `Vegetable and Vegetable Products` i `Soups, Sauces, and Gravies` mogą wspólnie korzystać z 6 000 o zainicjowaniu jednostki ru na partycji fizycznej. Ponieważ zainicjowana przepływność jest równomiernie podzielona na partycje fizyczne kontenera, należy wybrać klucz partycji, który równomiernie dystrybuuje zużycie przepływności przez wybranie odpowiedniego [klucza partycji logicznej](partitioning-overview.md#choose-partitionkey). W przypadku wybrania klucza partycji, który równomiernie dystrybuuje zużycie przepływności między partycjami logicznymi, należy zapewnić zrównoważenie zużycia przepływności w ramach partycji fizycznych.

## <a name="replica-sets"></a>Zestawy replik

Każda partycja fizyczna składa się z zestawu replik, nazywanych również [*zestawem replik*](global-dist-under-the-hood.md). Każdy zestaw replik jest hostem wystąpienia aparatu bazy danych Azure Cosmos. Zestaw replik sprawia, że dane przechowywane w partycji fizycznej są trwałe, wysoce dostępne i spójne. Każda replika tworząca partycję fizyczną dziedziczy przydział magazynowania partycji. Wszystkie repliki partycji fizycznej w sposób zbiorczy obsługują przepływność przydzieloną do partycji fizycznej. Azure Cosmos DB automatycznie zarządza zestawami replik.

Większość małych kontenerów Cosmos wymaga tylko pojedynczej partycji fizycznej, ale nadal będzie można korzystać z co najmniej 4 replik.

Na poniższej ilustracji przedstawiono, jak partycje logiczne są mapowane na partycje fizyczne dystrybuowane globalnie:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Wyświetlanie liczby partycji fizycznych" border="false":::

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat wybierania klucza partycji](partitioning-overview.md#choose-partitionkey).
* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
