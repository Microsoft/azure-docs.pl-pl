---
title: Znajdź opłatę jednostkową żądania dla interfejs API tabel zapytań w Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla zapytań interfejs API tabel wykonanych w odniesieniu do kontenera usługi Azure Cosmos. Możesz użyć języków Azure Portal, .NET, Java, Python i Node.js, aby znaleźć opłatę za RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201308"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Znajdź opłatę za jednostkę żądania dla operacji wykonywanych w Azure Cosmos DB interfejs API tabel
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Opłata za żądanie jest jednostką żądania zużywaną przez wszystkie operacje bazy danych. Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru. Aby dowiedzieć się więcej, zobacz artykuł [jednostki żądań i zagadnienia](request-units.md) .

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej względem kontenera w Azure Cosmos DB interfejs API tabel. Jeśli używasz innego interfejsu API, zobacz artykuł [API for MongoDB](find-request-unit-charge-mongodb.md), [interfejs API CASSANDRA](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)i [SQL API](find-request-unit-charge.md) , aby znaleźć koszt ru/s.

## <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Obecnie jedynym zestawem SDK, który zwraca opłatę RU dla operacji w tabeli, jest [zestaw .NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult`Obiekt ujawnia `RequestCharge` Właściwość, która jest wypełniana przez zestaw SDK, gdy jest używana w odniesieniu do Azure Cosmos DB interfejs API tabel:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji interfejs API tabel przy użyciu zestawu .NET SDK i Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)