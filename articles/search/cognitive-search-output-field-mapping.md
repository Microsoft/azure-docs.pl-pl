---
title: Mapuj dane wejściowe do pól wyjściowych
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i Wzbogacaj pola danych źródłowych i Mapuj do pól wyjściowych w indeksie Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74280981"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapować pola wzbogacone AI na indeks wyszukiwania

Ten artykuł zawiera informacje na temat mapowania ulepszonych pól wejściowych do pól wyjściowych w indeksie, który można przeszukiwać. Po [zdefiniowaniu zestawu umiejętności](cognitive-search-defining-skillset.md)należy zamapować pola danych wyjściowych o wszelkie umiejętności, które bezpośrednio współtworzą wartości w danym polu w indeksie wyszukiwania. 

Mapowania pól wyjściowych są wymagane do przeniesienia zawartości z ulepszonych dokumentów do indeksu.  Wzbogacony dokument jest w rzeczywistości drzewem informacji, a nawet w przypadku obsługi typów złożonych w indeksie czasami warto przekształcić informacje z wzbogaconego drzewa do bardziej prostego typu (na przykład tablicy ciągów). Mapowania pól wyjściowych umożliwiają wykonywanie transformacji kształtu danych przez spłaszczenie informacji.

## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zmapować pola, Dodaj `outputFieldMappings` je do definicji indeksatora, jak pokazano poniżej:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Treść żądania jest uporządkowana w następujący sposób:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Dla każdego mapowania pola danych wyjściowych Ustaw lokalizację danych w drzewie dokumentu wzbogaconego (sourceFieldName) i nazwę pola jako odwołanie w indeksie (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Spłaszczanie informacji z typów złożonych 

Ścieżka w sourceFieldName może reprezentować jeden element lub wiele elementów. W powyższym przykładzie ```/document/content/sentiment``` reprezentuje pojedynczą wartość liczbową, ```/document/content/organizations/*/description``` a jednocześnie reprezentuje kilka opisów organizacji. 

W przypadkach, gdy istnieje kilka elementów, są one "spłaszczone" do tablicy zawierającej poszczególne elementy. 

Dokładniej, na ```/document/content/organizations/*/description``` przykład dane w polu *opisy* będą wyglądały jak płaska tablica opisów przed rozpoczęciem indeksowania:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Jest to istotna zasada, więc zapewnimy kolejną przykład. Załóżmy, że masz tablicę typów złożonych jako część drzewa wzbogacania. Załóżmy, że istnieje element członkowski o nazwie customEntities, który ma tablicę typów złożonych, takich jak opisana poniżej.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Załóżmy, że indeks ma pole o nazwie "choroby" typu kolekcja (EDM. String), gdzie chcesz przechowywać każdą z nazw jednostek. 

Można to zrobić łatwo przy użyciu symbolu "\*" w następujący sposób:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Ta operacja spowoduje po prostu "Spłaszcz" każdą z nazw elementów customEntities w jedną tablicę ciągów, takich jak:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Następne kroki
Po zmapowaniu ulepszonych pól do pól z możliwością wyszukiwania można ustawić atrybuty pola dla każdego z pól z możliwością wyszukiwania [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji na temat mapowania pól, zobacz [mapowania pól w usłudze Azure wyszukiwanie poznawcze indeksatory](search-indexer-field-mappings.md).
