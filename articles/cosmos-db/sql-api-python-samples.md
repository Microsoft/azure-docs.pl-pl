---
title: Przykłady kodu Python dla interfejsu SQL API usługi Azure Cosmos DB
description: Znajdź w witrynie GitHub przykłady kodu Python służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: tracking-python
ms.openlocfilehash: c52651e15f648bede419501f516392acbc266e8a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558005"
---
# <a name="azure-cosmos-db-python-examples"></a>Przykłady kodu Python dla usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Python.
* Linki do powiązanej dokumentacji interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebny będzie również [zestaw SDK dla języka Python](sql-api-sdk-python.md).

   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady generują wiele wywołań do `CosmosClient.CreateContainer` . Każde z tych wywołań wiąże się z dodaniem jednej godziny użycia do rozliczenia za subskrypcję. Aby uzyskać więcej informacji na temat rozliczeń w usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB — cennik](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Przykłady dotyczące baz danych

Przykład w języku Python [database_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat baz danych usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient. create_database|
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient. get_database_client|
| [Wysyłanie zapytań do baz danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient. query_databases|
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient. list_databases|
| [Usuwanie bazy danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient. delete_database|

## <a name="container-examples"></a>Przykłady dotyczące kontenerów

Przykład w języku Python [container_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Zapytanie dotyczące kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |Baza danych. query_containers |
| [Tworzenie kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |Baza danych. create_container |
| [Wyświetl listę wszystkich kontenerów w bazie danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |Baza danych. list_containers |
| [Pobierz kontener według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |Baza danych. get_container_client |
| [Zarządzanie elastyczną przepływność kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |Container. read_offer, Container. replace_throughput|
| [Usuwanie kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |Baza danych. delete_container |

## <a name="item-examples"></a>Przykłady dotyczące elementów

Przykład w języku Python [item_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie elementów w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |kontener. create_item |
| [Odczytaj element według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |kontener. read_item |
| [Odczytaj wszystkie elementy w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |kontener. read_all_items |
| [Wykonywanie zapytania dotyczącego elementu według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |kontener. query_items |
| [Zastąp element](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |kontener. replace_items |
| [Upsert element](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |kontener. upsert_item |
| [Usuń element](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |kontener. delete_item |
| [Pobierz kanał informacyjny zmiany elementów w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |kontener. query_items_change_feed |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania

Przykład w języku Python [index_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [typy indeksowania](index-types.md)i [ścieżki indeksowania](index-paths.md) artykuły koncepcyjne.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykluczanie określonego elementu z indeksowania](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Używanie indeksowania ręcznego z określonymi elementami indeksowanymi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | Secret. IndexingDirective. include |
| [Wyklucz ścieżki z indeksowania](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Zdefiniuj ścieżki do wykluczenia we `IndexingPolicy` właściwości |
| [Używanie indeksów zakresu z ciągami](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Zdefiniuj zasady indeksowania przy użyciu indeksów zakresu dla typu danych ciągu. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Przekształcanie indeksu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |Database. replace_container (użyj zaktualizowanych zasad indeksowania)|
| [Użyj skanów, gdy na ścieżce istnieje tylko indeks skrótu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Ustawianie `enable_scan_in_query=True` i `enable_cross_partition_query=True` podczas wykonywania zapytania dotyczącego elementów |
