---
title: Indeksowanie obiektów BLOB zawierających wiele dokumentów
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie obiektów blob platformy Azure pod kątem zawartości tekstowej przy użyciu indeksatora usługi Azure Wyszukiwanie poznawcze BLOB, gdzie każdy obiekt BLOB może mieć jeden lub więcej dokumentów indeksu wyszukiwania.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430984"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indeksowanie obiektów BLOB w celu utworzenia wielu dokumentów wyszukiwania

Domyślnie indeksator obiektów BLOB będzie traktować zawartość obiektu BLOB jako pojedynczy dokument wyszukiwania. Aby uzyskać bardziej szczegółową reprezentację obiektu BLOB w indeksie wyszukiwania, można ustawić wartości **przeanalizmode** w celu utworzenia wielu dokumentów wyszukiwania z jednego obiektu BLOB. Wartości **przeanalizmode** , które powodują wiele dokumentów wyszukiwania `delimitedText` , obejmują (dla [woluminów CSV](search-howto-index-csv-blobs.md)) i `jsonArray` lub `jsonLines` (dla [formatu JSON](search-howto-index-json-blobs.md)).

W przypadku korzystania z dowolnego z tych trybów analizy nowe dokumenty wyszukiwania, które nastąpiły muszą mieć unikatowe klucze dokumentu, i występuje problem podczas określania, skąd pochodzi ta wartość. Nadrzędny obiekt BLOB ma co najmniej jedną unikatową wartość w postaci `metadata_storage_path property` , ale jeśli ta wartość wchodzi w skład więcej niż jednego dokumentu wyszukiwania, klucz nie jest już unikatowy w indeksie.

Aby rozwiązać ten problem, indeksator obiektu BLOB generuje `AzureSearch_DocumentKey` unikatowy identyfikator każdego podrzędnego dokumentu wyszukiwania utworzonego na podstawie pojedynczego elementu nadrzędnego obiektu BLOB. W tym artykule wyjaśniono, jak działa ta funkcja.

## <a name="one-to-many-document-key"></a>Klucz dokumentu "jeden do wielu"

Każdy dokument, który jest wyświetlany w indeksie Wyszukiwanie poznawcze platformy Azure, jest jednoznacznie identyfikowany przez klucz dokumentu. 

Gdy nie określono żadnego trybu analizowania i jeśli nie istnieje [jawne Mapowanie pól](search-indexer-field-mappings.md) w definicji indeksatora dla klucza dokumentu wyszukiwania, indeksator obiektu BLOB automatycznie mapuje `metadata_storage_path property` jako klucz dokumentu. To mapowanie zapewnia, że każdy obiekt BLOB pojawia się jako odrębny dokument wyszukiwania i zapisuje krok polegający na tym, że chcesz samodzielnie utworzyć mapowanie pola (zwykle tylko pola o identycznych nazwach i typach są mapowane automatycznie).

W przypadku korzystania z dowolnego z wymienionych powyżej trybów analizowania jeden obiekt BLOB mapuje do "wielu" dokumentów wyszukiwania, co sprawia, że klucz dokumentu opiera się wyłącznie na metadanych obiektów BLOB. Aby można było obejść to ograniczenie, usługa Azure Wyszukiwanie poznawcze może wygenerować klucz dokumentu "jeden do wielu" dla każdej jednostki wyodrębnionej z obiektu BLOB. Ta właściwość ma nazwę AzureSearch_DocumentKey i jest dodawana do poszczególnych jednostek wyodrębnionych z obiektu BLOB. Wartość tej właściwości gwarantuje, że jest unikatowa dla każdej jednostki w obiektach Blob, a jednostki będą widoczne jako osobne dokumenty wyszukiwania.

Domyślnie, gdy nie są określone żadne jawne mapowania pól dla pola indeks klucza, `AzureSearch_DocumentKey` jest ono mapowane na ten element przy użyciu `base64Encode` funkcji mapowania pól.

## <a name="example"></a>Przykład

Załóżmy, że masz definicję indeksu z następującymi polami:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

I kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1.jsna_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.jsna_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Gdy tworzysz indeksator i ustawisz **dla opcji** `jsonLines` -bez określania wszystkich jawnych mapowań pól klucza, następujące mapowanie zostanie zastosowane niejawnie.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Ta konfiguracja spowoduje odróżnienie kluczy dokumentów, podobnie jak na poniższej ilustracji (identyfikator kodowany algorytmem Base64 skrócony dla zwięzłości).

| ID (Identyfikator) | temperature | ciśnienie | sygnatura czasowa |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017 – 05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapowanie pola niestandardowego dla pola klucza indeksu

Przy założeniu, że w poprzednim przykładzie jest to sama definicja indeksu, Załóżmy, że kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1.jsna_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jsna_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Podczas tworzenia indeksatora przy użyciu elementu `delimitedText` **analizowaniamode** może zajść konieczność skonfigurowania funkcji mapowania pól do pola klucza w następujący sposób:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Jednak to mapowanie _nie_ powoduje, że 4 dokumenty wyświetlane w indeksie, ponieważ `recordid` pole nie jest unikatowe w obiektach _BLOB_. W związku z tym zalecamy użycie niejawnego mapowania pól stosowanego z `AzureSearch_DocumentKey` właściwości do pola indeksu klucza dla trybów analizy "jeden do wielu".

Jeśli chcesz skonfigurować jawne Mapowanie pól, upewnij się, że _sourceField_ różni się dla poszczególnych jednostek **we wszystkich** obiektach Blob.

> [!NOTE]
> Podejście używane w celu `AzureSearch_DocumentKey` zapewnienia unikatowości dla wyodrębnionej jednostki może ulec zmianie, dlatego nie należy polegać na jej wartości dla potrzeb aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli nie znasz już podstawowej struktury i przepływu pracy indeksowania obiektów blob, należy najpierw przejrzeć [indeksowanie BLOB Storage platformy Azure przy użyciu usługi azure wyszukiwanie poznawcze](search-howto-index-json-blobs.md) . Aby uzyskać więcej informacji na temat trybów analizowania dla różnych typów zawartości obiektów blob, zapoznaj się z następującymi artykułami.

> [!div class="nextstepaction"]
> [Indeksowanie obiektów BLOB](search-howto-index-csv-blobs.md) 
>  woluminów CSV [Indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md)
