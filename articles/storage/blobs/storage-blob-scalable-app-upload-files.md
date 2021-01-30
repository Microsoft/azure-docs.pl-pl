---
title: Równoległe przekazywanie dużych ilości danych losowych do usługi Azure Storage
description: Dowiedz się, jak używać biblioteki klienckiej usługi Azure Storage w celu równoległego przekazywania dużych ilości danych losowych do konta usługi Azure Storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed59903454725aca8ca5e73c2df6e4d6d6262ef6
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096275"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Równoległe przekazywanie dużych ilości danych losowych do usługi Azure Storage

Ten samouczek jest drugą częścią serii. Przedstawia sposób wdrażania aplikacji, która przekazuje dużą ilość danych losowych na konto usługi Azure Storage.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie parametrów połączenia
> * Kompilowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Microsoft Azure Blob Storage oferuje skalowalną usługę do przechowywania danych. Aby zagwarantować maksymalną wydajność aplikacji, warto wiedzieć, jak działa magazyn obiektów blob. Znajomość limitów obiektów blob platformy Azure jest ważne, aby dowiedzieć się więcej na temat tych limitów odwiedzania: [skalowalność i cele wydajności dla usługi BLOB Storage](../blobs/scalability-targets.md).

[Nazewnictwo partycji](../blobs/storage-performance-checklist.md#partitioning) jest innym potencjalnie ważnym czynnikiem podczas projektowania aplikacji o wysokiej wydajności przy użyciu obiektów BLOB. W przypadku bloków o rozmiarze większym niż lub równym 4 MiB są używane [blokowe obiekty blob o dużej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , a nazwy partycji nie wpłyną na wydajność. W przypadku bloków o rozmiarze mniejszym niż 4 MiB usługa Azure Storage korzysta z schematu partycjonowania opartego na zakresie, aby skalować i zrównoważyć obciążenie. Ta konfiguracja oznacza, że pliki z podobną konwencją nazewnictwa lub podobnymi prefiksami są umieszczane w tej samej partycji. Ta logika dotyczy także nazwy kontenera, do którego przekazywane są pliki. W tym samouczku używane są pliki, których nazwa zawiera identyfikator GUID, a ich zawartość jest generowana losowo. Są one przekazywane do pięciu różnych kontenerów o losowych nazwach.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka konieczne jest ukończenie poprzedniego samouczka dotyczącego usługi Storage: [Tworzenie maszyny wirtualnej i konta magazynu dla skalowalnej aplikacji][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Tworzenie zdalnej sesji na maszynie wirtualnej

Użyj następującego polecenia na maszynie lokalnej, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną. Zamień adres IP na publiczny adres IP Twojej maszyny wirtualnej. Po wyświetleniu monitu wprowadź poświadczenia użyte podczas tworzenia maszyny wirtualnej.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurowanie parametrów połączenia

W witrynie Azure Portal przejdź do swojego konta magazynu. Wybierz pozycję **Klucze dostępu** w obszarze **Ustawienia** konta magazynu. Skopiuj **parametry połączenia** z klucza podstawowego lub pomocniczego. Zaloguj się do maszyny wirtualnej utworzonej w poprzednim samouczku. Otwórz **wiersz polecenia** z uprawnieniami administratora i uruchom polecenie `setx` z przełącznikiem `/m`. To polecenie zapisuje zmienną środowiskową ustawień maszyny. Zmienna środowiskowa będzie dostępna po ponownym załadowaniu **wiersza polecenia**. Zastąp ciąg **\<storageConnectionString\>** w poniższym przykładzie:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Po zakończeniu operacji otwórz kolejny **wiersz polecenia**, przejdź do lokalizacji `D:\git\storage-dotnet-perf-scale-app` i wpisz polecenie `dotnet build`, aby skompilować aplikację.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Przejdź do adresu `D:\git\storage-dotnet-perf-scale-app`.

Wpisz polecenie `dotnet run`, aby uruchomić aplikację. Przy pierwszym uruchomieniu polecenia `dotnet` wypełnia ono lokalną pamięć podręczną pakietu w celu zwiększenia szybkości przywracania i umożliwienia dostępu w trybie offline. Wykonanie tego polecenia trwa maksymalnie minutę i odbywa się tylko raz.

```console
dotnet run
```

Aplikacja tworzy pięć losowo nazwanych kontenerów i rozpoczyna przekazywanie plików z katalogu przemieszczania do konta magazynu. Minimalna i Maksymalna liczba wątków jest ustawiona na 100, aby mieć pewność, że dozwolone są dużą liczbę współbieżnych połączeń.

`UploadFilesAsync`Metoda jest pokazana w poniższym przykładzie:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```
Oprócz skonfigurowania ustawień wątków oraz limitu połączeń, opcje [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) metody [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) są konfigurowane tak, aby używać funkcji równoległości i wyłączyć sprawdzanie poprawności wartości skrótu MD5. Pliki są przekazywane w blokach po 100 MB. Ta konfiguracja zapewnia lepszą wydajność, ale może być kosztowna, jeśli jest używana w sieci o niskiej wydajności — jeśli wystąpi błąd, konieczne będzie ponowne przekazanie całego bloku 100 MB.

|Właściwość|Wartość|Opis|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| To ustawienie dzieli obiekt blob na bloki na potrzeby przekazywania. W celu uzyskania najwyższej wydajności ta wartość powinna być osiem razy większa niż liczba rdzeni. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Ta właściwość wyłącza sprawdzanie skrótu MD5 przekazanej zawartości. Wyłączenie sprawdzania poprawności skrótu MD5 zwiększa szybkość transferu. Jednak poprawność i integralność przekazywanych plików nie jest wtedy sprawdzana.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| fałsz| Ta właściwość określa, czy skrót MD5 jest obliczany i przechowywany w pliku.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Czas wycofania: 2 sekundy, maksymalna liczba ponawianych prób: 10 |Określa zasady ponawiania żądań. Połączenia zakończone niepowodzeniem są ponawianie. W tym przykładzie skonfigurowano zasadę [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) z 2-sekundowych wycofaniem, a maksymalna liczba prób wynosi 10. To ustawienie jest ważne, gdy aplikacja zbliża się do elementów docelowych skalowalności dla usługi BLOB Storage. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../blobs/scalability-targets.md).  |

---

Poniższy przykład to skrócone dane wyjściowe aplikacji uruchomionej w systemie Windows.

```console
Created container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container 47646f1a-c498-40cd-9dae-840f46072180
Created container 38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Uploading D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Uploading D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Uploading D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Uploading D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180
Uploading D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Weryfikowanie połączeń

Podczas przekazywania plików możesz sprawdzić liczbę współbieżnych połączeń do konta magazynu. Otwórz okno konsoli i wpisz `netstat -a | find /c "blob:https"` . To polecenie pokazuje liczbę aktualnie otwartych połączeń. Jak widać w poniższym przykładzie, 800 połączeń było otwartych podczas przekazywania losowych plików do konta magazynu. Ta wartość zmienia się podczas przekazywania. Przekazywanie w równoległych blokach znacząco zmniejsza ilość czasu potrzebnego na przekazanie zawartości.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono równoległe przekazywanie dużej ilości danych losowych na konto magazynu, w tym następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie parametrów połączenia
> * Kompilowanie aplikacji
> * Uruchamianie aplikacji
> * Weryfikowanie liczby połączeń

Przejdź do trzeciej części serii, aby dowiedzieć się, jak pobierać dużą ilość danych z konta magazynu.

> [!div class="nextstepaction"]
> [Pobieranie dużych ilości danych losowych z usługi Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
