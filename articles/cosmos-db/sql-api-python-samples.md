---
title: Przykłady kodu Python dla interfejsu SQL API usługi Azure Cosmos DB
description: Znajdź w witrynie GitHub przykłady kodu Python służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 08/11/2020
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: 10da2f3a84a4c1e6d761c86901f3249972cc0f5b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477848"
---
# <a name="azure-cosmos-db-python-examples"></a>Przykłady kodu Python dla usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady zestawu SDK dla danych wiosennych v3](sql-api-spring-data-sdk-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Python.
* Linki do powiązanej dokumentacji interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto Cosmos DB. Dostępne są następujące opcje:
    * W ramach aktywnej subskrypcji platformy Azure:
        * [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free) lub Użyj istniejącej subskrypcji 
        * [Kredyty miesięczne programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB warstwy Bezpłatna](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Bez aktywnej subskrypcji platformy Azure:
        * [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)w środowisku testowym trwającym przez 30 dni.
        * [Emulator usługi Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 lub 3.5.3 +](https://www.python.org/downloads/), z `python` plikiem wykonywalnym w `PATH` .
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- Usługi [git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="database-examples"></a>Przykłady dotyczące baz danych

Przykład w języku Python [database_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat baz danych usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](account-databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Wysyłanie zapytań do baz danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Usuwanie bazy danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Przykłady dotyczące kontenerów

Przykład w języku Python [container_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](account-databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Zapytanie dotyczące kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Tworzenie kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Wyświetl listę wszystkich kontenerów w bazie danych](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Pobierz kontener według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Zarządzanie elastyczną przepływność kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Usuwanie kontenera](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Przykłady dotyczące elementów

Przykład w języku Python [item_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](account-databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie elementów w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Odczytaj element według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Odczytaj wszystkie elementy w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Wykonywanie zapytania dotyczącego elementu według jego identyfikatora](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Zastąp element](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Upsert element](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Usuwanie elementu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Pobierz kanał informacyjny zmiany elementów w kontenerze](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania

Przykład w języku Python [index_management. PR](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [typy indeksowania](index-overview.md#index-kinds)i [ścieżki indeksowania](index-policy.md#include-exclude-paths) artykuły koncepcyjne.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykluczanie określonego elementu z indeksowania](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Używanie indeksowania ręcznego z określonymi elementami indeksowanymi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | Secret. IndexingDirective. include |
| [Wyklucz ścieżki z indeksowania](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Zdefiniuj ścieżki do wykluczenia we `IndexingPolicy` właściwości |
| [Używanie indeksów zakresu z ciągami](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Zdefiniuj zasady indeksowania przy użyciu indeksów zakresu dla typu danych ciągu. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Przekształcanie indeksu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (użyj zaktualizowanych zasad indeksowania)|
| [Użyj skanów, gdy na ścieżce istnieje tylko indeks skrótu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | Ustawianie `enable_scan_in_query=True` i `enable_cross_partition_query=True` podczas wykonywania zapytania dotyczącego elementów |
