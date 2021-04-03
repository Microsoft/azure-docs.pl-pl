---
title: Anonimowe uzyskiwanie dostępu do publicznych kontenerów i obiektów BLOB przy użyciu platformy .NET
titleSuffix: Azure Storage
description: Użyj biblioteki klienta usługi Azure Storage dla platformy .NET, aby anonimowo uzyskiwać dostęp do publicznych kontenerów i obiektów BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088820"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Anonimowe uzyskiwanie dostępu do publicznych kontenerów i obiektów BLOB przy użyciu platformy .NET

Usługa Azure Storage obsługuje opcjonalny publiczny dostęp do odczytu dla kontenerów i obiektów BLOB. Klienci mogą anonimowo uzyskiwać dostęp do publicznych kontenerów i obiektów BLOB przy użyciu bibliotek klienckich usługi Azure Storage, a także za pomocą innych narzędzi i narzędzi, które obsługują dostęp do danych do usługi Azure Storage.

W tym artykule pokazano, jak uzyskać dostęp do publicznego kontenera lub obiektu BLOB z platformy .NET. Aby uzyskać informacje o konfigurowaniu anonimowego dostępu do odczytu w kontenerze, zobacz [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](anonymous-read-access-configure.md). Aby uzyskać informacje na temat zapobiegania wszystkim anonimowym dostępowi do konta magazynu, zobacz [zapobieganie Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md).

Klient, który uzyskuje dostęp do kontenerów i obiektów BLOB anonimowo, może używać konstruktorów, które nie wymagają poświadczeń. W poniższych przykładach przedstawiono kilka różnych sposobów anonimowego odwoływania się do kontenerów i obiektów BLOB.

## <a name="create-an-anonymous-client-object"></a>Tworzenie anonimowego obiektu klienta

Nowy obiekt klienta usługi dla dostępu anonimowego można utworzyć, dostarczając punkt końcowy magazynu obiektów BLOB dla konta. Należy jednak znać nazwę kontenera na tym koncie, który jest dostępny dla dostępu anonimowego.

# <a name="net-v12-sdk"></a>[\.Zestaw SDK NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.Zestaw SDK NET v11](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Anonimowe odwołanie do kontenera

Jeśli masz adres URL do kontenera, który jest anonimowym dostępnym, możesz go użyć do bezpośredniego odwoływania się do kontenera.

# <a name="net-v12-sdk"></a>[\.Zestaw SDK NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.Zestaw SDK NET v11](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Anonimowe odwołanie do obiektu BLOB

Jeśli masz adres URL do obiektu BLOB, który jest dostępny dla dostępu anonimowego, możesz odwołać się do obiektu BLOB bezpośrednio przy użyciu tego adresu URL:

# <a name="net-v12-sdk"></a>[\.Zestaw SDK NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.Zestaw SDK NET v11](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](anonymous-read-access-configure.md)
- [Zapobiegaj Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md)
- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
