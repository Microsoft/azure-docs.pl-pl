---
title: Jak wykonywać zapytania względem danych tabeli w usłudze Azure Cosmos DB?
description: Dowiedz się, jak wykonywać zapytania dotyczące danych przechowywanych na koncie Azure Cosmos DB interfejs API tabel przy użyciu filtrów OData i zapytań LINQ
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e184d85e3daee41f530334aa0034fc98f40a8766
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93099227"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Samouczek: Wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API tabel
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[Interfejs API tabel](table-introduction.md) usługi Azure Cosmos DB obsługuje zapytania OData i [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) dla danych klucz-wartość (tabeli).  

W tym artykule opisano następujące zadania:

> [!div class="checklist"]
> * Wykonywanie zapytania o dane przy użyciu interfejsu API tabel

Zapytania w tym artykule korzystają z następującej przykładowej tabeli `People`:

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jan | Jeff@contoso.com| 425-555-0104 |

Zobacz [Wykonywanie zapytań względem tabel i jednostek](/rest/api/storageservices/fileservices/querying-tables-and-entities), aby uzyskać szczegółowe informacje na temat wykonywania zapytań przy użyciu interfejsu API tabel.

Aby uzyskać więcej informacji na temat funkcji premium oferowanych przez usługę Azure Cosmos DB, zobacz [Interfejs API tabel usługi Azure Cosmos DB](table-introduction.md) i [Opracowywanie zawartości przy użyciu interfejsu API tabel na platformie .NET](tutorial-develop-table-dotnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby te zapytania działały, musisz mieć konto usługi Azure Cosmos DB i mieć dane jednostki w kontenerze. Nie spełniasz tych warunków? Ukończ [pięciominutowy przewodnik Szybki start](create-table-dotnet.md) lub [samouczek dewelopera](tutorial-develop-table-dotnet.md), aby utworzyć konto i wypełnić bazę danych.

## <a name="query-on-partitionkey-and-rowkey"></a>Zapytanie dotyczące właściwości PartitionKey i RowKey

Ponieważ właściwości PartitionKey i RowKey tworzą klucz podstawowy jednostki, do zidentyfikowania jednostki można użyć następującej specjalnej składni:

**Zapytanie**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**Wyniki**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Można także określić te właściwości jako część opcji `$filter`, jak pokazano w poniższej sekcji. Należy pamiętać, że w nazwach właściwości kluczy i wartościach stałych jest rozróżniana wielkość liter. Właściwości PartitionKey i RowKey są typu String.

## <a name="query-by-using-an-odata-filter"></a>Wykonywanie zapytań przy użyciu filtru OData

Podczas tworzenia ciągu filtru należy pamiętać o następujących regułach:

* Aby porównać właściwość z wartością, użyj operatorów logicznych określonych w specyfikacji protokołu OData. Pamiętaj, że nie można porównać właściwości z wartością dynamiczną. Jedna strona wyrażenia musi być stałą.
* Nazwa właściwości, operator i wartość stała muszą być oddzielone spacjami zakodowanymi w adresie URL. Spacja jest zakodowana w adresie URL jako `%20`.
* We wszystkich częściach ciągu filtru jest rozróżniana wielkość liter.
* Wartość stała musi mieć ten sam typ danych co właściwość, aby filtr zwracał prawidłowe wyniki. Aby uzyskać szczegółowe informacje na temat obsługiwanych typów właściwości, zobacz [Omówienie modelu danych usługi Table service](/rest/api/storageservices/understanding-the-table-service-data-model).

Poniżej przedstawiono przykładowe zapytanie, które pokazuje sposób filtrowania według właściwości PartitionKey i Email przy użyciu elementu `$filter` OData.

**Zapytanie**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Aby uzyskać więcej informacji na temat sposobu tworzenia wyrażenia filtru dla różnych typów danych, zobacz [Wykonywanie zapytań względem tabel i jednostek](/rest/api/storageservices/querying-tables-and-entities).

**Wyniki**

| PartitionKey | RowKey | E-mail | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

Zapytania dotyczące właściwości DateTime nie zwracają żadnych danych, gdy są wykonywane w interfejs API tabel Azure Cosmos DB. Podczas gdy magazyn tabel Azure przechowuje wartości daty z szczegółowością czasu taktów, interfejs API tabel w Azure Cosmos DB używa  `_ts` właściwości. `_ts`Właściwość jest na drugim poziomie stopnia szczegółowości, który nie jest filtrem OData. Dlatego zapytania we właściwościach sygnatur czasowych są blokowane przez Azure Cosmos DB. Jako obejście można zdefiniować niestandardową właściwość DateTime lub Long typu danych i ustawić wartość daty z klienta.

## <a name="query-by-using-linq"></a>Wykonywanie zapytań za pomocą wyrażenia LINQ 
Możesz także wykonywać zapytania za pomocą wyrażenia LINQ, co oznacza odpowiednie wyrażenia zapytań OData. Oto przykład sposobu tworzenia zapytań przy użyciu zestawu .NET SDK:

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu interfejsu API tabel

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-table.md)
