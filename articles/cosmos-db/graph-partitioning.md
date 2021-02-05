---
title: Partycjonowanie danych w Azure Cosmos DB interfejsie API Gremlin
description: Dowiedz się, jak używać wykresu partycjonowanego w Azure Cosmos DB. W tym artykule opisano również wymagania i najlepsze rozwiązania dotyczące wykresu partycjonowanego.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 91516c9815cfd71ffb59c399ea6580c6e28d8fce
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576468"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Używanie grafu podzielonego na partycje w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Jedną z najważniejszych funkcji interfejsu API Gremlin w Azure Cosmos DB jest możliwość obsługi wykresów o dużej skali za pomocą skalowania w poziomie. Kontenery mogą być skalowane niezależnie pod względem magazynu i przepływności. Możesz tworzyć kontenery w Azure Cosmos DB, które mogą być automatycznie skalowane w celu przechowywania danych grafu. Dane są automatycznie równoważone w oparciu o określony **klucz partycji**.

Partycjonowanie jest wykonywane wewnętrznie, jeśli oczekuje się, że rozmiar kontenera przekracza 20 GB lub jeśli chcesz przydzielić więcej niż 10 000 jednostek żądań na sekundę (jednostek ru). Dane są automatycznie partycjonowane na podstawie określonego klucza partycji. Klucz partycji jest wymagany w przypadku tworzenia kontenerów grafu na podstawie Azure Portal lub 3. x lub wyższych wersji sterowników Gremlin. Klucz partycji nie jest wymagany w przypadku używania 2. x lub niższych wersji sterowników Gremlin. 

Te same ogólne zasady dotyczące [mechanizmu partycjonowania Azure Cosmos DB](partitioning-overview.md) stosują się z kilkoma optymalizacjami specyficznymi dla wykresu opisanymi poniżej.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Partycjonowanie wykresu." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mechanizm partycjonowania wykresu

W poniższych wytycznych opisano sposób działania strategii partycjonowania w Azure Cosmos DB:

- **Zarówno wierzchołki, jak i krawędzie są przechowywane jako dokumenty JSON**.

- **Wierzchołki wymagają klucza partycji**. Ten klucz decyduje o tym, w której partycji wierzchołk będzie przechowywany za pomocą algorytmu wyznaczania wartości skrótu. Nazwa właściwości klucza partycji jest definiowana podczas tworzenia nowego kontenera i ma format: `/partitioning-key-name` .

- **Krawędzie będą przechowywane z wierzchołkiem źródła**. Inaczej mówiąc, dla każdego wierzchołka, jego klucz partycji definiuje, gdzie są przechowywane wraz z wychodzącymi krawędziami. Ta optymalizacja ma na celu uniknięcie zapytań między partycjami podczas korzystania z `out()` kardynalności w kwerendach grafu.

- **Krawędzie zawierają odwołania do wierzchołków, do których prowadzą punkty**. Wszystkie krawędzie są przechowywane z kluczami partycji i identyfikatorami wierzchołków, do których się znajdują. To obliczenie sprawia, że wszystkie `out()` zapytania kierunku zawsze są zapytaniem partycjonowanym z podziałem na partycje, a nie z nieślepą kwerendą między partycjami.

- **Zapytania programu Graph muszą określać klucz partycji**. Aby w pełni korzystać z partycjonowania poziomego w Azure Cosmos DB, należy określić klucz partycji w przypadku wybrania pojedynczego wierzchołka, gdy jest to możliwe. Poniżej przedstawiono zapytania dotyczące wybierania jednego lub wielu wierzchołków na wykresie partycjonowanym:

    - `/id` i `/label` nie są obsługiwane jako klucze partycji dla kontenera w interfejsie API Gremlin.


    - Wybierz wierzchołek według identyfikatora, a następnie **za pomocą `.has()` kroku, aby określić właściwość klucza partycji**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Wybieranie wierzchołka przez **określenie krotki, w tym wartości klucza partycji i identyfikatora**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Określanie **tablicy spójnych wartości kluczy partycji i identyfikatorów**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Wybieranie zestawu wierzchołków z ich identyfikatorami i **określanie listy wartości kluczy partycji**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Użycie **strategii partycji** na początku zapytania i określenie partycji dla zakresu pozostałej części zapytania Gremlin:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Najlepsze rozwiązania w przypadku używania wykresu partycjonowanego

Skorzystaj z poniższych wskazówek, aby zapewnić wydajność i skalowalność w przypadku używania wykresów partycjonowanych z nieograniczoną liczbą kontenerów:

- **Zawsze określaj wartość klucza partycji podczas wykonywania zapytania w wierzchołku**. Uzyskanie wierzchołka ze znanej partycji jest sposobem na osiągnięcie wydajności. Wszystkie kolejne operacje sąsiedztwa zawsze zostaną ograniczone do zakresu partycji, ponieważ krawędzie zawierają identyfikator odwołania i klucz partycji do ich wierzchołków docelowych.

- **Użyj kierunku wychodzącego podczas wykonywania zapytania o krawędzi wszędzie tam, gdzie jest to możliwe**. Jak wspomniano powyżej, krawędzie są przechowywane z wierzchołkami źródła w kierunku wychodzącym. W związku z tym szanse na przekroczenie zapytań między partycjami są zminimalizowane, gdy dane i zapytania są zaprojektowane z tym wzorcem. W przeciwieństwie do `in()` kwerendy zawsze będzie kosztowne zapytanie w wentylator.

- **Wybierz klucz partycji, który będzie równomiernie dystrybuowany dane między partycjami**. Ta decyzja jest silnie zależna od modelu danych rozwiązania. Przeczytaj więcej na temat tworzenia odpowiedniego klucza partycji na potrzeby [partycjonowania i skalowania w Azure Cosmos DB](partitioning-overview.md).

- **Optymalizuj zapytania, aby uzyskać dane w granicach partycji**. Optymalna strategia partycjonowania będzie wyrównana do wzorców zapytań. Zapytania, które uzyskują dane z pojedynczej partycji, zapewniają najlepszą możliwą wydajność.

## <a name="next-steps"></a>Następne kroki

Następnie możesz zapoznać się z następującymi artykułami:

* Dowiedz się więcej [na temat partycji i skalowania w Azure Cosmos DB](partitioning-overview.md).
* Dowiedz się więcej o [obsłudze Gremlin w interfejsie API Gremlin](gremlin-support.md).
* Dowiedz się więcej o [wprowadzeniu do interfejsu API Gremlin](graph-introduction.md).
