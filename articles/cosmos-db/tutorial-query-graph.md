---
title: Jak wykonywać zapytania względem danych grafu w usłudze Azure Cosmos DB?
description: Dowiedz się, jak badać dane grafów z Azure Cosmos DB przy użyciu zapytań Gremlin
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 545ffd303d2039a3c54088220c1fa74e742c750f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360772"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Samouczek: wykonywanie zapytań względem interfejsu API języka Gremlin w usłudze Azure Cosmos DB przy użyciu języka Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Interfejs API języka Gremlin](graph-introduction.md) w usłudze Azure Cosmos DB obsługuje zapytania w języku [Gremlin](https://github.com/tinkerpop/gremlin/wiki). W tym artykule udostępniono przykładowe dokumenty i zapytania ułatwiające rozpoczęcie pracy. Szczegółowa dokumentacja dotycząca języka Gremlin została podana w artykule [Obsługa języka Gremlin](gremlin-support.md).

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytań na danych przy użyciu języka Gremlin

## <a name="prerequisites"></a>Wymagania wstępne

Aby te zapytania działały, musisz mieć konto usługi Azure Cosmos DB i mieć dane grafu w kontenerze. Nie spełniasz tych warunków? Ukończ [5-minutowy przewodnik Szybki start](create-graph-dotnet.md) lub [samouczek dewelopera](tutorial-query-graph.md), aby utworzyć konto i wypełnić bazę danych. Poniższe zapytania możesz uruchomić przy użyciu [konsoli Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) lub ulubionego sterownika Gremlin.

## <a name="count-vertices-in-the-graph"></a>Liczba wierzchołków grafu

Poniższy fragment kodu pokazuje sposób obliczania liczby wierzchołków grafu:

```
g.V().count()
```

## <a name="filters"></a>Filtry

Filtry można wykonywać przy użyciu kroków `has` i `hasLabel`, a także łączyć je przy użyciu operatorów `and`, `or` i `not` w celu utworzenia bardziej złożonych filtrów. Usługa Azure Cosmos DB zapewnia niezależne od schematów indeksowanie wszystkich zależności w wierzchołkach i stopniach na potrzeby szybkich zapytań:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projekcja

Pewne właściwości wyników zapytania można poddawać projekcji za pomocą kroku `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Znajdowanie powiązanych krawędzi i wierzchołków

Jak dotąd przedstawiono tylko operatory zapytań, które działają w dowolnej bazie danych. Grafy są szybkie i wydajne dla operacji przechodzenia, gdy trzeba przejść do powiązanych krawędzi i wierzchołków. Znajdźmy wszystkich znajomych Thomasa. Możemy to zrobić, używając kroku `outE` języka Gremlin w celu znalezienia wszystkich krawędzi wychodzących od Thomasa, a następnie przechodząc do wierzchołków z tych krawędzi przy użyciu kroku `inV` języka Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Następne zapytanie wykonuje dwa przeskoki w celu znalezienia wszystkich „znajomych znajomych” Thomasa, wywołując `outE` i `inV` dwa razy. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin, w tym mieszanie wyrażeń filtrowania, wykonywanie zapętlenia przy użyciu kroku `loop` i implementowanie nawigacji warunkowej przy użyciu kroku `choose`. Dowiedz się więcej o tym, co można zrobić dzięki [obsłudze języka Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu programu Graph 

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Cosmos DB.

> [!div class="nextstepaction"]
> [Dystrybucja globalna](distribute-data-globally.md) 

