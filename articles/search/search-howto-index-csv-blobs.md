---
title: Wyszukiwanie obiektów BLOB w formacie CSV
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zaimportuj plik CSV z usługi Azure Blob Storage przy użyciu trybu analizowania delimitedText.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: f9c01e8e31e78c277a7a3ec1e5d8d0c32b58f8bc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403657"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w formacie CSV przy użyciu trybu analizowania delimitedText i indeksatorów obiektów BLOB na platformie Azure Wyszukiwanie poznawcze

Domyślnie [indeksator usługi Azure wyszukiwanie poznawcze BLOB](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielony tekst obiektów BLOB jako pojedynczy fragment tekstu. Jednak z obiektami BLOB zawierającymi dane CSV często warto traktować każdy wiersz w obiekcie BLOB jako oddzielny dokument. Na przykład, uwzględniając następujący rozdzielony tekst, możesz chcieć przeanalizować go w dwóch dokumentach, z których każdy zawiera pola "ID", "datePublished" i "Tags": 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

W tym artykule dowiesz się, jak analizować obiekty blob w formacie CSV za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB, ustawiając `delimitedText` tryb analizy. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami dotyczącymi konfiguracji indeksatora w [indeksie jeden-do-wielu](search-howto-index-one-to-many-blobs.md) , aby wyprowadzić wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania woluminów CSV
Aby indeksować obiekty blob w formacie CSV, Utwórz lub zaktualizuj definicję indeksatora przy użyciu `delimitedText` trybu analizowania w żądaniu [tworzenia indeksatora](/rest/api/searchservice/create-indexer) :

```http
    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }
```

`firstLineContainsHeaders` wskazuje, że pierwsza (niepusta) linia każdego obiektu BLOB zawiera nagłówki.
Jeśli obiekty blob nie zawierają początkowego wiersza nagłówka, nagłówki należy określić w konfiguracji indeksatora: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Można dostosować znak ogranicznika przy użyciu `delimitedTextDelimiter` Ustawienia konfiguracji. Na przykład:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Obecnie obsługiwane jest tylko kodowanie UTF-8. Jeśli potrzebujesz pomocy technicznej dotyczącej innych kodowań, zagłosuj na nią w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> W przypadku korzystania z rozdzielonego trybu analizowania tekstu usługa Azure Wyszukiwanie poznawcze zakłada, że wszystkie obiekty blob w źródle danych będą CSV. Jeśli potrzebujesz obsługi kombinacji obiektów BLOB w formacie CSV i innych niż woluminy woluminów CSV w tym samym źródle danych, zapoznaj się z nim w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykłady żądań
Wszystkie te przykłady są kompletne. 

DataSource 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   
```

Indeksatora

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam ulepszyć platformę Azure Wyszukiwanie poznawcze
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Jeśli potrzebujesz pomocy przy korzystaniu z istniejącej funkcji, Opublikuj pytanie na [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).