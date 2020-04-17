---
title: Zgodność usługi Azure Cosmos DB Gremlin z funkcjami TinkerPop
description: Dokumentacja dokumentacja Graph problemy ze zgodnością silnika
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449888"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Zgodność z programem Azure Cosmos DB Gremlin
Aparat programu Azure Cosmos DB Graph ściśle przestrzega specyfikacji [kroków przechodzenia Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) ale występują różnice.

## <a name="behavior-differences"></a>Różnice w zachowaniu

* Aparat programu Azure Cosmos DB Graph uruchamia ***pierwszy*** przeglądowy, podczas gdy TinkerPop Gremlin jest pierwszy na głębokości. To zachowanie osiąga lepszą wydajność w skalowalnym poziomie systemie, takim jak Cosmos DB. 

## <a name="unsupported-features"></a>Nieobsługiwały się funkcje

* ***[Kod bajtowy języka Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** to specyfikacja przechodzenia przez graf niezależna od języka programowania. Cosmos DB Graph nie obsługuje go jeszcze. Użyj `GremlinClient.SubmitAsync()` i przekaż przechodzenie jako ciąg tekstowy.

* ***`property(set, 'xyz', 1)`*** kardynalność nie jest dziś obsługiwana. Zamiast tego użyj polecenia cmdlet `property(list, 'xyz', 1)`. Aby dowiedzieć się więcej, zobacz [Właściwości wierzchołka z TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** umożliwia wykonywanie zapytań o wykresy przy użyciu dopasowywania wzorca deklaratywnego. Ta funkcja nie jest dostępna.

* ***Obiekty jako właściwości*** na wierzchołkach lub krawędziach nie są obsługiwane. Właściwości mogą być tylko typami pierwotnymi lub tablicami.

* ***Sortowanie według właściwości tablicy*** `order().by(<array property>)` nie jest obsługiwane. Sortowanie jest obsługiwane tylko według typów pierwotnych.

* ***Niepryderne typy JSON*** nie są obsługiwane. Użyj `string` `number`, `true` / `false` lub typów. `null`wartości nie są obsługiwane. 

* ***Serializator GraphSONv3*** nie jest obecnie obsługiwany. Użyj `GraphSONv2` klasy Serializer, Reader i Writer w konfiguracji połączenia. Wyniki zwracane przez interfejs API Gremlin usługi Azure Cosmos DB nie mają tego samego formatu co format GraphSON. 

* **Wyrażenia lambda i funkcje** nie są obecnie obsługiwane. Obejmuje to `.map{<expression>}`funkcje `.by{<expression>}`, `.filter{<expression>}` i , i funkcje. Aby dowiedzieć się więcej i dowiedzieć się, jak przepisać je za pomocą kroków Gremlin, zobacz [Uwaga na Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakcje*** nie są obsługiwane ze względu na rozproszony charakter systemu.  Skonfiguruj odpowiedni model spójności na koncie Gremlin, aby "odczytywać własne zapisy" i używać optymistycznej współbieżności do rozwiązywania powodujących konflikt zapisów.

## <a name="next-steps"></a>Następne kroki
* Odwiedź stronę [głosową użytkownika usługi Cosmos DB,](https://feedback.azure.com/forums/263030-azure-cosmos-db) aby podzielić się opiniami i pomóc zespołowi skupić się na funkcjach, które są dla Ciebie ważne.
