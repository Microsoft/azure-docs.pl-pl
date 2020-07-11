---
title: Indeksatory do przeszukiwania danych podczas importowania
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie Azure SQL Database, wystąpienia zarządzanego SQL, Azure Cosmos DB lub usługi Azure Storage w celu wyodrębnienia danych z możliwością wyszukiwania i wypełnienia indeksu Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 253cd8174ec523f6c8a6aae2b94f7ed367701fec
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146773"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indeksatory w usłudze Azure Cognitive Search

*Indeksator* w usłudze Azure wyszukiwanie poznawcze to przeszukiwarka, która wyodrębnia dane z możliwością wyszukiwania i metadane z zewnętrznego źródła danych platformy Azure i wypełnia indeks na podstawie mapowań pola do pola między indeksem a źródłem danych. Takie podejście jest czasami nazywane "modelem ściągania", ponieważ usługa ściąga dane w programie bez konieczności pisania kodu, który dodaje dane do indeksu.

Indeksatory są oparte na typach źródeł danych lub na platformach z poszczególnymi indeksatorami dla SQL Server na platformie Azure, Cosmos DB, Table Storage platformy Azure i Blob Storage. Indeksatory magazynu obiektów BLOB mają dodatkowe właściwości specyficzne dla typów zawartości obiektów BLOB.

Dane można wprowadzać tylko za pomocą indeksatora lub kombinacji metod obejmujących użycie indeksatora w celu załadowania tylko niektórych pól w indeksie.

Indeksatory można uruchamiać na żądanie lub według cyklicznego harmonogramu odświeżania danych uruchamianego co pięć minut. Częstsze aktualizacje wymagają modelu wypychania, który jednocześnie aktualizuje dane zarówno w usłudze Azure Wyszukiwanie poznawcze, jak i w zewnętrznym źródle danych.

## <a name="approaches-for-creating-and-managing-indexers"></a>Podejścia do tworzenia indeksatorów i zarządzania nimi

Możesz tworzyć indeksatory i zarządzać nimi przy użyciu tych metod:

* [Kreator importu danych > portalu](search-import-data-portal.md)
* [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Początkowo nowy indeksator jest ogłaszany jako funkcja w wersji zapoznawczej. Funkcje w wersji zapoznawczej są wprowadzane w interfejsach API (REST i .NET), a następnie integrowane z portalem po przejściu do poziomu ogólnej dostępności. Jeśli oceniasz nowy indeksator, zaplanuj napisanie kodu.

## <a name="permissions"></a>Uprawnienia

Wszystkie operacje związane z indeksatorami, w tym żądania pobrania dla stanu lub definicji, wymagają [klucza API-Key administratora](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Indeksatory przeszukują magazyny danych na platformie Azure.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (w wersji zapoznawczej)
* [Table Storage platformy Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database i wystąpienie zarządzane SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Program SQL Server na maszynach wirtualnych platformy Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Wystąpienie zarządzane SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Podstawowe kroki konfiguracji
Indeksatory oferują funkcje, które są unikatowe dla źródła danych. W związku z tym niektóre aspekty konfiguracji indeksatora lub źródła danych różnią się w zależności od typu indeksatora. Wszystkie indeksatory korzystają jednak z takich samych kompozycji i wymagań. Kroki, które są wspólne dla wszystkich indeksatorów, znajdują się poniżej.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych
Indeksator uzyskuje połączenie ze źródłem danych z obiektu *źródła danych* . Definicja źródła danych zawiera parametry połączenia i prawdopodobnie poświadczenia. Wywołaj interfejs API REST [tworzenia źródła danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) lub [klasę DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) , aby utworzyć zasób.

Źródła danych są konfigurowane i zarządzane niezależnie od indeksatorów, które z nich korzystają. Oznacza to, że jedno źródło może być używane przez wiele indeksatorów w celu jednoczesnego ładowania kilku indeksów.

### <a name="step-2-create-an-index"></a>Krok 2. Tworzenie indeksu
Indeksator automatyzuje niektóre zadania związane z pozyskiwaniem danych, ale tworzenie indeksu na ogół nie należy do tych zadań. Jako warunek wstępny należy posiadać wstępnie zdefiniowany indeks z polami, które odpowiadają polom w zewnętrznym źródle danych. Pola muszą być zgodne według nazwy i typu danych. Aby uzyskać więcej informacji o tworzeniu struktury indeksu, zobacz [Tworzenie indeksu (interfejs API REST platformy Azure wyszukiwanie poznawcze)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) lub [klasy indeksów](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Aby uzyskać pomoc dotyczącą skojarzeń pól, zobacz [mapowania pól w usłudze Azure wyszukiwanie poznawcze indeksatory](search-indexer-field-mappings.md).

> [!Tip]
> Chociaż indeksatory nie generują automatycznie indeksu, kreator **Importowanie danych** w portalu może pomóc w wykonaniu tego zadania. W większości przypadków kreator może rozpoznać schemat indeksu na podstawie istniejących metadanych w źródle i przedstawić wstępny schemat indeksu, który można edytować przy użyciu funkcji wbudowanych w tym kreatorze. Po utworzeniu indeksu w usłudze możliwość dalszej edycji w portalu jest w większości ograniczona do dodawania nowych pól. Należy rozważyć użycie kreatora do tworzenia indeksu, ale nie do jego poprawiania. Aby nauczyć się wykonywania tych zadań w praktyce, skorzystaj z [przewodnika po portalu](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3. Tworzenie indeksatora i ustawianie jego harmonogramu
Definicja indeksatora to konstrukcja, która łączy wszystkie elementy związane z pozyskiwaniem danych. Wymagane elementy obejmują źródło i indeks danych. Opcjonalne elementy obejmują mapowania harmonogramu i pola. Mapowanie pól jest opcjonalne tylko wtedy, gdy pola źródłowe i pola indeksu są wyraźnie zgodne. Aby uzyskać więcej informacji o tworzeniu struktury indeksatora, zobacz [Create indeksatorer (Azure wyszukiwanie POZNAWCZE REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Uruchamianie indeksatorów na żądanie

Chociaż często jest planowane planowanie indeksowania, indeksator może być również wywoływany na żądanie przy użyciu [polecenia Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Po pomyślnym uruchomieniu interfejsu API wywołanie indeksatora zostało zaplanowane, ale rzeczywiste przetwarzanie odbywa się asynchronicznie. 

Stan indeksatora można monitorować w portalu lub za pomocą interfejsu API pobierania stanu indeksatora. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Pobierz stan indeksatora

Można pobrać stan i historię wykonywania indeksatora za pomocą [polecenia Pobierz indeksator stanu](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Odpowiedź zawiera ogólny stan indeksatora, ostatnie (lub w toku) wywołanie indeksatora i historię najnowszych wywołań indeksatora.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Historia wykonywania zawiera do 50 najnowszych zakończonych wykonań, które są sortowane w odwrotnej kolejności chronologicznej (w związku z czym ostatnie wykonanie jest najpierw w odpowiedzi).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz już podstawowe informacje, następnym krokiem jest przegląd wymagań i zadań specyficznych dla poszczególnych typów źródeł danych.

* [Azure SQL Database, wystąpienie zarządzane SQL lub SQL Server na maszynie wirtualnej platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Table Storage platformy Azure](search-howto-indexing-azure-tables.md)
* [Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB](search-howto-index-csv-blobs.md)
* [Indeksowanie obiektów BLOB JSON za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB](search-howto-index-json-blobs.md)
