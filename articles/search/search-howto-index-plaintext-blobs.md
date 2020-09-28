---
title: Wyszukaj w postaci obiektów BLOB w postaci zwykłego tekstu
titleSuffix: Azure Cognitive Search
description: Skonfiguruj indeksator wyszukiwania, aby wyodrębnić zwykły tekst z obiektów blob platformy Azure w celu wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403803"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w postaci zwykłego tekstu na platformie Azure Wyszukiwanie poznawcze

W przypadku użycia [indeksatora BLOB](search-howto-indexing-azure-blob-storage.md) do wyodrębnienia tekstu do przeszukiwania dla wyszukiwania pełnotekstowego można wywołać różne tryby analizy, aby uzyskać lepsze indeksowanie wyników. Domyślnie indeksator analizuje rozdzielone tekstowe obiekty blob jako pojedynczy fragment tekstu. Jeśli jednak wszystkie obiekty blob zawierają zwykły tekst w tym samym kodowaniu, można znacząco poprawić wydajność indeksowania przy użyciu **trybu analizowania tekstu**.

## <a name="set-up-plain-text-indexing"></a>Konfigurowanie indeksowania zwykłego tekstu

Aby indeksować obiekty blob w postaci zwykłego tekstu, należy utworzyć lub zaktualizować definicję indeksatora przy użyciu `parsingMode` Właściwości konfiguracji do `text` żądania [utworzenia indeksatora](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Domyślnie `UTF-8` jest założono kodowanie. Aby określić inne kodowanie, użyj `encoding` właściwości konfiguracja: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Przykład żądania

Tryby analizowania są określone w definicji indeksatora.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam ulepszyć platformę Azure Wyszukiwanie poznawcze
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Jeśli potrzebujesz pomocy przy korzystaniu z istniejącej funkcji, Opublikuj pytanie na [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Zobacz też

* [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
* [Jak skonfigurować indeksator obiektów BLOB](search-howto-indexing-azure-blob-storage.md)
* [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)