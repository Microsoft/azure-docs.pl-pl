---
title: Zmieniono i usunięto obiekty blob
titleSuffix: Azure Cognitive Search
description: Po utworzeniu początkowego indeksu wyszukiwania, który importuje z usługi Azure Blob Storage, kolejne indeksowanie może pobrać tylko te obiekty blob, które zostały zmienione lub usunięte. W tym artykule objaśniono szczegółowe informacje.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383538"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Wykrywanie zmian i usuwania w indeksie obiektów BLOB (Azure Wyszukiwanie poznawcze)

Po utworzeniu początkowego indeksu wyszukiwania można chcieć, aby kolejne zadania indeksatora miały tylko możliwość pobrania nowych i zmienionych dokumentów. W przypadku wyszukiwania zawartości pochodzącej z usługi Azure Blob Storage wykrywanie zmian odbywa się automatycznie w przypadku użycia harmonogramu do wyzwolenia indeksowania. Domyślnie usługa ponownie indeksuje tylko zmienione obiekty blob zgodnie z `LastModified` sygnaturą czasową obiektu BLOB. W przeciwieństwie do innych źródeł danych obsługiwanych przez indeksatory wyszukiwania, obiekty blob zawsze mają sygnaturę czasową, co eliminuje konieczność ręcznego skonfigurowania zasad wykrywania zmian.

Chociaż wykrywanie zmian jest określone, wykrywanie usuwania nie jest. Jeśli chcesz wykryć usunięte dokumenty, upewnij się, że użyto podejścia "usuwanie nietrwałe". Po usunięciu obiektów blob z prawej strony, odpowiednie dokumenty nie zostaną usunięte z indeksu wyszukiwania.

Istnieją dwa sposoby implementacji nietrwałego podejścia do usuwania:

+ Natywne usuwanie nietrwałe obiektów BLOB (wersja zapoznawcza), opisane dalej
+ [Usuwanie nietrwałe przy użyciu metadanych niestandardowych](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Natywne usuwanie nietrwałego obiektu BLOB (wersja zapoznawcza)

W ramach tego podejścia do wykrywania usuwania Wyszukiwanie poznawcze zależy od [natywnej funkcji usuwania nietrwałego obiektu BLOB](../storage/blobs/soft-delete-blob-overview.md) w usłudze Azure Blob Storage, aby określić, czy obiekty blob zostały przeniesione do stanu nietrwałego usunięcia. Gdy obiekty blob są wykrywane w tym stanie, indeksator wyszukiwania używa tych informacji do usunięcia odpowiedniego dokumentu z indeksu.

> [!IMPORTANT]
> Obsługa natywnego usuwania nietrwałego obiektu BLOB jest w wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2020-06-30 — wersja zapoznawcza](./search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

### <a name="prerequisites"></a>Wymagania wstępne

+ [Włącz usuwanie nietrwałe dla obiektów BLOB](../storage/blobs/soft-delete-blob-enable.md).
+ Obiekty blob muszą znajdować się w kontenerze usługi Azure Blob Storage. Wyszukiwanie poznawcze natywne zasady usuwania nietrwałego obiektu BLOB są nieobsługiwane w przypadku obiektów blob z Azure Data Lake Storage Gen2.
+ Klucze dokumentów dla dokumentów w indeksie muszą być zamapowane na właściwości obiektu BLOB lub obiektu BLOB.
+ `api-version=2020-06-30-Preview`Aby skonfigurować obsługę usuwania nietrwałego, należy użyć interfejsu API REST (wersja zapoznawcza).

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Jak skonfigurować wykrywanie usuwania przy użyciu natywnego usuwania nietrwałego

1. W usłudze BLOB Storage w przypadku włączania usuwania nietrwałego Ustaw dla zasad przechowywania wartość, która jest znacznie wyższa niż harmonogram interwału indeksatora. W ten sposób, jeśli wystąpi problem z uruchamianiem indeksatora lub jeśli masz dużą liczbę dokumentów do indeksowania, istnieje dużo czasu, aby indeksator mógł ostatecznie przetworzyć usunięte nietrwałe obiekty blob. Indeksatory usługi Azure Wyszukiwanie poznawcze spowodują usunięcie dokumentu z indeksu tylko wtedy, gdy przetwarza on obiekt BLOB w stanie nietrwałego usunięcia.

1. W Wyszukiwanie poznawcze Ustaw natywne zasady wykrywania usuwania nietrwałego obiektów BLOB w źródle danych. Przykład przedstawiono poniżej. Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, należy użyć interfejsu API REST.

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

1. [Uruchom indeksator](/rest/api/searchservice/run-indexer) lub ustaw indeksator do uruchomienia [zgodnie z harmonogramem](search-howto-schedule-indexers.md). Gdy indeksator działa i przetwarza obiekt BLOB ze stanem usuwania nietrwałego, odpowiedni dokument wyszukiwania zostanie usunięty z indeksu.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Ponowne indeksowanie nieusuniętych obiektów BLOB (przy użyciu natywnych zasad usuwania nietrwałego)

W przypadku przywrócenia nietrwałego usuniętego obiektu BLOB w magazynie obiektów BLOB indeksator nie zawsze będzie ponownie indeksować go. Wynika to z faktu, że indeksator używa `LastModified` sygnatury czasowej obiektu BLOB, aby określić, czy jest konieczne indeksowanie. Po cofnięciu usunięcia nietrwałego usuniętego obiektu BLOB jego `LastModified` sygnatura czasowa nie jest aktualizowana, więc jeśli indeksator przetworzył już obiekty blob o większej liczbie `LastModified` sygnatur czasowych, nie będzie on ponownie indeksować nieusuniętego obiektu BLOB. 

Aby upewnić się, że nieusunięty obiekt BLOB jest ponownie indeksowany, należy zaktualizować `LastModified` sygnaturę czasową obiektu BLOB. Jednym ze sposobów na wykonanie tej czynności jest ponowne zapisanie metadanych tego obiektu BLOB. Nie musisz zmieniać metadanych, ale ponowne Zapisywanie metadanych spowoduje zaktualizowanie `LastModified` sygnatury czasowej obiektu BLOB, tak aby indeksator mógł je pobrać.

## <a name="soft-delete-using-custom-metadata"></a>Usuwanie nietrwałe przy użyciu metadanych niestandardowych

Ta metoda używa metadanych obiektu BLOB w celu określenia, czy dokument wyszukiwania powinien zostać usunięty z indeksu. Ta metoda wymaga dwóch oddzielnych akcji, usunięcie dokumentu wyszukiwania z indeksu, a następnie usunięcie obiektu BLOB w usłudze Azure Storage.

Istnieją kroki, które należy wykonać w przypadku usługi BLOB Storage i Wyszukiwanie poznawcze, ale nie ma żadnych innych zależności funkcji. Ta funkcja jest obsługiwana w ogólnie dostępnych interfejsach API.

1. Dodaj niestandardową parę klucz-wartość metadanych do obiektu BLOB, aby wskazać, że usługa Azure Wyszukiwanie poznawcze jest usuwana logicznie.

1. Skonfiguruj zasady wykrywania nietrwałej kolumny usuwania w źródle danych. Na przykład następujące zasady uznają obiekt BLOB, który ma zostać usunięty, jeśli ma właściwość metadanych `IsDeleted` o wartości `true` :

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

1. Gdy indeksator przetworzył obiekt BLOB i usunął dokument z indeksu, można usunąć obiekt BLOB w usłudze Azure Blob Storage.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Ponowne indeksowanie nieusuniętych obiektów BLOB (przy użyciu metadanych niestandardowych)

Gdy indeksator przetwarza usunięty obiekt BLOB i usuwa odpowiedni dokument wyszukiwania z indeksu, nie będzie ponownie odwiedzany ten obiekt BLOB, jeśli zostanie przywrócony później, jeśli `LastModified` znacznik czasu obiektu BLOB jest starszy niż ostatni przebieg indeksatora.

Jeśli chcesz ponownie zindeksować ten dokument, Zmień `"softDeleteMarkerValue" : "false"` dla tego obiektu BLOB i uruchom indeksator.

## <a name="next-steps"></a>Następne kroki

+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Jak skonfigurować indeksator obiektów BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)