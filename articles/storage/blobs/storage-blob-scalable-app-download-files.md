---
title: Pobieranie dużych ilości danych losowych z usługi Azure Storage | Microsoft Docs
description: Dowiedz się, jak przy użyciu zestawu Azure SDK pobierać duże ilości danych losowych z konta usługi Azure Storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584977"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Pobieranie dużych ilości danych losowych z usługi Azure Storage

Ten samouczek jest trzecią częścią serii. Ten samouczek pokazuje, jak pobrać dużą ilość danych z usługi Azure Storage.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, należy wcześniej wykonać czynności opisane w poprzednim samouczku dotyczącym magazynu: [Równoległe przekazywanie dużych ilości danych losowych do usługi Azure Storage][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Tworzenie zdalnej sesji na maszynie wirtualnej

 Użyj następującego polecenia na maszynie lokalnej, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zamień adres IP na publiczny adres IP Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia maszyny wirtualnej.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Aktualizowanie aplikacji

W poprzednim samouczku pliki były tylko przekazywane na konto magazynu. Otwórz plik `D:\git\storage-dotnet-perf-scale-app\Program.cs` w edytorze tekstów. Zastąp metodę `Main` poniższym przykładem. W tym przykładzie zadanie przekazywania jest umieszczone w komentarzu, a poza komentarzem umieszczone są zadania pobierania i usuwania zawartości konta magazynu po ukończeniu.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Po zaktualizowaniu aplikacji należy ponownie skompilować aplikację. Otwórz pozycję `Command Prompt` i przejdź do lokalizacji `D:\git\storage-dotnet-perf-scale-app`. Ponownie skompiluj aplikację, uruchamiając polecenie `dotnet build`, jak pokazano na poniższym przykładzie:

```console
dotnet build
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po ponownym skompilowaniu aplikacji możesz uruchomić aplikację ze zaktualizowanym kodem. Jeśli nie zostało to jeszcze zrobione, otwórz pozycję `Command Prompt` i przejdź do lokalizacji `D:\git\storage-dotnet-perf-scale-app`.

Wpisz polecenie `dotnet run`, aby uruchomić aplikację.

```console
dotnet run
```

Zadanie `DownloadFilesAsync` jest przedstawione w poniższym przykładzie:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aplikacja odczytuje kontenery znajdujące się na koncie magazynu określonym przez parametr **storageconnectionstring**. Wykonuje iterację za pośrednictwem obiektów BLOB przy użyciu metody [Getblobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) i pobiera je na komputer lokalny przy użyciu metody [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aplikacja odczytuje kontenery znajdujące się na koncie magazynu określonym przez parametr **storageconnectionstring**. Wykonuje iterację przez obiekty blob 10 jednocześnie przy użyciu metody [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) w kontenerach i pobiera je na komputer lokalny przy użyciu metody [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) .

W poniższej tabeli przedstawiono [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) zdefiniowane dla każdego obiektu BLOB, który został pobrany.

|Właściwość|Wartość|Opis|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Ta właściwość wyłącza sprawdzanie skrótu MD5 przekazanej zawartości. Wyłączenie sprawdzania poprawności skrótu MD5 zwiększa szybkość transferu. Jednak poprawność i integralność przekazywanych plików nie jest wtedy sprawdzana. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| fałsz| Ta właściwość określa, czy skrót MD5 jest obliczany i przechowywany.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Weryfikowanie połączeń

Podczas pobierania plików możesz sprawdzić liczbę współbieżnych połączeń z kontem magazynu. Otwórz okno konsoli i wpisz `netstat -a | find /c "blob:https"` . To polecenie pokazuje liczbę aktualnie otwartych połączeń. Jak widać w poniższym przykładzie, podczas pobierania plików z konta magazynu otwarto ponad 280 połączeń.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Następne kroki

W trzeciej części serii omówiono pobieranie dużych ilości danych z konta magazynu, w tym:

> [!div class="checklist"]
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Przejdź do czwartej części serii, aby sprawdzić metryki przepływności i opóźnień w portalu.

> [!div class="nextstepaction"]
> [Sprawdzanie metryk przepływności i opóźnień w witrynie Azure Portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
