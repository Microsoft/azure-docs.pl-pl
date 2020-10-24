---
title: Azure Cosmos DB załączników
description: W tym artykule przedstawiono przegląd Azure Cosmos DB załączników.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 847ae3688fb713ddbd2dbf196ad1b89a6bb472a1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486637"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB załączników

Azure Cosmos DB załączniki to specjalne elementy, które zawierają odwołania do skojarzonych metadanych z zewnętrznym obiektem BLOB lub plikiem multimedialnym.

Azure Cosmos DB obsługuje dwa typy załączników:

* **Niezarządzane załączniki** to otoka otaczająca odwołanie do obiektu BLOB, który jest przechowywany w usłudze zewnętrznej (na przykład Azure Storage, OneDrive itp.). Takie podejście jest podobne do przechowywania właściwości URI w standardowym elemencie Azure Cosmos DB.
* **Zarządzane załączniki** to obiekty blob zarządzane i przechowywane wewnętrznie przez Azure Cosmos DB i udostępniane za pośrednictwem wygenerowanego przez system link.


> [!NOTE]
> Załącznik jest starszą funkcją. Dział pomocy technicznej jest objęty zakresem, aby oferować dalsze funkcje, jeśli ta funkcja jest już używana.
> 
> Zamiast korzystać z załączników zalecamy używanie Blob Storage platformy Azure jako usługi magazynu obiektów blob, która umożliwia przechowywanie danych obiektów BLOB. Można nadal przechowywać metadane związane z obiektami BLOB, a także linki do identyfikatorów URI odwołań, w Azure Cosmos DB jako właściwości elementu. Przechowywanie tych danych w Azure Cosmos DB umożliwia wykonywanie zapytań dotyczących metadanych i linków do obiektów BLOB przechowywanych w usłudze Azure Blob Storage.
> 
> Firma Microsoft dokłada starań, aby zapewnić co najmniej 36-miesięczne powiadomienie przed użyciem w pełni przestarzałych załączników — które będą ogłaszane w późniejszym czasie.

## <a name="known-limitations"></a>Znane ograniczenia

Zarządzane załączniki Azure Cosmos DB są różne od ich obsługi dla elementów standardowych — dla których oferuje nieograniczoną skalowalność, globalną dystrybucję i integrację z innymi usługami platformy Azure.

- Załączniki nie są obsługiwane we wszystkich wersjach zestawów SDK Azure Cosmos DB.
- Zarządzane załączniki są ograniczone do 2 GB przestrzeni dyskowej na konto bazy danych.
- Zarządzane załączniki nie są zgodne z dystrybucją globalną Azure Cosmos DB i nie są replikowane między regionami.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrowanie załączników do usługi Azure Blob Storage

Zalecamy Migrowanie Azure Cosmos DB załączników do usługi Azure Blob Storage, wykonując następujące czynności:

1. Skopiuj dane załączników ze źródłowego kontenera Azure Cosmos DB do docelowego kontenera Blob Storage platformy Azure.
2. Sprawdź poprawność przekazanych danych obiektu BLOB w docelowym kontenerze usługi Azure Blob Storage.
3. W razie potrzeby Dodaj odwołania do identyfikatorów URI do obiektów BLOB zawartych w usłudze Azure Blob Storage jako właściwości ciągu w ramach Azure Cosmos DB zestawu danych.
4. Refaktoryzacja kodu aplikacji, aby odczytywać i zapisywać obiekty blob z nowego kontenera Blob Storage platformy Azure.

Poniższy przykładowy kod dotnet przedstawia sposób kopiowania załączników z Azure Cosmos DB do magazynu obiektów blob platformy Azure w ramach przepływu migracji przy Azure Cosmos DB użyciu zestawu .NET SDK w wersji 2 i platformy Azure Blob Storage .NET SDK V12. Upewnij się, że zastąpisz `<placeholder values>` dla konta źródłowego Azure Cosmos DB i docelowy kontener usługi Azure Blob Storage.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki

- Rozpoczynanie pracy z [usługą Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Pobierz odwołania do korzystania z załączników za pomocą [zestawu .NET SDK w Azure Cosmos DB wersja 2](/dotnet/api/microsoft.azure.documents.attachment?preserve-view=true&view=azure-dotnet)
- Pobierz odwołania do używania załączników za pomocą [zestawu Java SDK w Azure Cosmos DB wersja 2](/java/api/com.microsoft.azure.documentdb.attachment?preserve-view=true&view=azure-java-stable)
- Pobierz odwołania do korzystania z załączników za pośrednictwem [interfejsu API REST Azure Cosmos DB](/rest/api/cosmos-db/attachments)