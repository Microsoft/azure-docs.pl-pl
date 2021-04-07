---
title: Znajdź opłatę jednostki żądania (RU) dla zapytań interfejsu API Gremlin w Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla zapytań Gremlin wykonanych w odniesieniu do kontenera usługi Azure Cosmos. Możesz użyć Azure Portal, .NET i sterowników środowiska Java, aby znaleźć opłatę za RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201340"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Znajdź opłatę za jednostkę żądania dla operacji wykonywanych w Azure Cosmos DB interfejsie API Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Opłata za żądanie jest jednostką żądania zużywaną przez wszystkie operacje bazy danych. Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru. Aby dowiedzieć się więcej, zobacz artykuł [jednostki żądań i zagadnienia](request-units.md) .

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej względem kontenera w Azure Cosmos DB interfejsie API Gremlin. Jeśli używasz innego interfejsu API, zobacz [API for MongoDB](find-request-unit-charge-mongodb.md), [interfejs API CASSANDRA](find-request-unit-charge-cassandra.md), [SQL API](find-request-unit-charge.md)i [interfejs API tabel](find-request-unit-charge-table.md) , aby znaleźć koszt ru/s.

Nagłówki zwracane przez interfejs API Gremlin są mapowane na niestandardowe atrybuty stanu, które obecnie są przedstawiane przez Gremlin .NET i zestaw Java SDK. Opłata za żądanie jest dostępna pod `x-ms-request-charge` kluczem. Korzystając z interfejsu API Gremlin, dostępnych jest wiele opcji znajdowania zużycia RU dla operacji w odniesieniu do kontenera usługi Azure Cosmos.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-graph-gremlin-console.md#create-a-database-account) i podawanie danych z danymi lub Wybierz istniejące konto, które zawiera już dane.

1. Przejdź do okienka **Eksplorator danych** , a następnie wybierz kontener, w którym chcesz rozpocząć pracę.

1. Wprowadź prawidłowe zapytanie, a następnie wybierz pozycję **wykonaj zapytanie Gremlin**.

1. Wybierz pozycję **Statystyka zapytania** , aby wyświetlić rzeczywistą opłatę za żądanie dla wykonywanego żądania.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Zrzut ekranu przedstawiający żądanie pobrania zapytania Gremlin w Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>Korzystanie ze sterownika zestawu SDK platformy .NET

Gdy używasz [zestawu SDK Gremlin.NET](https://www.nuget.org/packages/Gremlin.Net/), atrybuty stanu są dostępne pod `StatusAttributes` właściwością `ResultSet<>` obiektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji .NET Framework lub Core przy użyciu konta interfejsu API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Korzystanie ze sterownika Java SDK

W przypadku używania [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)można pobrać atrybuty stanu, wywołując `statusAttributes()` metodę dla `ResultSet` obiektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie bazy danych grafu w Azure Cosmos dB przy użyciu zestawu Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)