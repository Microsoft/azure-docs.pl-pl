---
title: Azure Cosmos DB zgodność Gremlin z funkcjami TinkerPop
description: Informacje o problemach ze zgodnością aparatu grafu dokumentacji
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092511"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB zgodność Gremlin
Aparat Azure Cosmos DB Graph jest ściśle opisany w specyfikacji kroków przechodzenia [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale istnieją różnice w implementacji, które są specyficzne dla Azure Cosmos DB. Aby zapoznać się z listą obsługiwanych kroków Gremlin, zobacz artykuł [Obsługa protokołu sieci interfejsu API Gremlin](gremlin-support.md) .

## <a name="behavior-differences"></a>Różnice w zachowaniu

* Aparat grafu Azure Cosmos DB uruchamia przechodzenie w ***pierwszej kolejności*** , podczas gdy TinkerPop Gremlin jest głębokością. Takie zachowanie zapewnia lepszą wydajność w skalowalnym systemie, takim jak Cosmos DB. 

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

* ***[Kod bajtowy języka Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** to specyfikacja przechodzenia przez graf niezależna od języka programowania. Program Cosmos DB Graph jeszcze nie obsługuje tego programu. Użyj `GremlinClient.SubmitAsync()` i przekaż przechodzenie jako ciąg tekstowy.

* ***`property(set, 'xyz', 1)`*** ustawienie Kardynalność nie jest obecnie obsługiwane. Zamiast tego użyj polecenia cmdlet `property(list, 'xyz', 1)`. Aby dowiedzieć się więcej, zobacz [Właściwości wierzchołka z TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* Ten *** `match()` krok*** nie jest obecnie dostępny. Ten krok zapewnia możliwości deklaracyjnego wykonywania zapytań.

* ***Obiekty jako właściwości*** dla wierzchołków lub krawędzi nie są obsługiwane. Właściwości mogą być tylko typami pierwotnymi lub tablicami.

* ***Sortowanie według właściwości tablicy*** `order().by(<array property>)` nie jest obsługiwana. Sortowanie jest obsługiwane tylko według typów pierwotnych.

* ***Niepierwotne typy JSON*** nie są obsługiwane. Użyj `string` `number` typów,, lub `true` / `false` . `null` wartości nie są obsługiwane. 

* Serializator ***GraphSONv3*** nie jest obecnie obsługiwany. `GraphSONv2`W konfiguracji połączenia użyj klas serializatorów, czytników i składników zapisywania. Wyniki zwrócone przez interfejs API Azure Cosmos DB Gremlin nie mają takiego samego formatu jak format GraphSON. 

* **Wyrażenia lambda i funkcje** nie są obecnie obsługiwane. Obejmuje to `.map{<expression>}` `.by{<expression>}` funkcje, i `.filter{<expression>}` . Aby dowiedzieć się więcej, i dowiedzieć się, jak ponownie napisać je za pomocą kroków Gremlin, zobacz [uwagi na temat wyrażeń lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakcje*** nie są obsługiwane z powodu rozproszonego charakteru systemu.  Skonfiguruj odpowiedni model spójności na koncie Gremlin na "odczytywanie własnych zapisów" i Użyj optymistycznej współbieżności, aby rozwiązać konflikty zapisów.

## <a name="known-limitations"></a>Znane ograniczenia

* **Użycie indeksu dla zapytań Gremlin z `.V()` etapami przechodzenia do częściowej**: obecnie tylko pierwsze `.V()` wywołanie przechodzenia spowoduje użycie indeksu w celu rozpoznania dowolnych filtrów lub predykatów, do których dołączono. Kolejne wywołania nie zapoznają się z indeksem, co może spowodować wydłużenie czasu oczekiwania i kosztów zapytania.
    
    Przy założeniu domyślnego indeksowania, typowe zapytanie Gremlin odczytu, które rozpoczyna się od `.V()` kroku, będzie używać parametrów w dołączonych krokach filtrowania, takich jak `.has()` lub `.where()` w celu optymalizacji kosztu i wydajności zapytania. Na przykład:

    ```java
    g.V().has('category', 'A')
    ```

    Jednak `.V()` w przypadku dołączenia więcej niż jednego kroku do zapytania Gremlin rozpoznawanie danych dla zapytania może nie być optymalne. Wykonaj następujące zapytanie jako przykład:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    To zapytanie zwróci dwie grupy wierzchołków na podstawie ich właściwości o nazwie `category` . W takim przypadku tylko pierwsze wywołanie `g.V().has('category', 'A')` spowoduje użycie indeksu do rozpoznawania wierzchołków na podstawie wartości właściwości.

    Obejście tego zapytania polega na użyciu podprocedur przechodzenia, takich jak `.map()` i `union()` . Jest to exemplified poniżej:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Wydajność zapytań można sprawdzić za pomocą [ `executionProfile()` kroku Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Następne kroki
* Odwiedź stronę [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) , aby udostępnić Opinie i ułatwić zespołowi skupienie się na funkcjach, które są dla Ciebie ważne.
