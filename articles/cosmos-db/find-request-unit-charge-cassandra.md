---
title: Znajdź opłatę jednostki żądania (RU) dla zapytania interfejs API Cassandra w Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla zapytań Cassandra wykonanych w odniesieniu do kontenera usługi Azure Cosmos. Możesz użyć sterowników Azure Portal, .NET i Java, aby znaleźć opłatę RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 38027f651623b16fed259cbf099bc274457723eb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482557"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Znajdź opłatę za jednostkę żądania dla operacji wykonywanych w Azure Cosmos DB interfejs API Cassandra

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru. Aby dowiedzieć się więcej, zobacz artykuł [jednostki żądań i zagadnienia](request-units.md) .

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej względem kontenera w Azure Cosmos DB interfejs API Cassandra. Jeśli używasz innego interfejsu API, zobacz [API for MongoDB](find-request-unit-charge-mongodb.md), [SQL API](find-request-unit-charge.md), [Gremlin API](find-request-unit-charge-gremlin.md)i artykuły [interfejs API tabel](find-request-unit-charge-table.md) , aby znaleźć koszt ru/s.

Gdy wykonujesz operacje dotyczące interfejs API Cassandra Azure Cosmos DB, opłata RU jest zwracana w ładunku przychodzącym jako pole o nazwie `RequestCharge` . Istnieje wiele opcji pobierania opłaty za jednostkę żądania.

## <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

W przypadku korzystania z [zestawu SDK platformy .NET](https://www.nuget.org/packages/CassandraCSharpDriver/)można pobrać ładunek przychodzący do `Info` właściwości `RowSet` obiektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Cassandra przy użyciu zestawu .NET SDK i Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

W przypadku korzystania z [zestawu SDK języka Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)można pobrać ładunek przychodzący przez wywołanie `getExecutionInfo()` metody na `ResultSet` obiekcie:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Cassandra przy użyciu zestawu Java SDK i Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)