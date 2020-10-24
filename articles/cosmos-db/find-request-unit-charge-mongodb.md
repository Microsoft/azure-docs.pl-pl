---
title: Znajdź opłatę jednostkową żądania dla Azure Cosmos DB API dla operacji MongoDB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla zapytań MongoDB wykonanych w odniesieniu do kontenera usługi Azure Cosmos. Możesz użyć Azure Portal, MongoDB .NET, Java Node.js Drivers.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b1e2d6e9d5d06410596a40f16869d7c1a0f094e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490700"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Znajdź koszt jednostkowy żądania dla operacji wykonywanych w interfejsie API Azure Cosmos DB dla MongoDB

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru. Aby dowiedzieć się więcej, zobacz artykuł [jednostki żądań i zagadnienia](request-units.md) .

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej w odniesieniu do kontenera w interfejsie API Azure Cosmos DB MongoDB. Jeśli używasz innego interfejsu API, zobacz artykuł [SQL API](find-request-unit-charge.md), [interfejs API Cassandra](find-request-unit-charge-cassandra.md), [interfejs API Gremlin](find-request-unit-charge-gremlin.md)i artykuły [interfejs API tabel](find-request-unit-charge-table.md) , aby znaleźć opłatę za ru/s.

Opłata za RU jest udostępniana przez niestandardowe [polecenie bazy danych](https://docs.mongodb.com/manual/reference/command/) o nazwie `getLastRequestStatistics` . Polecenie zwraca dokument zawierający nazwę wykonanej ostatniej operacji, jej koszt żądania i czas trwania. Jeśli używasz interfejsu API Azure Cosmos DB dla MongoDB, masz wiele opcji pobierania opłaty za RU.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) i podawanie danych z danymi lub Wybierz istniejące konto, które zawiera już dane.

1. Przejdź do okienka **Eksplorator danych** , a następnie wybierz kontener, w którym chcesz rozpocząć pracę.

1. Wybierz pozycję **Nowe zapytanie**.

1. Wprowadź prawidłowe zapytanie, a następnie wybierz pozycję **wykonaj zapytanie**.

1. Wybierz pozycję **Statystyka zapytania** , aby wyświetlić rzeczywistą opłatę za żądanie dla wykonywanego żądania.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Zrzut ekranu przedstawiający opłaty za żądanie zapytania MongoDB w Azure Portal":::

## <a name="use-the-mongodb-net-driver"></a>Korzystanie ze sterownika MongoDB .NET

W przypadku korzystania z [oficjalnego sterownika .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/)można wykonać polecenia, wywołując `RunCommand` metodę dla `IMongoDatabase` obiektu. Ta metoda wymaga implementacji `Command<>` klasy abstrakcyjnej:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web platformy .NET przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Korzystanie ze sterownika Java MongoDB


W przypadku korzystania z [oficjalnego sterownika Java MongoDB](https://mongodb.github.io/mongo-java-driver/)można wykonać polecenia, wywołując `runCommand` metodę na `MongoDatabase` obiekcie:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web za pomocą interfejsu API Azure Cosmos DB dla MongoDB i zestawu Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Korzystanie ze sterownika Node.js MongoDB

W przypadku korzystania z [oficjalnego sterownika Node.js MongoDB](https://mongodb.github.io/node-mongodb-native/)można wykonać polecenia, wywołując `command` metodę na `db` obiekcie:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Migrowanie istniejącej aplikacji sieci Web MongoDB Node.js w Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)