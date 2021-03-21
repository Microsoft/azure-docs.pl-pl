---
title: Wyszukaj w obiektach Blob JSON
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie obiektów BLOB usługi Azure JSON dla zawartości tekstowej przy użyciu indeksatora usługi Azure Wyszukiwanie poznawcze BLOB. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259054"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w formacie JSON za pomocą indeksatora obiektów BLOB na platformie Azure Wyszukiwanie poznawcze

W tym artykule opisano sposób [konfigurowania indeksatora obiektów BLOB](search-howto-indexing-azure-blob-storage.md) dla obiektów blob, które składają się z dokumentów JSON. Obiekty blob JSON w usłudze Azure Blob Storage zazwyczaj zakładają dowolne z następujących form:

+ Pojedynczy dokument JSON
+ Dokument JSON zawierający tablicę poprawnie sformułowanych elementów JSON
+ Dokument JSON zawierający wiele jednostek rozdzielonych znakiem nowego wiersza

Indeksator obiektów BLOB udostępnia **`parsingMode`** parametr do optymalizowania danych wyjściowych dokumentu wyszukiwania na podstawie trybów analizy struktury składają się z następujących opcji:

| przeanalizowanie | Dokument JSON | Opis |
|--------------|-------------|--------------|
| **`json`** | Jeden na obiekt BLOB | wartooć Analizuje obiekty blob JSON jako pojedynczy fragment tekstu. Każdy obiekt BLOB JSON jest pojedynczym dokumentem wyszukiwania. |
| **`jsonArray`** | Wiele na obiekt BLOB | Analizuje tablicę JSON w obiekcie blob, gdzie każdy element tablicy zostaje oddzielnym dokumentem wyszukiwania.  |
| **`jsonLines`** | Wiele na obiekt BLOB | Analizuje obiekt BLOB, który zawiera wiele jednostek JSON (również tablicę), z pojedynczymi elementami oddzielonymi przez nowy wiersz. Indeksator uruchamia nowy dokument wyszukiwania po każdym nowym wierszu. |

Dla obu **`jsonArray`** i **`jsonLines`** , należy przejrzeć [indeksowanie jednego obiektu BLOB, aby utworzyć wiele dokumentów wyszukiwania](search-howto-index-one-to-many-blobs.md) , aby zrozumieć, jak indeksator usługi BLOB obsługuje Uściślanie klucza dokumentu dla wielu dokumentów wyszukiwania utworzonych na podstawie tego samego obiektu BLOB.

W definicji indeksatora można opcjonalnie ustawić [mapowania pól](search-indexer-field-mappings.md) , aby określić, które właściwości źródłowego dokumentu JSON są używane do wypełniania docelowego indeksu wyszukiwania. Na przykład w przypadku korzystania z **`jsonArray`** trybu analizowania, jeśli tablica istnieje jako właściwość niższego poziomu, można ustawić **`document root`** Właściwość wskazującą, gdzie tablica jest umieszczona w obiekcie blob.

W poniższych sekcjach opisano każdy tryb bardziej szczegółowo. Jeśli nie znasz klientów i koncepcji indeksatora, zobacz [Tworzenie indeksatora wyszukiwania](search-howto-create-indexers.md). Należy również zapoznać się ze szczegółami dotyczącymi [konfiguracji podstawowej indeksatora obiektów BLOB](search-howto-indexing-azure-blob-storage.md), która nie jest powtarzana w tym miejscu.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indeksowanie pojedynczych dokumentów JSON (jeden na obiekt BLOB)

Domyślnie indeksatory obiektów BLOB analizują obiekty blob JSON jako pojedynczy fragment tekstu, jeden dokument wyszukiwania dla każdego obiektu BLOB w kontenerze. Jeśli kod JSON jest strukturalny, dokument wyszukiwania może odzwierciedlać tę strukturę, przy czym poszczególne elementy reprezentowane są jako pojedyncze pola. Załóżmy na przykład, że masz następujący dokument JSON w usłudze Azure Blob Storage:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Indeksator obiektu BLOB analizuje dokument JSON w pojedynczym dokumencie wyszukiwania, ładując indeks przez dopasowanie "text", "datePublished" i "Tags" ze źródła względem pól indeksowanych o identycznej nazwie i typach. Mając indeks z polami "text", "datePublished" i "Tags", indeksator obiektów BLOB może wywnioskować poprawne mapowanie bez mapowania pola obecnego w żądaniu.

Chociaż zachowanie domyślne jest jednym dokumentem wyszukiwania na obiekt BLOB JSON, ustawienie Tryb analizy JSON zmienia wewnętrzne mapowania pól zawartości, promując pola wewnątrz `content` do rzeczywistych pól w indeksie wyszukiwania. Przykładowa definicja indeksatora dla **`json`** trybu analizy może wyglądać następująco:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Podobnie jak w przypadku wszystkich indeksatorów, jeśli pola nie są wyraźnie zgodne, należy jawnie określić pojedyncze [mapowania pól](search-indexer-field-mappings.md) , chyba że są używane mapowania pól niejawnych dostępnych dla zawartości i metadanych obiektu BLOB, zgodnie z opisem w temacie [Podstawowa konfiguracja indeksatora obiektów BLOB](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>przykład JSON (pojedyncze pliki JSON w hotelu)

[Dane dokumentu JSON w hotelu](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) w serwisie GitHub są przydatne do testowania analizy kodu JSON, gdzie każdy obiekt BLOB reprezentuje strukturalny plik JSON. Pliki danych można przekazać do magazynu obiektów blob i za pomocą kreatora **importu danych** szybko oszacować sposób analizowania zawartości w poszczególnych dokumentach wyszukiwania. 

Zestaw danych składa się z pięciu obiektów blob, z których każdy zawiera dokument hotelu z kolekcją adresów i kolekcją pokojów. Indeksator obiektów BLOB wykrywa kolekcje i odzwierciedla strukturę dokumentów wejściowych w schemacie indeksu.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizowanie tablic JSON

Alternatywnie możesz użyć opcji tablicy JSON. Ta opcja jest przydatna, gdy obiekty blob zawierają tablicę poprawnie sformułowanych obiektów JSON i chcesz, aby każdy element stał się oddzielnym dokumentem wyszukiwania. Przy użyciu **`jsonArrays`** , poniższy obiekt BLOB JSON tworzy trzy osobne dokumenty z `"id"` `"text"` polami i.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** Właściwość indeksatora zawiera wartości trybu analizy. W przypadku tablicy JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>przykład jsonArrays (dane przykładowe prób klinicznych)

[Dane JSON testów klinicznych](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) w serwisie GitHub są przydatne do testowania analizy tablicy JSON. Pliki danych można przekazać do magazynu obiektów blob i za pomocą kreatora **importu danych** szybko oszacować sposób analizowania zawartości w poszczególnych dokumentach wyszukiwania. 

Zestaw danych składa się z ośmiu obiektów blob, z których każdy zawiera tablicę JSON jednostek, w sumie 100 jednostek. Jednostki różnią się w zależności od tego, które pola są wypełniane, ale końcowy wynik jest jednym dokumentem wyszukiwania dla każdej jednostki, ze wszystkich tablic, we wszystkich obiektach Blob.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analizowanie zagnieżdżonych tablic JSON

W przypadku tablic JSON mających elementy zagnieżdżone można określić, **`documentRoot`** Aby wskazać strukturę wielopoziomową. Na przykład jeśli obiekty blob wyglądają następująco:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Ta konfiguracja służy do indeksowania tablicy zawartej we `level2` Właściwości:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analizowanie jednostek JSON rozdzielonych znakami nowego wiersza

Jeśli obiekt BLOB zawiera wiele jednostek JSON rozdzielonych znakiem nowego wiersza, a każdy element ma zostać oddzielnym dokumentem wyszukiwania, użyj **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

W przypadku linii JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>przykład jsonLines (dane przykładowe caselaw)

[Zestaw danych JSON caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) w usłudze GitHub jest przydatny do testowania analizy nowego wiersza w formacie JSON. Podobnie jak w przypadku innych przykładów, można przekazać te dane do magazynu obiektów blob i użyć kreatora **importu danych** , aby szybko oszacować wpływ trybu analizowania na poszczególne obiekty blob.

Zestaw danych składa się z jednego obiektu BLOB zawierającego 10 jednostek JSON oddzielonych od nowego wiersza, gdzie każda jednostka opisuje pojedynczy przypadek prawny. Wynik końcowy to jeden dokument wyszukiwania na jednostkę.

## <a name="map-json-fields-to-search-fields"></a>Mapuj pola JSON na pola wyszukiwania

Mapowania pól są używane do kojarzenia pola źródłowego z polem docelowym w sytuacjach, gdy nazwy pól i typy nie są identyczne. Mapowania pól mogą być również używane do dopasowywania części dokumentu JSON i "podnoszenia" do pól najwyższego poziomu dokumentu wyszukiwania.

Poniższy przykład ilustruje ten scenariusz. Aby uzyskać więcej informacji na temat ogólnych mapowań pól, zobacz [mapowania pól](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Przyjmij indeks wyszukiwania o następujące pola: `text` typu `Edm.String` , `date` typu `Edm.DateTimeOffset` i `tags` typu `Collection(Edm.String)` . Zwróć uwagę na różnice między "datePublished" w źródle i `date` polu w indeksie. Aby zmapować kod JSON do żądanego kształtu, użyj następujących mapowań pól:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Pola źródłowe są określane przy użyciu notacji [wskaźnika JSON](https://tools.ietf.org/html/rfc6901) . Zacznij od ukośnika, aby odwołać się do katalogu głównego dokumentu JSON, a następnie wybierz odpowiednią właściwość (na dowolnym poziomie zagnieżdżenia), używając ścieżki rozdzielanej ukośnikiem.

Można również odwoływać się do poszczególnych elementów tablicy przy użyciu indeksu od zera. Na przykład aby wybrać pierwszy element tablicy "Tags" z powyższego przykładu, użyj mapowania pola, takiego jak:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Jeśli **`sourceFieldName`** odwołuje się do właściwości, która nie istnieje w obiekcie blob JSON, to mapowanie jest pomijane bez błędu. Takie zachowanie umożliwia kontynuowanie indeksowania w przypadku obiektów BLOB JSON, które mają inny schemat (który jest typowym przypadkiem użycia). Ponieważ nie ma sprawdzenia poprawności, należy uważnie sprawdzić mapowania dla literówków, aby nie utracić dokumentów z niewłaściwej przyczyny.
>

## <a name="next-steps"></a>Następne kroki

+ [Konfigurowanie indeksatorów obiektów BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Definiuj mapowania pól](search-indexer-field-mappings.md)
+ [Indexers overview](search-indexer-overview.md) (Omówienie indeksatorów)
+ [Jak indeksować obiekty blob w formacie CSV za pomocą indeksatora obiektów BLOB](search-howto-index-csv-blobs.md)
+ [Samouczek: wyszukiwanie danych z częściową strukturą z usługi Azure Blob Storage](search-semi-structured-data.md)