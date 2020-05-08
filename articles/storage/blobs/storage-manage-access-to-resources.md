---
title: Zarządzanie publicznym dostępem do odczytu dla kontenerów i obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak udostępniać kontenery i obiekty blob dostępowi Anonimowemu oraz jak korzystać z nich programistycznie.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7c524cb30b73c95329650924123b2ebc26a5d8a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856013"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob

Możesz włączyć anonimowy, publiczny dostęp do odczytu do kontenera i jego obiektów blob w usłudze Azure Blob Storage. W ten sposób możesz przyznać dostęp tylko do odczytu do tych zasobów bez udostępniania klucza konta i konieczności wymagania sygnatury dostępu współdzielonego.

Publiczny dostęp do odczytu jest najlepszy dla scenariuszy, w których niektóre obiekty blob mają zawsze być dostępne do anonimowego dostępu do odczytu. Aby uzyskać bardziej szczegółowy formant, możesz utworzyć sygnaturę dostępu współdzielonego. Sygnatury dostępu współdzielonego umożliwiają zapewnienie ograniczonego dostępu przy użyciu różnych uprawnień w określonym przedziale czasu. Aby uzyskać więcej informacji na temat tworzenia sygnatur dostępu współdzielonego, zobacz [Używanie sygnatur dostępu współdzielonego (SAS) w usłudze Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Przyznaj anonimowym użytkownikom uprawnienia do kontenerów i obiektów BLOB

Domyślnie kontener i wszystkie jego obiekty blob mogą być dostępne tylko dla użytkownika, który otrzymał odpowiednie uprawnienia. Aby przyznać anonimowym użytkownikom prawa dostępu do odczytu do kontenera i jego obiektów blob, można ustawić poziom dostępu publicznego do kontenera. Po przyznaniu praw dostępu publicznego do kontenera użytkownicy anonimowi mogą odczytywać obiekty blob w ramach dostępnego publicznie kontenera bez autoryzowania żądania.

Można skonfigurować kontener z następującymi uprawnieniami:

- **Brak publicznego dostępu do odczytu:** Do kontenera i jego obiektów BLOB można uzyskać dostęp tylko przez właściciela konta magazynu. Jest to wartość domyślna dla wszystkich nowych kontenerów.
- **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty blob w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne. Klienci anonimowi nie mogą wyliczyć obiektów BLOB w kontenerze.
- **Publiczny dostęp do odczytu dla kontenera i jego obiektów blob:** Wszystkie dane kontenera i obiektu BLOB mogą być odczytywane przez żądanie anonimowe. Klienci mogą wyliczać obiekty blob w kontenerze przez żądanie anonimowe, ale nie mogą wyliczać kontenerów na koncie magazynu.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ustaw poziom dostępu publicznego kontenera w Azure Portal

W [Azure Portal](https://portal.azure.com)można zaktualizować poziom dostępu publicznego dla jednego lub kilku kontenerów:

1. Przejdź do omówienia konta magazynu w Azure Portal.
1. W obszarze **BLOB Service** w bloku menu wybierz pozycję **obiekty blob**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj przycisku **Zmień poziom dostępu** , aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **poziom dostępu publicznego** , a następnie kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

Poniższy zrzut ekranu przedstawia sposób zmiany publicznego poziomu dostępu dla wybranych kontenerów.

![Zrzut ekranu przedstawiający sposób ustawiania poziomu dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Nie można zmienić publicznego poziomu dostępu dla pojedynczego obiektu BLOB. Poziom dostępu publicznego jest ustawiany tylko na poziomie kontenera.

### <a name="set-container-public-access-level-with-net"></a>Ustawianie publicznego poziomu dostępu kontenera za pomocą platformy .NET

# <a name="net-v12-sdk"></a>[\.Zestaw SDK NET V12](#tab/dotnet)

Aby ustawić uprawnienia dla kontenera, wywołaj metodę [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) . 

Poniższy przykład ustawia uprawnienia kontenera na pełny publiczny dostęp do odczytu. Aby ustawić uprawnienia publicznego dostępu do odczytu tylko dla obiektów blob, Przekaż pole **PublicAccessType. blob** do metody [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) . Aby usunąć wszystkie uprawnienia dla użytkowników anonimowych, użyj pola **BlobContainerPublicAccessType. None** .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# <a name="net-v11-sdk"></a>[\.Zestaw SDK NET v11](#tab/dotnet11)

Aby ustawić uprawnienia dla kontenera przy użyciu biblioteki klienta usługi Azure Storage dla platformy .NET, najpierw Pobierz istniejące uprawnienia kontenera, wywołując jedną z następujących metod:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Następnie ustaw właściwość **PublicAccess** obiektu [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) , który jest zwracany przez metodę **getpermissionss** .

Na koniec Wywołaj jedną z następujących metod, aby zaktualizować uprawnienia kontenera:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Poniższy przykład ustawia uprawnienia kontenera na pełny publiczny dostęp do odczytu. Aby ustawić uprawnienia publicznego dostępu do odczytu tylko dla obiektów blob, ustaw właściwość **PublicAccess** na **BlobContainerPublicAccessType. blob**. Aby usunąć wszystkie uprawnienia dla użytkowników anonimowych, należy ustawić właściwość na **BlobContainerPublicAccessType. off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## <a name="access-containers-and-blobs-anonymously"></a>Anonimowe dostęp do kontenerów i obiektów BLOB

Klient, który uzyskuje dostęp do kontenerów i obiektów BLOB anonimowo, może używać konstruktorów, które nie wymagają poświadczeń. W poniższych przykładach przedstawiono kilka różnych sposobów anonimowego odwoływania się do kontenerów i obiektów BLOB.

### <a name="create-an-anonymous-client-object"></a>Tworzenie anonimowego obiektu klienta

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

### <a name="reference-a-container-anonymously"></a>Anonimowe odwołanie do kontenera

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

### <a name="reference-a-blob-anonymously"></a>Anonimowe odwołanie do obiektu BLOB

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

- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md)
- [Interfejs API REST usługi Blob Service](/rest/api/storageservices/blob-service-rest-api)
