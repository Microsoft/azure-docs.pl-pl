---
title: Tworzenie kontenerów usługi Azure Cosmos z dużym kluczem partycji
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB z dużym kluczem partycji przy użyciu Azure Portal i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339303"
---
# <a name="create-containers-with-large-partition-key"></a>Tworzenie kontenerów z dużym kluczem partycji
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB stosuje schemat partycjonowania oparty na skrócie, aby osiągnąć skalowanie danych w poziomie. Wszystkie kontenery usługi Azure Cosmos utworzone przed 3 2019 maja mogą korzystać z funkcji skrótu, która oblicza wartość skrótu na podstawie pierwszych 100 bajtów klucza partycji. Jeśli istnieje wiele kluczy partycji, które mają te same pierwsze 100 bajtów, te partycje logiczne są traktowane jako te same partycje logiczne przez usługę. Może to prowadzić do problemów, takich jak nieprawidłowy limit przydziału rozmiaru partycji i unikatowe indeksy stosowane w ramach kluczy partycji. Wprowadzono duże klucze partycji w celu rozwiązania tego problemu. Azure Cosmos DB teraz obsługuje duże klucze partycji z wartościami do 2 KB.

Duże klucze partycji są obsługiwane przy użyciu funkcji rozszerzonej wersji funkcji skrótu, która może generować unikatowy skrót z dużych kluczy partycji do 2 KB. Ta wersja skrótu jest również zalecana w przypadku scenariuszy o dużej kardynalności klucza partycji, niezależnie od rozmiaru klucza partycji. Kardynalność klucza partycji jest definiowana jako liczba unikatowych partycji logicznych, na przykład w kolejności ~ 30000 partycji logicznych w kontenerze. W tym artykule opisano sposób tworzenia kontenera z dużym kluczem partycji przy użyciu Azure Portal i różnych zestawów SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Tworzenie dużego klucza partycji (Azure Portal)

Aby utworzyć duży klucz partycji, podczas tworzenia nowego kontenera przy użyciu Azure Portal zaznacz opcję **mój klucz partycji jest większy niż 100-bajtów** . Usuń zaznaczenie pola wyboru, jeśli nie są potrzebne duże klucze partycji lub aplikacje działające w wersji SDK starszej niż 1,18.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Tworzenie dużych kluczy partycji przy użyciu Azure Portal":::

## <a name="create-a-large-partition-key-powershell"></a>Tworzenie dużego klucza partycji (program PowerShell)

Aby utworzyć kontener z obsługą dużych kluczy partycji, zobacz,

* [Tworzenie kontenera usługi Azure Cosmos o dużym rozmiarze klucza partycji](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Tworzenie dużego klucza partycji (zestaw SDK dla platformy .NET)

Aby utworzyć kontener z dużym kluczem partycji przy użyciu zestawu .NET SDK, określ `PartitionKeyDefinitionVersion.V2` Właściwość. Poniższy przykład pokazuje, jak określić Właściwość Version w obiekcie PartitionKeyDefinition i ustawić ją na PartitionKeyDefinitionVersion. v2.

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Obsługiwane wersje zestawu SDK

Klucze dużych partycji są obsługiwane z następującymi minimalnymi wersjami zestawów SDK:

|Typ zestawu SDK  | Minimalna wersja   |
|---------|---------|
|.Net     |    1,18     |
|Synchronizacja Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| Interfejs API REST | wersja wyższa niż przy `2017-05-03` użyciu `x-ms-version` nagłówka żądania.|
| Szablon usługi Resource Manager | Wersja 2 przy użyciu `"version":2` właściwości w `partitionKey` obiekcie. |

Obecnie nie można używać kontenerów z dużym kluczem partycji w ramach Power BI i Azure Logic Apps. Z tych aplikacji można korzystać z kontenerów bez dużego klucza partycji.

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](./account-databases-containers-items.md)