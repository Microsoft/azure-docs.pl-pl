---
title: Azure Cosmos DB zgodność Gremlin z funkcjami TinkerPop
description: Informacje o problemach ze zgodnością aparatu grafu dokumentacji
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449888"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB zgodność Gremlin
Aparat grafu Azure Cosmos DB jest ściśle opisany w specyfikacji kroków przechodzenia do programu [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale istnieją różnice.

## <a name="behavior-differences"></a>Różnice w zachowaniu

* Aparat grafu Azure Cosmos DB uruchamia przechodzenie w ***pierwszej kolejności*** , podczas gdy TinkerPop Gremlin jest głębokością. Takie zachowanie zapewnia lepszą wydajność w skalowalnym systemie, takim jak Cosmos DB. 

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

* ***[Kod bajtowy języka Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** to specyfikacja przechodzenia przez graf niezależna od języka programowania. Program Cosmos DB Graph jeszcze nie obsługuje tego programu. Użyj `GremlinClient.SubmitAsync()` i przekaż przechodzenie jako ciąg tekstowy.

* ***`property(set, 'xyz', 1)`*** ustawienie Kardynalność nie jest obecnie obsługiwane. Zamiast tego użyj polecenia cmdlet `property(list, 'xyz', 1)`. Aby dowiedzieć się więcej, zobacz [Właściwości wierzchołka z TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** umożliwia wykonywanie zapytań na wykresach przy użyciu specyfikatora wzorca deklaratywnego. Ta funkcja jest niedostępna.

* ***Obiekty jako właściwości*** dla wierzchołków lub krawędzi nie są obsługiwane. Właściwości mogą być tylko typami pierwotnymi lub tablicami.

* ***Sortowanie według właściwości*** `order().by(<array property>)` tablicy nie jest obsługiwane. Sortowanie jest obsługiwane tylko według typów pierwotnych.

* ***Niepierwotne typy JSON*** nie są obsługiwane. Użyj `string`typów `number`,, `true` / `false` lub. `null`wartości nie są obsługiwane. 

* Serializator ***GraphSONv3*** nie jest obecnie obsługiwany. W `GraphSONv2` konfiguracji połączenia użyj klas serializatorów, czytników i składników zapisywania. Wyniki zwrócone przez interfejs API Azure Cosmos DB Gremlin nie mają takiego samego formatu jak format GraphSON. 

* **Wyrażenia lambda i funkcje** nie są obecnie obsługiwane. Obejmuje to `.filter{<expression>}` funkcje `.map{<expression>}`, `.by{<expression>}`i. Aby dowiedzieć się więcej, i dowiedzieć się, jak ponownie napisać je za pomocą kroków Gremlin, zobacz [uwagi na temat wyrażeń lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakcje*** nie są obsługiwane z powodu rozproszonego charakteru systemu.  Skonfiguruj odpowiedni model spójności na koncie Gremlin na "odczytywanie własnych zapisów" i Użyj optymistycznej współbieżności, aby rozwiązać konflikty zapisów.

## <a name="next-steps"></a>Następne kroki
* Odwiedź stronę [Cosmos DB User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) , aby udostępnić Opinie i ułatwić zespołowi skupienie się na funkcjach, które są dla Ciebie ważne.
