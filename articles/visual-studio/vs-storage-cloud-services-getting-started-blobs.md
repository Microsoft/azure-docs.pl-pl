---
title: Wprowadzenie do usługi BLOB Storage przy użyciu programu Visual Studio (Cloud Services)
description: Jak rozpocząć korzystanie z usługi Azure Blob Storage w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007045"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (projekty usług Cloud Services)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczynania pracy z usługą Azure Blob Storage po utworzeniu konta usługi Azure Storage lub przywoływaniu go za pomocą okna dialogowego **Dodawanie usług połączonych** programu Visual Studio w projekcie usług w chmurze programu Pokażemy Ci, jak uzyskać dostęp do kontenerów obiektów blob oraz jak wykonywać typowe zadania, takie jak przekazywanie, tworzenie list i pobieranie obiektów BLOB. Przykłady są zapisywane w języku C \# i używają [Microsoft Azure Storage biblioteki klienckiej dla platformy .NET](/previous-versions/azure/dn261237(v=azure.100)).

Azure Blob Storage to usługa do przechowywania dużych ilości danych bez struktury, do których można uzyskiwać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt BLOB może być dowolnym rozmiarem. Obiekty blob mogą być takie jak obrazy, pliki audio i wideo, dane pierwotne i pliki dokumentów.

Równie jak pliki aktywne w folderach, Magazyn obiektów BLOB na żywo w kontenerach. Po utworzeniu magazynu należy utworzyć co najmniej jeden kontener w magazynie. Na przykład w magazynie o nazwie "album" można utworzyć kontenery w magazynie o nazwie "images", aby przechowywać obrazy i inne o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów można przekazać do nich pojedyncze pliki obiektów BLOB.

* Aby uzyskać więcej informacji na temat programistycznego manipulowania obiektami BLOB, zobacz [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentację magazynu](https://azure.microsoft.com/documentation/services/storage/).
* Aby uzyskać ogólne informacje na temat usługi Azure Cloud Services, zobacz [dokumentację dotyczącą Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/).
* Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów BLOB w kodzie
Aby programowo uzyskać dostęp do obiektów BLOB w projektach usług w chmurze, należy dodać następujące elementy, jeśli jeszcze nie istnieją.

1. Dodaj następujące deklaracje przestrzeni nazw kodu na początku każdego pliku C#, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać informacje o parametrach połączenia magazynu i koncie magazynu z konfiguracji usługi platformy Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Pobierz obiekt **CloudBlobClient** , aby odwołać się do istniejącego kontenera na koncie magazynu.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Pobierz obiekt **CloudBlobContainer** , aby odwołać się do określonego kontenera obiektów BLOB.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Użyj całego kodu podanego w poprzedniej procedurze przed kodem pokazanym w poniższych sekcjach.
> 
> 

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie
> [!NOTE]
> Niektóre interfejsy API, które wykonują wywołania do usługi Azure Storage w ASP.NET, są asynchroniczne. Aby uzyskać więcej informacji [, zobacz Programowanie asynchroniczne z Async i await](/previous-versions/hh191443(v=vs.140)) . W poniższym przykładzie założono, że korzystasz z metod programowania asynchronicznego.
> 
> 

Aby utworzyć kontener na koncie magazynu, wystarczy dodać wywołanie **CreateIfNotExistsAsync** jak w poniższym kodzie:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Aby pliki w kontenerze były dostępne dla wszystkich użytkowników, można ustawić kontener jako publiczny przy użyciu następującego kodu.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Każda osoba w Internecie może wyświetlać obiekty blob w kontenerze publicznym, ale można je modyfikować lub usuwać tylko wtedy, gdy dysponujesz odpowiednim kluczem dostępu.

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Usługa Azure Storage obsługuje blokowe obiekty blob i stronicowe obiekty blob. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dowolny strumień danych, wywołując metodę **UploadFromStream** . Ta operacja tworzy obiekt blob, jeśli jeszcze nie istniał, lub go zastępuje, jeśli już istniał. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć metody **ListBlobs** kontenera, aby pobrać obiekty blob i/lub zawarte w nim katalogi. Aby uzyskać dostęp do bogatego zestawu właściwości i metod dla zwracanych **zwróconego ilistblobitem**, należy rzutować go na obiekt **CloudBlockBlob**, **CloudPageBlob** lub **CloudBlobDirectory** . Jeśli typ jest nieznany, można zastosować sprawdzanie typu, aby określić, do którego obiektu rzutować obiekt. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w kontenerze **photos**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Jak pokazano w poprzednim przykładzie kodu, usługa BLOB Service ma również koncepcję katalogów w kontenerach. Jest to tak, aby można było zorganizować obiekty blob w bardziej podobnej strukturze folderów. Rozważmy na przykład następujący zestaw blokowych obiektów blob w kontenerze o nazwie **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Gdy wywołasz **ListBlobs** w kontenerze (jak w poprzednim przykładzie), zwrócona kolekcja zawiera obiekty **CloudBlobDirectory** i **CloudBlockBlob** reprezentujące katalogi i obiektów BLOB znajdujących się na najwyższym poziomie. Oto wynikowe wyniki:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Opcjonalnie można ustawić parametr **UseFlatBlobListing** metody **ListBlobs** na wartość **true**. Powoduje to, że każdy obiekt BLOB jest zwracany jako **CloudBlockBlob**, niezależnie od katalogu. Oto wywołanie **ListBlobs**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

Oto wyniki:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Aby uzyskać więcej informacji, zobacz [CloudBlobContainer. ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Pobieranie obiektów blob
Aby pobrać obiekty blob, należy najpierw pobrać odwołanie do obiektu blob, a następnie wywołać metodę **DownloadToStream**. W poniższym przykładzie użyto metody **DownloadToStream**, aby przesłać zawartość obiektu blob do obiektu strumienia, który można następnie zachować w pliku lokalnym.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Można również użyć metody **DownloadToStream**, aby pobrać zawartość obiektu blob jako ciąg tekstowy.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Usuwać obiekty blob
Aby usunąć obiekt BLOB, należy najpierw pobrać odwołanie do obiektu BLOB, a następnie wywołać metodę **delete** .

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Asynchroniczne wyświetlanie obiektów blob na stronach
Jeśli chcesz wyświetlić dużą liczbę obiektów blob lub kontrolować liczbę wyników zwracanych przez jedną operację wyświetlania listy, możesz wyświetlić obiekty blob na stronach wyników. W tym przykładzie przedstawiono sposób asynchronicznego zwracania wyników na stronach, dzięki czemu wykonanie nie jest blokowane podczas oczekiwania na zwrócenie dużych zestawów wyników.

W tym przykładzie przedstawiono niezhierarchizowaną listę obiektów blob, ale można również uzyskać listę hierarchiczną, ustawiając parametr **useFlatBlobListing** metody **ListBlobsSegmentedAsync** na wartość **false**.

Ze względu na to, że przykładowa metoda wywołuje metodę asynchroniczną, musi być poprzedzona słowem kluczowym **async** i zwracać obiekt **Task**. Słowo kluczowe await określone dla metody **ListBlobsSegmentedAsync** wstrzymuje wykonywanie przykładowej metody do momentu ukończenia zadania wyświetlania listy.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]