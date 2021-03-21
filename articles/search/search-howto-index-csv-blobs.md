---
title: Wyszukiwanie obiektów BLOB w formacie CSV
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zaimportuj plik CSV z usługi Azure Blob Storage przy użyciu trybu analizowania delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430483"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w formacie CSV przy użyciu trybu analizowania delimitedText i indeksatorów obiektów BLOB na platformie Azure Wyszukiwanie poznawcze

Indeksator usługi Azure Wyszukiwanie poznawcze [BLOB](search-howto-indexing-azure-blob-storage.md) udostępnia `delimitedText` tryb analizy dla plików CSV, który traktuje każdy wiersz w woluminie CSV jako oddzielny dokument wyszukiwania. Na przykład, uwzględniając następujący tekst rozdzielany przecinkami, `delimitedText` wynikiem będzie dwa dokumenty w indeksie wyszukiwania: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Bez `delimitedText` trybu analizowania cała zawartość pliku CSV powinna być traktowana jako jeden dokument wyszukiwania.

Za każdym razem, gdy tworzysz wiele dokumentów wyszukiwania z pojedynczego obiektu BLOB, pamiętaj o przejrzeniu [indeksowania obiektów BLOB w celu utworzenia wielu dokumentów wyszukiwania](search-howto-index-one-to-many-blobs.md) , aby zrozumieć, jak działają przypisania kluczy dokumentów. Indeksator obiektów BLOB jest w stanie znajdować lub generować wartości, które jednoznacznie definiują każdy nowy dokument. W odniesieniu do niego można utworzyć tymczasowy, `AzureSearch_DocumentKey` który wygenerował, gdy obiekt BLOB jest analizowany do mniejszych części, gdzie wartość jest używana jako klucz dokumentu wyszukiwania w indeksie.

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


