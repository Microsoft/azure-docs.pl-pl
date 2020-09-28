---
title: Zmieniono i usunięto obiekty blob
titleSuffix: Azure Cognitive Search
description: Po utworzeniu początkowego indeksu wyszukiwania, który importuje z usługi Azure Blob Storage, kolejne indeksowanie może pobrać tylko te obiekty blob, które zostały zmienione lub usunięte. W tym artykule objaśniono szczegółowe informacje.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403794"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Jak skonfigurować wykrywanie zmian i usuwania dla obiektów BLOB w usłudze Azure Wyszukiwanie poznawcze Indexing

Po utworzeniu początkowego indeksu wyszukiwania można skonfigurować kolejne zadania indeksatora w celu pobrania tylko tych dokumentów, które zostały utworzone lub usunięte od momentu początkowego uruchomienia. W przypadku wyszukiwania zawartości pochodzącej z usługi Azure Blob Storage wykrywanie zmian odbywa się automatycznie w przypadku użycia harmonogramu do wyzwolenia indeksowania. Domyślnie usługa ponownie indeksuje tylko zmienione obiekty blob zgodnie z `LastModified` sygnaturą czasową obiektu BLOB. W przeciwieństwie do innych źródeł danych obsługiwanych przez indeksatory wyszukiwania, obiekty blob zawsze mają sygnaturę czasową, co eliminuje konieczność ręcznego skonfigurowania zasad wykrywania zmian.

Chociaż wykrywanie zmian jest określone, wykrywanie usuwania nie jest. Jeśli chcesz wykryć usunięte dokumenty, upewnij się, że użyto podejścia "usuwanie nietrwałe". Po usunięciu obiektów blob z prawej strony, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania.

Istnieją dwa sposoby implementacji nietrwałego podejścia do usuwania. Oba te elementy zostały opisane poniżej.

## <a name="native-blob-soft-delete-preview"></a>Natywne usuwanie nietrwałego obiektu BLOB (wersja zapoznawcza)

> [!IMPORTANT]
> Obsługa natywnego usuwania nietrwałego obiektu BLOB jest w wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2020-06-30 — wersja zapoznawcza](./search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

> [!NOTE]
> W przypadku używania natywnych zasad usuwania nietrwałego obiektu BLOB klucze dokumentów dla dokumentów w indeksie muszą być właściwościami obiektu BLOB lub obiektami BLOB.

W tej metodzie będziesz używać [natywnej funkcji usuwania nietrwałego obiektu BLOB](../storage/blobs/soft-delete-blob-overview.md) oferowanej przez usługę Azure Blob Storage. Jeśli na koncie magazynu jest włączone natywne trwałe usuwanie obiektów blob, źródło danych ma natywny zestaw zasad usuwania nietrwałego, a indeksator odnajdzie obiekt BLOB, który został przeniesiony do nietrwałego stanu usuniętego, indeksator usunie ten dokument z indeksu. Natywne zasady usuwania nietrwałego obiektu BLOB nie są obsługiwane podczas indeksowania obiektów blob z Azure Data Lake Storage Gen2.

Wykonaj następujące kroki:

1. Włącz [natywne usuwanie nietrwałe dla magazynu obiektów blob platformy Azure](../storage/blobs/soft-delete-blob-overview.md). Zalecamy ustawienie zasad przechowywania na wartość, która jest znacznie wyższa niż harmonogram interwału indeksatora. W ten sposób, jeśli wystąpi problem z uruchamianiem indeksatora lub jeśli masz dużą liczbę dokumentów do indeksowania, istnieje dużo czasu, aby indeksator mógł ostatecznie przetworzyć usunięte nietrwałe obiekty blob. Indeksatory usługi Azure Wyszukiwanie poznawcze spowodują usunięcie dokumentu z indeksu tylko wtedy, gdy przetwarza on obiekt BLOB w stanie nietrwałego usunięcia.

1. Skonfiguruj zasady wykrywania natywnych usunięć obiektów BLOB w źródle danych. Przykład przedstawiono poniżej. Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, należy użyć interfejsu API REST.

1. Uruchom indeksator lub ustaw indeksator do uruchomienia zgodnie z harmonogramem. Gdy indeksator działa i przetwarza obiekt BLOB, dokument zostanie usunięty z indeksu.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Ponowne indeksowanie usuniętych obiektów BLOB (przy użyciu natywnych zasad usuwania nietrwałego)

Jeśli usuniesz obiekt BLOB z usługi Azure Blob Storage z natywnym usuwaniem nieaktywnym włączonym na koncie magazynu, obiekt BLOB przejdzie do nietrwałego stanu usuniętego, co umożliwi usunięcie tego obiektu BLOB w ramach okresu przechowywania. W przypadku odwrócenia usunięcia po przetworzeniu indeksatora indeksator nie zawsze będzie indeksować przywróconego obiektu BLOB. Wynika to z faktu, że indeksator określa, które obiekty blob mają być indeksowane na podstawie `LastModified` sygnatury czasowej obiektu BLOB. Po usunięciu nietrwałego usuniętego obiektu BLOB jego `LastModified` sygnatura czasowa nie jest aktualizowana, więc jeśli indeksator przetworzył już obiekty blob o większej liczbie `LastModified` sygnatur czasowych, nie będzie on ponownie indeksować usuniętego obiektu BLOB. 

Aby upewnić się, że nieusunięty obiekt BLOB jest ponownie indeksowany, należy zaktualizować `LastModified` sygnaturę czasową obiektu BLOB. Jednym ze sposobów na wykonanie tej czynności jest ponowne zapisanie metadanych tego obiektu BLOB. Nie musisz zmieniać metadanych, ale ponowne Zapisywanie metadanych spowoduje zaktualizowanie `LastModified` sygnatury czasowej obiektu BLOB, aby indeksator wiedział, że musi on ponownie zindeksować ten obiekt BLOB.

## <a name="soft-delete-using-custom-metadata"></a>Usuwanie nietrwałe przy użyciu metadanych niestandardowych

W tej metodzie metadane obiektu BLOB zostaną użyte do wskazania, kiedy dokument powinien zostać usunięty z indeksu wyszukiwania. Ta metoda wymaga dwóch oddzielnych akcji, usunięcie dokumentu wyszukiwania z indeksu, a następnie usunięcie obiektu BLOB w usłudze Azure Storage.

Wykonaj następujące kroki:

1. Dodaj niestandardową parę klucz-wartość metadanych do obiektu BLOB, aby wskazać, że usługa Azure Wyszukiwanie poznawcze jest usuwana logicznie.

1. Skonfiguruj zasady wykrywania nietrwałej kolumny usuwania w źródle danych. Przykład przedstawiono poniżej.

1. Gdy indeksator przetworzył obiekt BLOB i usunął dokument z indeksu, można usunąć obiekt BLOB w usłudze Azure Blob Storage.

Na przykład następujące zasady uznają obiekt BLOB, który ma zostać usunięty, jeśli ma właściwość metadanych `IsDeleted` o wartości `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Ponowne indeksowanie usuniętych obiektów BLOB (przy użyciu metadanych niestandardowych)

Gdy indeksator przetwarza usunięty obiekt BLOB i usuwa odpowiedni dokument wyszukiwania z indeksu, nie będzie ponownie odwiedzany ten obiekt BLOB, jeśli zostanie przywrócony później, jeśli `LastModified` znacznik czasu obiektu BLOB jest starszy niż ostatni przebieg indeksatora.

Jeśli chcesz ponownie zindeksować ten dokument, Zmień `"softDeleteMarkerValue" : "false"` dla tego obiektu BLOB i uruchom indeksator.

## <a name="see-also"></a>Zobacz też

* [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
* [Jak skonfigurować indeksator obiektów BLOB](search-howto-indexing-azure-blob-storage.md)
* [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)