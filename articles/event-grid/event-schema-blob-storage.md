---
title: Blob Storage platformy Azure jako źródło Event Grid
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi BLOB Storage z Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 893e86ecf220ceb327eed9c6f95be4c7ed1afb1c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363648"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Blob Storage platformy Azure jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń usługi BLOB Storage. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków umożliwiających korzystanie z usługi Azure Blob Storage jako źródła zdarzeń.


>[!NOTE]
> Tylko konta magazynu typu **StorageV2 (ogólnego przeznaczenia w wersji 2)**, **BlockBlobStorage** i **BlobStorage** obsługują integrację zdarzeń. **Magazyn (ogólnego przeznaczenia w wersji 1)** *nie obsługuje integracji* z programem Event Grid.

## <a name="available-event-types"></a>Dostępne typy zdarzeń

### <a name="list-of-events-for-blob-rest-apis"></a>Lista zdarzeń dla interfejsów API REST obiektów BLOB

Te zdarzenia są wyzwalane, gdy klient tworzy, zastępuje lub usuwa obiekt BLOB przez wywoływanie interfejsów API REST obiektów BLOB.

> [!NOTE]
> `$logs` `$blobchangefeed` Kontenery i nie są zintegrowane z Event Grid, więc działanie w tych kontenerach nie spowoduje wygenerowania zdarzeń. Ponadto użycie punktu końcowego systemu plików DFS *`(abfss://URI) `* dla niehierarchicznych kont z obsługą przestrzeni nazw nie spowoduje wygenerowania zdarzeń, ale punkt końcowy obiektów BLOB *`(wasb:// URI)`* będzie generować zdarzenia.

 |Nazwa zdarzenia |Opis|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Wyzwalane, gdy obiekt BLOB zostanie utworzony lub zastąpiony. <br>To zdarzenie jest wyzwalane, gdy klienci korzystają z `PutBlob` , `PutBlockList` lub `CopyBlob` operacji, które są dostępne w INTERFEJSie API REST usługi BLOB.   |
 |**Microsoft. Storage. BlobDeleted** |Wyzwalane po usunięciu obiektu BLOB. <br>To zdarzenie jest wyzwalane, gdy klienci wywołują `DeleteBlob` operację, która jest dostępna w interfejsie API REST usługi BLOB. |

> [!NOTE]
> W przypadku **usługi Azure Blob Storage**, jeśli chcesz mieć pewność, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, odfiltruj zdarzenie dla `CopyBlob` `PutBlob` `PutBlockList` wywołań interfejsu API, i. Te wywołania interfejsu API wyzwalają zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](./how-to-filter-events.md).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista zdarzeń dla Azure Data Lake Storage interfejsu API REST generacji 2

Te zdarzenia są wyzwalane po włączeniu hierarchicznej przestrzeni nazw na koncie magazynu, a klienci używają Azure Data Lake Storage Gen2 interfejsów API REST. Aby uzyskać więcej informacji na bout Azure Data Lake Storage Gen2, zobacz [wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Nazwa zdarzenia|Opis|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Wyzwalane, gdy obiekt BLOB zostanie utworzony lub zastąpiony. <br>To zdarzenie jest wyzwalane, gdy klienci korzystają z `CreateFile` `FlushWithClose` operacji i, które są dostępne w interfejsie API REST Azure Data Lake Storage Gen2. |
|**Microsoft. Storage. BlobDeleted** |Wyzwalane po usunięciu obiektu BLOB. <br>To zdarzenie jest również wyzwalane, gdy klienci wywołują `DeleteFile` operację, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2. |
|**Microsoft. Storage. BlobRenamed**|Wyzwalane po zmianie nazwy obiektu BLOB. <br>To zdarzenie jest wyzwalane, gdy klienci używają tej `RenameFile` operacji, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryCreated**|Wyzwalane po utworzeniu katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają tej `CreateDirectory` operacji, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryRenamed**|Wyzwalane, gdy zostanie zmieniona nazwa katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają tej `RenameDirectory` operacji, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryDeleted**|Wyzwalane po usunięciu katalogu. <br>To zdarzenie jest wyzwalane, gdy klienci używają tej `DeleteDirectory` operacji, która jest dostępna w interfejsie API REST Azure Data Lake Storage Gen2.|

> [!NOTE]
> Aby uzyskać **Azure Data Lake Storage Gen2**, jeśli chcesz upewnić się, że zdarzenie **Microsoft. Storage. BlobCreated** jest wyzwalane tylko wtedy, gdy blokowy obiekt BLOB jest całkowicie zatwierdzony, odfiltruj zdarzenie dla `FlushWithClose` wywołania interfejsu API REST. To wywołanie interfejsu API wyzwala zdarzenie **Microsoft. Storage. BlobCreated** tylko wtedy, gdy dane są w pełni zatwierdzone do blokowego obiektu BLOB. Aby dowiedzieć się, jak utworzyć filtr, zobacz [filtrowanie zdarzeń dla Event Grid](./how-to-filter-events.md).

## <a name="example-event"></a>Przykładowe zdarzenie
Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego. Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia usługi BLOB Storage.

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Zdarzenie Microsoft. Storage. BlobCreated

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:

* `dataVersion`Klucz ma ustawioną wartość `2` .

* `data.api`Klucz jest ustawiony na ciąg `CreateFile` lub `FlushWithClose` .

* `contentOffset`Klucz jest zawarty w zestawie danych.

> [!NOTE]
> Jeśli aplikacje używają `PutBlockList` operacji do przekazania nowego obiektu BLOB do konta, dane nie będą zawierać tych zmian.

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

### <a name="microsoftstorageblobdeleted-event"></a>Zdarzenie Microsoft. Storage. BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:

* `dataVersion`Klucz ma ustawioną wartość `2` .

* `data.api`Klucz jest ustawiony na ciąg `DeleteFile` .

* `url`Klucz zawiera ścieżkę `dfs.core.windows.net` .

> [!NOTE]
> Jeśli aplikacje używają `DeleteBlob` operacji do usuwania obiektu BLOB z konta, dane nie będą zawierać tych zmian.

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

### <a name="microsoftstorageblobrenamed-event"></a>Zdarzenie Microsoft. Storage. BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Zdarzenie Microsoft. Storage. DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Zdarzenie Microsoft. Storage. DirectoryRenamed

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Zdarzenie Microsoft. Storage. DirectoryDeleted

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

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Zdarzenie Microsoft. Storage. BlobCreated

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:

* `data.api`Klucz jest ustawiony na ciąg `CreateFile` lub `FlushWithClose` .
* `contentOffset`Klucz jest zawarty w zestawie danych.

> [!NOTE]
> Jeśli aplikacje używają `PutBlockList` operacji do przekazania nowego obiektu BLOB do konta, dane nie będą zawierać tych zmian.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Zdarzenie Microsoft. Storage. BlobDeleted

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Zdarzenie Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Jeśli konto magazynu obiektów BLOB ma hierarchiczną przestrzeń nazw, dane wyglądają podobnie jak w poprzednim przykładzie, z wyjątkiem tych zmian:


* `data.api`Klucz jest ustawiony na ciąg `DeleteFile` .
* `url`Klucz zawiera ścieżkę `dfs.core.windows.net` .

> [!NOTE]
> Jeśli aplikacje używają `DeleteBlob` operacji do usuwania obiektu BLOB z konta, dane nie będą zawierać tych zmian.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Zdarzenie Microsoft. Storage. BlobRenamed

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Zdarzenie Microsoft. Storage. DirectoryCreated

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Zdarzenie Microsoft. Storage. DirectoryRenamed

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Zdarzenie Microsoft. Storage. DirectoryDeleted

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
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
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>Właściwości zdarzenia

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `topic` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `eventType` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `eventTime` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Dane zdarzenia magazynu obiektów BLOB. |
| `dataVersion` | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| `metadataVersion` | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `source` | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| `subject` | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| `type` | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| `time` | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| `id` | ciąg | Unikatowy identyfikator zdarzenia. |
| `data` | object | Dane zdarzenia magazynu obiektów BLOB. |
| `specversion` | ciąg | Wersja specyfikacji schematu CloudEvents. |

---

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| `api` | ciąg | Operacja, która wyzwoliła zdarzenie. |
| `clientRequestId` | ciąg | Identyfikator żądania dostarczonego przez klienta dla operacji interfejsu API magazynu. Tego identyfikatora można użyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Client-Request-ID" w dziennikach i można go podać w żądaniach klientów przy użyciu nagłówka "x-MS-Client-Request-ID". Zobacz [format dziennika](/rest/api/storageservices/storage-analytics-log-format). |
| `requestId` | ciąg | Identyfikator żądania wygenerowanego przez usługę dla operacji interfejsu API magazynu. Może służyć do skorelowania dzienników diagnostycznych usługi Azure Storage przy użyciu pola "Request-ID-Header" w dziennikach i jest zwracana z inicjowania wywołania interfejsu API w nagłówku "x-MS-Request-ID". Zobacz [format dziennika](/rest/api/storageservices/storage-analytics-log-format). |
| `eTag` | ciąg | Wartość, której można użyć do warunkowego uruchamiania operacji. |
| `contentType` | ciąg | Typ zawartości określony dla obiektu BLOB. |
| `contentLength` | liczba całkowita | Rozmiar obiektu BLOB w bajtach. |
| `blobType` | ciąg | Typ obiektu BLOB. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| `contentOffset` | liczba | Przesunięcie w bajtach operacji zapisu wykonane w punkcie, w którym aplikacja wyzwalająca zdarzenie zakończyła zapisywanie do pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw.|
| `destinationUrl` |ciąg | Adres URL pliku, który będzie istniał po zakończeniu operacji. Na przykład jeśli nazwa pliku zostanie zmieniona, `destinationUrl` Właściwość zawiera adres URL nowej nazwy pliku. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw.|
| `sourceUrl` |ciąg | Adres URL pliku, który istnieje przed wykonaniem operacji. Na przykład jeśli nazwa pliku zostanie zmieniona, `sourceUrl` zawiera adres URL oryginalnej nazwy pliku przed operacją zmiany nazwy. <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw. |
| `url` | ciąg | Ścieżka do obiektu BLOB. <br>Jeśli klient używa interfejsu API REST usługi BLOB, ten adres URL ma następującą strukturę: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>Jeśli klient używa interfejsu API REST Data Lake Storage, ten adres URL ma następującą strukturę: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| `recursive` | ciąg | `True` Aby uruchomić operację we wszystkich katalogach podrzędnych; w przeciwnym razie `False` . <br>Pojawia się tylko dla zdarzeń wyzwalanych na kontach usługi BLOB Storage, które mają hierarchiczną przestrzeń nazw. |
| `sequencer` | ciąg | Nieprzezroczysta wartość ciągu reprezentująca logiczną sekwencję zdarzeń dla każdej konkretnej nazwy obiektu BLOB.  Użytkownicy mogą używać standardowego porównania ciągów, aby zrozumieć względną sekwencję dwóch zdarzeń dla tej samej nazwy obiektu BLOB. |
| `storageDiagnostics` | object | Dane diagnostyczne są czasami uwzględniane przez usługę Azure Storage. Gdy są obecne, powinny być ignorowane przez odbiorców zdarzeń. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego internetowego punktu końcowego za pomocą interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak za pomocą interfejsu wiersza polecenia platformy Azure wysyłać zdarzenia usługi BLOB Storage do elementu webhook. |
| [Szybki Start: kierowanie zdarzeń magazynu obiektów BLOB do niestandardowego punktu końcowego sieci Web przy użyciu programu PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać Azure PowerShell do wysyłania zdarzeń magazynu obiektów BLOB do elementu webhook. |
| [Szybki Start: Tworzenie i kierowanie zdarzeń magazynu obiektów BLOB za pomocą Azure Portal](blob-event-quickstart-portal.md) | Pokazuje, jak za pomocą portalu wysyłać zdarzenia magazynu obiektów BLOB do elementu webhook. |
| [Interfejs wiersza polecenia platformy Azure: subskrybowanie zdarzeń dla konta usługi BLOB Storage](./scripts/event-grid-cli-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta usługi BLOB Storage. Wysyła zdarzenie do elementu webhook. |
| [PowerShell: subskrybowanie zdarzeń dla konta usługi BLOB Storage](./scripts/event-grid-powershell-blob.md) | Przykładowy skrypt, który subskrybuje zdarzenie dla konta usługi BLOB Storage. Wysyła zdarzenie do elementu webhook. |
| [Szablon Menedżer zasobów: Tworzenie magazynu obiektów blob i subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. Wysyła zdarzenia do elementu webhook. |
| [Przegląd: reagowanie na zdarzenia usługi BLOB Storage](../storage/blobs/storage-blob-event-overview.md) | Omówienie integrowania usługi BLOB Storage z Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby zapoznać się z wprowadzeniem do pracy ze zdarzeniami usługi BLOB Storage, zobacz [Route BLOB Storage Events — interfejs wiersza polecenia platformy Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
