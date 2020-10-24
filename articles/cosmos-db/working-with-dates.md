---
title: Praca z datami w Azure Cosmos DB
description: Dowiedz się, jak przechowywać, indeksować i wykonywać zapytania dotyczące obiektów czasu danych w Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a876c5ba9c289f0edbbfdf8727e9957e7937b781
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476250"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Praca z datami w Azure Cosmos DB

Azure Cosmos DB zapewnia elastyczność schematu i rozbudowane indeksowanie za pośrednictwem natywnego modelu danych [JSON](https://www.json.org) . Wszystkie zasoby Azure Cosmos DB, w tym bazy danych, kontenery, dokumenty i procedury składowane, są modelowane i przechowywane jako dokumenty JSON. Jako wymaganie do przenośnego, JSON (i Azure Cosmos DB) obsługuje tylko niewielki zestaw typów podstawowych: String, Number, Boolean, Array, Object i null. Jednak kod JSON jest elastyczny i umożliwia deweloperom i strukturom reprezentowanie bardziej złożonych typów przy użyciu tych podstawowych elementów i tworzenie ich jako obiektów lub tablic.

Oprócz typów podstawowych wiele aplikacji wymaga typu DateTime do reprezentowania dat i sygnatur czasowych. W tym artykule opisano, jak deweloperzy mogą przechowywać i pobierać daty oraz wykonywać zapytania w Azure Cosmos DB przy użyciu zestawu .NET SDK.

## <a name="storing-datetimes"></a>Przechowywanie DateTimes

Azure Cosmos DB obsługuje typy JSON, takie jak-String, Number, Boolean, null, Array i Object. Nie obsługuje bezpośrednio typu DateTime. Obecnie Azure Cosmos DB nie obsługuje lokalizacji dat. Dlatego należy przechowywać wartości DateTimes jako ciągi. Zalecany format ciągów DateTime w Azure Cosmos DB jest `yyyy-MM-ddTHH:mm:ss.fffffffZ` następujący po standardzie ISO 8601 UTC. Zaleca się przechowywanie wszystkich dat w Azure Cosmos DB jako czas UTC. Konwersja ciągów daty na ten format umożliwi lexicographically dat sortowania. Jeśli są przechowywane daty inne niż UTC, logika musi być obsługiwana po stronie klienta. Aby skonwertować lokalną datę i godzinę do UTC, przesunięcie musi być znane/przechowywane jako właściwość w formacie JSON, a klient może użyć przesunięcia do obliczenia wartości daty i godziny UTC.

Zapytania zakresowe z ciągami DateTime jako filters są obsługiwane tylko wtedy, gdy ciągi DateTime są wszystkie w formacie UTC i o tej samej długości. W Azure Cosmos DB Funkcja systemowa [GetCurrentDateTime](sql-query-getcurrentdatetime.md) zwróci bieżącą wartość ciągu ISO 8601 daty i czasu UTC w formacie: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Większość aplikacji może używać domyślnej reprezentacji ciągu dla daty i godziny z następujących powodów:

* Ciągi można porównać, a względne porządkowanie wartości DateTime jest zachowywane, gdy są przekształcane na ciągi.
* Takie podejście nie wymaga żadnych niestandardowych kodów ani atrybutów dla konwersji JSON.
* Daty zapisane w formacie JSON są czytelne dla człowieka.
* Takie podejście może wykorzystać indeks Azure Cosmos DB w celu uzyskania szybkiej wydajności zapytań.

Na przykład poniższy fragment kodu przechowuje `Order` obiekt zawierający dwie właściwości DateTime- `ShipDate` i `OrderDate` jako dokument przy użyciu zestawu .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Ten dokument jest przechowywany w Azure Cosmos DB w następujący sposób:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Alternatywnie można przechowywać daty i godziny jako sygnatury czasowe systemu UNIX, czyli liczbę reprezentującą liczbę sekund, które upłynęły od 1 stycznia 1970. Azure Cosmos DB wewnętrzna właściwość sygnatury czasowej ( `_ts` ) jest zgodna z tą metodą. Klasy [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) można użyć do serializacji datetimes jako liczby.

## <a name="querying-datetimes-in-linq"></a>Wykonywanie zapytań o wartości DateTimes w LINQ

Zestaw SDK programu SQL .NET automatycznie obsługuje wykonywanie zapytań dotyczących danych przechowywanych w Azure Cosmos DB za pośrednictwem LINQ. Na przykład poniższy fragment kodu przedstawia zapytanie LINQ, które filtruje zamówienia, które zostały wysłane w ciągu ostatnich trzech dni:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Przetłumaczone na następującą instrukcję SQL i wykonywane na Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Możesz dowiedzieć się więcej na temat języka zapytań SQL Azure Cosmos DB i dostawcy LINQ podczas [wykonywania zapytań Cosmos DB w LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indeksowanie DateTimes dla zapytań zakresu

Zapytania są typowe dla wartości typu DateTime. Aby efektywnie wykonywać te zapytania, musisz mieć zdefiniowany indeks we wszystkich właściwościach filtru zapytania.

Więcej informacji na temat konfigurowania zasad indeksowania można znaleźć w [Azure Cosmos DB zasad indeksowania](index-policy.md). 

## <a name="next-steps"></a>Następne kroki

* Pobierz i uruchom [przykłady kodu w witrynie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Dowiedz się więcej o [zapytaniach SQL](sql-query-getting-started.md)
* Dowiedz się więcej na temat [Azure Cosmos DB zasad indeksowania](index-policy.md)