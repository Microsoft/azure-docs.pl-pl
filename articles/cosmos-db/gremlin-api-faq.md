---
title: Często zadawane pytania dotyczące interfejsu API Gremlin w Azure Cosmos DB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API Gremlin w Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359787"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące interfejsu API Gremlin w Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

W tym artykule wyjaśniono odpowiedzi na kilka często zadawanych pytań dotyczących interfejsu API Gremlin w programie Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Jak oszacować wydajność zapytań Gremlin

Krok zapoznawczy **executionProfile ()** może służyć do przeprowadzenia analizy planu wykonywania zapytania. Ten krok należy dodać do końca dowolnego zapytania Gremlin, jak pokazano na poniższym przykładzie:

**Przykład zapytania**

```
g.V('mary').out('knows').executionProfile()
```

**Przykładowe dane wyjściowe**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Dane wyjściowe powyższego profilu pokazują, ile czasu poświęcają na uzyskanie obiektów wierzchołków, obiektów brzegowych i rozmiaru zestawu danych roboczych. Jest to związane ze standardowymi pomiarami kosztów dla Azure Cosmos DB zapytań.

## <a name="other-frequently-asked-questions"></a>Inne często zadawane pytania

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Jak są naliczone opłaty za RU/s podczas wykonywania zapytań w bazie danych grafu?

Wszystkie obiekty wykresu, wierzchołki i krawędzie są wyświetlane jako dokumenty JSON w zapleczu. Ponieważ jedno zapytanie Gremlin może modyfikować jeden lub wiele obiektów grafu jednocześnie, koszt skojarzony z nim jest bezpośrednio związany z obiektami, krawędziami przetworzonymi przez zapytanie. Jest to ten sam proces, którego Azure Cosmos DB używa dla wszystkich innych interfejsów API. Aby uzyskać więcej informacji, zobacz [Jednostki żądań w usłudze Azure Cosmos DB](request-units.md).

Opłata za RU zależy od zestawu danych roboczych przechodzenia, a nie zestawu wyników. Na przykład, jeśli zapytanie ma na celu uzyskanie pojedynczego wierzchołka w wyniku, ale wymaga przechodzenia więcej niż jednego innego obiektu w sposób, koszt będzie oparty na wszystkich obiektach grafu, które pozostaną w celu obliczenia jednego wierzchołka wynik.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Jaka jest maksymalna skala, która może znajdować się w bazie danych programu Graph Azure Cosmos DB Gremlin API?

Azure Cosmos DB korzysta z [partycjonowania poziomego](partitioning-overview.md) , aby automatycznie zwiększać wymagania dotyczące magazynu i przepływności. Maksymalna przepływność i pojemność magazynu obciążenia są określane przez liczbę partycji skojarzonych z danym kontenerem. Jednak kontener interfejsu API Gremlin ma określony zestaw wytycznych, aby zapewnić prawidłowe działanie wydajności na dużą skalę. Aby uzyskać więcej informacji o partycjonowaniu i najlepszych rozwiązaniach, zobacz [partycjonowanie w Azure Cosmos DB](partitioning-overview.md) artykule.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>W przypadku programowania w języku C#/.NET należy używać pakietu Microsoft. Azure. graphs lub Gremlin.NET?

Azure Cosmos DB interfejs API Gremlin korzysta ze sterowników typu "open source" jako głównych łączników dla usługi. Zalecanym rozwiązaniem jest użycie [sterowników, które są obsługiwane przez Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Jak chronić przed atakami polegającymi na iniekcji przy użyciu sterowników Gremlin?

Większość natywnych sterowników Gremlin Apache Tinkerpop umożliwia określenie słownika parametrów do wykonania zapytania. Jest to przykład, jak to zrobić w [Gremlin.NET](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) i w [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Dlaczego otrzymuję komunikat o błędzie "Gremlin kompilacja zapytania: nie można znaleźć żadnej metody"?

Azure Cosmos DB API Gremlin implementuje podzestaw funkcji zdefiniowanych w obszarze powierzchni Gremlin. Aby zapoznać się z obsługiwanymi krokami i więcej informacji, zobacz artykuł dotyczący [pomocy technicznej Gremlin](gremlin-support.md) .

Najlepszym obejściem jest ponowne napisanie wymaganych kroków Gremlin z obsługiwanymi funkcjami, ponieważ wszystkie podstawowe kroki Gremlin są obsługiwane przez Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Dlaczego otrzymuję komunikat "WebSocketException: serwer zwrócił kod stanu" 200 ", gdy oczekiwano kodu stanu" 101 "?

Ten błąd jest prawdopodobnie zgłaszany, gdy jest używany niewłaściwy punkt końcowy. Punkt końcowy, który generuje ten błąd, ma następujący wzorzec:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

To jest punkt końcowy dokumentów dla bazy danych grafu.  Prawidłowym punktem końcowym, który ma być używany, jest punkt końcowy Gremlin o następującym formacie:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Dlaczego otrzymuję komunikat o błędzie "RequestRateIsTooLarge"?

Ten błąd oznacza, że przydzieloną liczbę jednostek żądań na sekundę nie są wystarczające do obsłużynia zapytania. Ten błąd jest zwykle widoczny podczas uruchamiania zapytania, które uzyskuje wszystkie wierzchołki:

```
// Query example:
g.V()
```

To zapytanie spróbuje pobrać wszystkie wierzchołki z grafu. W związku z tym koszt tego zapytania będzie równy co najmniej liczbie wierzchołków w odniesieniu do jednostek ru. Ustawienie RU/s powinno być dostosowane do tego zapytania.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Dlaczego moje połączenia sterowników Gremlin są ostatecznie porzucane?

Nawiązywane jest połączenie Gremlin za pomocą połączenia WebSocket. Chociaż połączenia protokołu WebSocket nie mają określonego czasu na żywo, Azure Cosmos DB interfejs API Gremlin zakończy połączenia bezczynne po 30 minutach braku aktywności.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Dlaczego nie mogę używać wywołań interfejsu API Fluent w natywnych sterownikach Gremlin?

Wywołania interfejsu API Fluent nie są jeszcze obsługiwane przez interfejs API usługi Azure Cosmos DB Gremlin. Wywołania interfejsu API Fluent wymagają funkcji formatowania wewnętrznego znanej jako obsługa kodu bajtowego, która obecnie nie jest obsługiwana przez Azure Cosmos DB interfejs API Gremlin. Z tego samego powodu Najnowsza wersja sterownika Gremlin-JavaScript również nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* [Azure Cosmos DB obsługa protokołu przewodowego Gremlin](gremlin-support.md)
* Tworzenie, wykonywanie zapytań i przechodzenie Azure Cosmos DB bazy danych programu Graph przy użyciu [konsoli Gremlin](create-graph-gremlin-console.md)
