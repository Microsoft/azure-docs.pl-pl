---
title: Wyszukaj w postaci obiektów BLOB w postaci zwykłego tekstu
titleSuffix: Azure Cognitive Search
description: Skonfiguruj indeksator wyszukiwania, aby wyodrębnić zwykły tekst z obiektów blob platformy Azure w celu wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509456"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w postaci zwykłego tekstu na platformie Azure Wyszukiwanie poznawcze

W przypadku używania [indeksatora obiektów BLOB](search-howto-indexing-azure-blob-storage.md) do wyodrębniania wyszukiwania tekstu obiektu BLOB w celu przeszukiwania pełnotekstowego można przypisać tryb analizy, aby uzyskać lepszy indeks wyników. Domyślnie indeksator analizuje zawartość obiektu BLOB jako pojedynczy fragment tekstu. Jeśli jednak wszystkie obiekty blob zawierają zwykły tekst w tym samym kodowaniu, można znacząco poprawić wydajność indeksowania przy użyciu `text` trybu analizowania.

Zalecenia dotyczące zastosowania `text` analizy obejmują:

+ Typ pliku to. txt
+ Pliki są dowolnego typu, ale zawartość jest tekstem (na przykład kod źródłowy programu, HTML, XML itd.). W przypadku plików w języku oznaczania wszystkie znaki składni będą się znajdować jako tekst statyczny.

Odwołaj wszystkie indeksatory serializacji do formatu JSON. Domyślnie zawartość całego pliku tekstowego będzie indeksowana w ramach jednego dużego pola jako `"content": "<file-contents>"` . Każdy nowy wiersz i instrukcje Return są osadzone w polu zawartości i wyrażane jako `\r\n\` .

Jeśli chcesz uzyskać bardziej szczegółowy wynik i jeśli typ pliku jest zgodny, weź pod uwagę następujące rozwiązania:

+ [`delimitedText`](search-howto-index-csv-blobs.md) Tryb analizowania, jeśli źródło jest w formacie CSV
+ [ `jsonArray` lub `jsonLines` ](search-howto-index-json-blobs.md), jeśli źródło jest w formacie JSON

Trzecia opcja dla dzielenia zawartości na wiele części wymaga zaawansowanych funkcji w postaci [wzbogacania AI](cognitive-search-concept-intro.md). Dodaje analizę identyfikującą i przypisuje fragmenty pliku do różnych pól wyszukiwania. Możesz znaleźć pełne lub częściowe rozwiązanie za pomocą [wbudowanych umiejętności](cognitive-search-predefined-skills.md), ale bardziej podobne rozwiązanie będzie modelem uczenia się, który zrozumie zawartość, w oparciu o model uczenia niestandardowego, opakowany w [niestandardową umiejętność](cognitive-search-custom-skill-interface.md).

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

## <a name="next-steps"></a>Następne kroki

+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Jak skonfigurować indeksator obiektów BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)