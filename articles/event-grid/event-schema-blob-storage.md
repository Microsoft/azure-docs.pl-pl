---
title: Usługa Azure Blob Storage jako źródło siatki zdarzeń
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń magazynu obiektów blob z usługą Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: cfc6e4790b67137b423cc90d93874d4914f81251
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393372"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Usługa Azure Blob Storage jako źródło siatki zdarzeń

Ten artykuł zawiera właściwości i schemat zdarzeń magazynu obiektów blob.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md). Zawiera również listę szybkich uruchomień i samouczków do korzystania z usługi Azure Blob Storage jako źródła zdarzeń.


>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia v2),** **BlockBlobStorage**i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (genral purpose v1)** *nie* obsługuje integracji z siatką zdarzeń.

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="list-of-events-for-blob-rest-apis"></a>Lista zdarzeń dla interfejsów API REST obiektów blob

Te zdarzenia są wyzwalane, gdy klient tworzy, zastępuje lub usuwa obiekt blob, wywołując interfejsy API SPOCZYNK obiektów blob.

 |Nazwa wydarzenia |Opis|
 |----------|-----------|
 |**Microsoft.Storage.BlobTworzenie** |Wyzwalane podczas tworzenia lub zastępowywał obiekt blob. <br>W szczególności to zdarzenie jest wyzwalane, `PutBlockList`gdy `CopyBlob` klienci używają `PutBlob`, lub operacji, które są dostępne w interfejsie API REST obiektów blob.   |
 |**Microsoft.Storage.BlobDeleted** |Wyzwalane po usunięciu obiektu blob. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci wywołają `DeleteBlob` operację, która jest dostępna w interfejsie API REST obiektów blob. |

> [!NOTE]
> Jeśli chcesz upewnić się, że **zdarzenie Microsoft.Storage.BlobCreated** jest wyzwalane tylko wtedy, gdy `CopyBlob`blok `PutBlob`blob jest całkowicie zatwierdzony, filtruj zdarzenie dla wywołań interfejsu API , i `PutBlockList` REST. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft.Storage.BlobCreated** tylko wtedy, gdy dane są w pełni zaangażowane w blokowy obiekt blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista zdarzeń dla interfejsów API REST usługi Azure Data Storage Gen 2

Te zdarzenia są wyzwalane po włączeniu hierarchicznego obszaru nazw na koncie magazynu, a klienci będą wywoływać interfejsy API REST usługi Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen2, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Nazwa wydarzenia|Opis|
|----------|-----------|
|**Microsoft.Storage.BlobTworzenie** | Wyzwalane podczas tworzenia lub zastępowywał obiekt blob. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci używają `CreateFile` i `FlushWithClose` operacji, które są dostępne w interfejsie API REST usługi Azure Data Lake Storage Gen2. |
|**Microsoft.Storage.BlobDeleted** |Wyzwalane po usunięciu obiektu blob. <br>W szczególności to zdarzenie jest również wyzwalane, gdy klienci wywołać `DeleteFile` operację, która jest dostępna w interfejsie API REST usługi Azure Data Lake Storage Gen2. |
|**Magazyn Microsoft.BlobRenamed**|Wyzwalane, gdy zmienia się nazwę obiektu blob. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci używają `RenameFile` operacji, która jest dostępna w interfejsie API REST usługi Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectorTworzone**|Wyzwalane podczas tworzenia katalogu. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci używają `CreateDirectory` operacji, która jest dostępna w interfejsie API REST usługi Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryRenamed**|Wyzwalane po zmianie nazwy katalogu. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci używają `RenameDirectory` operacji, która jest dostępna w interfejsie API REST usługi Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryDeleted**|Wyzwalane po usunięciu katalogu. <br>W szczególności to zdarzenie jest wyzwalane, gdy klienci używają `DeleteDirectory` operacji, która jest dostępna w interfejsie API REST usługi Azure Data Lake Storage Gen2.|

> [!NOTE]
> Jeśli chcesz upewnić się, że **zdarzenie Microsoft.Storage.BlobCreated** jest wyzwalane tylko wtedy, gdy `FlushWithClose` blok obiektu blob jest całkowicie zatwierdzony, filtruj zdarzenie dla wywołania interfejsu API REST. To wywołanie interfejsu API wyzwala **zdarzenie Microsoft.Storage.BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do bloku obiektu blob. Aby dowiedzieć się, jak utworzyć filtr, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

### <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane o tym zdarzeniu do subskrybowania punktu końcowego.

Ta sekcja zawiera przykład tego, jak będą wyglądać te dane dla każdego zdarzenia magazynu obiektów blob.

### <a name="microsoftstorageblobcreated-event"></a>Zdarzenie Microsoft.Storage.BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft.Storage.BlobCreated (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów blob ma hierarchiczny obszar nazw, dane wyglądają podobnie do poprzedniego przykładu, z wyjątkiem tych zmian:

* Klucz `dataVersion` jest ustawiony na `2`wartość .

* Klucz `data.api` jest ustawiony na `CreateFile` `FlushWithClose`ciąg lub .

* Klucz `contentOffset` znajduje się w zestawie danych.

> [!NOTE]
> Jeśli aplikacje `PutBlockList` używają operacji do przekazywania nowego obiektu blob do konta, dane nie będą zawierać tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Zdarzenie Microsoft.Storage.BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft.Storage.BlobDeleted (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów blob ma hierarchiczny obszar nazw, dane wyglądają podobnie do poprzedniego przykładu, z wyjątkiem tych zmian:

* Klucz `dataVersion` jest ustawiony na `2`wartość .

* Klucz `data.api` jest ustawiony na `DeleteFile`ciąg .

* Klucz `url` zawiera ścieżkę `dfs.core.windows.net`.

> [!NOTE]
> Jeśli aplikacje `DeleteBlob` używają operacji do usunięcia obiektu blob z konta, dane nie będą zawierać tych zmian.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Zdarzenie Microsoft.Storage.BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Zdarzenie Microsoft.Storage.DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Zdarzenie Microsoft.Storage.DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryOstawione zdarzenie

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | ciąg | Operacja, która wyzwoliła zdarzenie. |
| identyfikator żądania klienta | ciąg | identyfikator żądania dostarczonego przez klienta dla operacji interfejsu API magazynu. Ten identyfikator może służyć do skorelowania z dziennikami diagnostycznymi usługi Azure Storage przy użyciu pola "client-request-id" w dziennikach i może być dostarczany w żądaniach klientów przy użyciu nagłówka "x-ms-client-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Requestid | ciąg | Identyfikator żądania wygenerowanego przez usługę dla operacji interfejsu API magazynu. Może służyć do skorelowania z dziennikami diagnostycznymi usługi Azure Storage przy użyciu pola "request-id-header" w dziennikach i jest zwracany z inicjowania wywołania interfejsu API w nagłówku "x-ms-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | ciąg | Wartość, której można użyć do wykonywania operacji warunkowo. |
| Contenttype | ciąg | Typ zawartości określony dla obiektu blob. |
| Contentlength | liczba całkowita | Rozmiar obiektu blob w bajtach. |
| typ obiektu blob | ciąg | Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| zawartośćOffset | numer | Przesunięcie w bajtach operacji zapisu podjęte w punkcie, w którym aplikacja wyzwalająca zdarzenia zakończeniu zapisu do pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach magazynu obiektów blob, które mają hierarchiczną przestrzeń nazw.|
| destinationUrl |ciąg | Adres URL pliku, który będzie istnieć po zakończeniu operacji. Na przykład jeśli nazwa pliku zostanie `destinationUrl` zmieniona, właściwość zawiera adres URL nowej nazwy pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach magazynu obiektów blob, które mają hierarchiczną przestrzeń nazw.|
| źródłoUrl |ciąg | Adres URL pliku, który istnieje przed operacją. Na przykład jeśli nazwa pliku zostanie `sourceUrl` zmieniona, zawiera adres URL oryginalnej nazwy pliku przed operacją zmiany nazwy. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach magazynu obiektów blob, które mają hierarchiczną przestrzeń nazw. |
| url | ciąg | Ścieżka do obiektu blob. <br>Jeśli klient używa interfejsu API REST obiektów Blob, adres URL ma tę strukturę: * \<nazwa\>magazynu-account .blob.core.windows.net/\<nazwa\>/\<pliku\>kontenera*. <br>Jeśli klient używa interfejsu API REST magazynu usługi Data Lake, adres URL ma tę strukturę: * \<nazwa konta magazynu\>.dfs.core.windows.net/ nazwa\<pliku file-system-name\>/\<\>*. |
| Cykliczne | ciąg | `True`, aby wykonać operację we wszystkich katalogach podrzędnych; w `False`przeciwnym razie . <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach magazynu obiektów blob, które mają hierarchiczną przestrzeń nazw. |
| Sekwenser | ciąg | Wartość nieprzezroczystego ciągu reprezentująca logiczną sekwencję zdarzeń dla określonej nazwy obiektu blob.  Użytkownicy mogą używać standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń o tej samej nazwie obiektu blob. |
| storageDiagnostics | obiekt | Dane diagnostyczne od czasu do czasu zawarte przez usługę Azure Storage. Gdy obecny, powinny być ignorowane przez konsumentów zdarzeń. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Szybki start: kierowanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Szybki start: kierowanie zdarzeń magazynu obiektów Blob do niestandardowego punktu końcowego sieci Web za pomocą programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać programu Azure PowerShell do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Szybki start: tworzenie i rozsyłanie zdarzeń magazynu obiektów Blob za pomocą witryny Azure portal](blob-event-quickstart-portal.md) | Pokazuje, jak używać portalu do wysyłania zdarzeń magazynu obiektów blob do elementu WebHook. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla konta magazynu obiektów Blob](./scripts/event-grid-cli-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta magazynu obiektów Blob. Wysyła zdarzenie do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń dla konta magazynu obiektów Blob](./scripts/event-grid-powershell-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta magazynu obiektów Blob. Wysyła zdarzenie do elementu WebHook. |
| [Szablon Menedżera zasobów: tworzenie magazynu i subskrypcji obiektów Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. Wysyła zdarzenia do elementu WebHook. |
| [Omówienie: reagowanie na zdarzenia magazynu obiektów Blob](../storage/blobs/storage-blob-event-overview.md) | Omówienie integrowania magazynu obiektów Blob z siatką zdarzeń. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby zapoznać się z wprowadzeniem do pracy ze zdarzeniami magazynu obiektów blob, zobacz [Rozsyłanie zdarzeń magazynu obiektów Blob — interfejsu wiersza polecenia platformy Azure.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) 
