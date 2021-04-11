---
title: Przekazywanie plików na konto Media Services przy użyciu platformy .NET | Microsoft Docs
description: Dowiedz się, jak pobrać zawartość multimedialną do Media Services przez utworzenie i przekazanie zasobów przy użyciu platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013731"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Przekazywanie plików na konto usługi Media Services na platformie .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. Jednostka **zasobu** może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików).  Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego.

Pliki w elementach zawartości są nazywane **plikami elementów zawartości**. Wystąpienie **AssetFile** i rzeczywisty plik multimedialny są dwa odrębne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące pliku nośnika, natomiast plik multimedialny zawiera rzeczywistą zawartość multimedialną.

## <a name="considerations"></a>Zagadnienia do rozważenia

Obowiązują następujące zastrzeżenia:
 
 * Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresów URL dla zawartości przesyłania strumieniowego (na przykład http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters). Z tego powodu nie jest dozwolone kodowanie procentowo. Wartość właściwości **name** nie może zawierać żadnego z następujących [znaków:%-Encoding](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters), które są zastrzeżone.! * ' ();: @ &= + $,/?% # [] ". Ponadto dla rozszerzenia nazwy pliku może istnieć tylko jeden ".".
* Długość nazwy nie może przekraczać 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.
* Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) artykuł.

Podczas tworzenia zasobów można określić następujące opcje szyfrowania:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. W przypadku korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania ani przechowywania w magazynie.
  Jeśli planujesz dostarczanie plików MP4 przy użyciu pobierania progresywnego, Użyj tej opcji: 
* **CommonEncryption** — Użyj tej opcji, jeśli przesyłasz zawartość, która została już zaszyfrowana i chroniona za pomocą technologii DRM Common Encryption lub PlayReady (na przykład Smooth Streaming chronione za pomocą technologii PlayReady DRM).
* **EnvelopeEncrypted** — Użyj tej opcji, jeśli przekazujesz HLS szyfrowany przy użyciu algorytmu AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.
* **StorageEncrypted** — szyfruje zawartość Wyczyść lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazuje je do usługi Azure Storage, gdzie jest przechowywana w stanie spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
  
    Media Services zapewnia szyfrowanie magazynów na dysku dla zasobów, a nie za pośrednictwem programu, takiego jak Menedżer praw cyfrowych (DRM).
  
    Jeśli element zawartości jest szyfrowany w magazynie, musisz skonfigurować zasady dostarczania elementu zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

Jeśli określisz, że element zawartości ma być szyfrowany przy użyciu opcji **CommonEncrypted** lub opcji **EnvelopeEncrypted** , należy skojarzyć element zawartości z **ContentKey**. Aby uzyskać więcej informacji, zobacz [How to Create a ContentKey](media-services-dotnet-create-contentkey.md). 

Jeśli określisz, że element zawartości ma być szyfrowany przy użyciu opcji **StorageEncrypted** Media Services, zestaw SDK dla platformy .NET utworzy **StorageEncrypted** **ContentKey** dla elementu zawartości.

W tym artykule pokazano, jak używać programu Media Services .NET SDK, a Media Services także rozszerzeń zestawu .NET SDK do przekazywania plików do Media Services elementu zawartości.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Przekazywanie jednego pliku z zestawem SDK Media Services .NET

Poniższy kod używa platformy .NET do przekazania pojedynczego pliku. AccessPolicy i lokalizator są tworzone i niszczone przez funkcję upload. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Przekazywanie wielu plików przy użyciu zestawu Media Services .NET SDK
Poniższy kod przedstawia sposób tworzenia elementu zawartości i przekazywania wielu plików.

Kod wykonuje następujące czynności:

* Tworzy pusty element zawartości przy użyciu metody CreateEmptyAsset zdefiniowanej w poprzednim kroku.
* Tworzy wystąpienie **AccessPolicy** , które definiuje uprawnienia i czas trwania dostępu do elementu zawartości.
* Tworzy wystąpienie **lokalizatora** , które zapewnia dostęp do zasobu.
* Tworzy wystąpienie **BlobTransferClient** . Ten typ reprezentuje klienta, który działa w obiektach Blob platformy Azure. W tym przykładzie klient monitoruje postęp przekazywania. 
* Wylicza pliki w określonym katalogu i tworzy wystąpienie **AssetFile** dla każdego pliku.
* Przekazuje pliki do Media Services przy użyciu metody **UploadAsync** . 

> [!NOTE]
> Użyj metody UploadAsync, aby upewnić się, że wywołania nie blokują, a pliki są przekazywane równolegle.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Podczas przekazywania dużej liczby zasobów należy wziąć pod uwagę następujące kwestie:

* Utwórz nowy obiekt **CloudMediaContext** na wątek. Klasa **CloudMediaContext** nie jest bezpieczna wątkowo.
* Zwiększ NumberOfConcurrentTransfers z wartości domyślnej 2 do wyższej wartości, np. 5. Ustawienie tej właściwości ma wpływ na wszystkie wystąpienia elementu **CloudMediaContext**. 
* Pozostaw wartość domyślną ParallelTransferThreadCount.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Zbiorcze pozyskiwanie zasobów przy użyciu zestawu SDK Media Services .NET
Przekazywanie dużych plików zasobów może stanowić wąskie gardło podczas tworzenia zasobów. Pozyskiwanie zasobów zbiorczo lub zbiorczo polega na rozdzieleniu tworzenia zasobów przy użyciu procesu przekazywania. Aby użyć podejścia do pozyskiwania zbiorczego, należy utworzyć manifest (IngestManifest), który opisuje element zawartości i skojarzone z nim pliki. Następnie użyj wybranej metody przekazywania, aby przekazać skojarzone pliki do kontenera obiektów BLOB manifestu. Microsoft Azure Media Services czujki kontenera obiektów BLOB skojarzonego z manifestem. Po przekazaniu pliku do kontenera obiektów BLOB Microsoft Azure Media Services wykonuje tworzenie zasobów na podstawie konfiguracji zasobu w manifeście (IngestManifestAsset).

Aby utworzyć nowy IngestManifest, wywołaj metodę Create wyświetloną przez kolekcję IngestManifests na CloudMediaContext. Ta metoda tworzy nowy IngestManifest z podaną nazwą manifestu.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Utwórz zasoby, które są skojarzone z IngestManifest zbiorczym. Skonfiguruj odpowiednie opcje szyfrowania dla zasobu do pozyskiwania zbiorczego.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset kojarzy element zawartości z IngestManifest zbiorczo do pozyskiwania zbiorczego. Kojarzy ona również AssetFiles, które tworzą każdy element zawartości. Aby utworzyć IngestManifestAsset, użyj metody Create w kontekście serwera.

Poniższy przykład ilustruje dodanie dwóch nowych IngestManifestAssets, które kojarzą dwa elementy zawartości wcześniej utworzonych do manifestu pozyskiwania zbiorczego. Każdy IngestManifestAsset również kojarzy zbiór plików, które są przekazywane dla każdego elementu zawartości podczas zbiorczego pozyskiwania.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Można użyć dowolnej szybkiej aplikacji klienckiej, która umożliwia przekazywanie plików zasobów do identyfikatora URI kontenera magazynu obiektów BLOB dostarczonego przez właściwość **IIngestManifest. BlobStorageUriForUpload** IngestManifest. 

Poniższy kod pokazuje, jak używać zestawu .NET SDK do przekazywania plików zasobów.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

Kod przekazywania plików zasobów dla przykładu użytego w tym artykule przedstawiono w poniższym przykładzie kodu:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Możesz określić postęp zbiorczego pozyskiwania dla wszystkich zasobów skojarzonych z **IngestManifest** przez sondowanie właściwości Statistics **IngestManifest**. Aby można było zaktualizować informacje o postępie, należy użyć nowego **CloudMediaContext** przy każdym sondowaniu właściwości statystyk.

Poniższy przykład ilustruje sondowanie elementu IngestManifest przez jego **Identyfikator**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Przekazywanie plików przy użyciu rozszerzeń zestawu .NET SDK
Poniższy przykład przedstawia sposób przekazywania pojedynczego pliku przy użyciu rozszerzeń zestawu SDK platformy .NET. W takim przypadku używana jest metoda **CreateFromFile** , ale wersja asynchroniczna jest również dostępna (**CreateFromFileAsync**). Metoda **CreateFromFile** umożliwia określenie nazwy pliku, opcji szyfrowania i wywołania zwrotnego w celu raportowania postępu przekazywania pliku.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

Poniższy przykład wywołuje funkcję UploadFile i określa szyfrowanie magazynu jako opcję tworzenia elementu zawartości.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Następne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Następny krok
Teraz, gdy element zawartości został przekazany do Media Services, przejdź do artykułu [jak uzyskać informacje o procesorze multimediów][How to Get a Media Processor] .

[How to Get a Media Processor]: media-services-get-media-processor.md
