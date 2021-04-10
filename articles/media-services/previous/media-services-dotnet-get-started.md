---
title: Wprowadzenie do dostarczania zawartości na żądanie przy użyciu platformy .NET | Microsoft Docs
description: Ten samouczek przedstawia kroki wdrażania aplikacji do dostarczania zawartości na żądanie w usługach Azure Media Services z użyciem platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e7f9fa33af43fb846255815d862d4cd3a07e99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013979"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu zestawu .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

W tym samouczku przedstawiono kolejne kroki wdrażania podstawowej usługi do dostarczania zawartości wideo na żądanie (VoD) za pomocą aplikacji Azure Media Services (AMS) przy użyciu zestawu Azure Media Services .NET SDK.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są niezbędne następujące elementy:

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* .NET Framework 4.0 lub nowszy.
* Programu Visual Studio.

W tym samouczku opisano następujące zadania:

1. Uruchamianie punktów końcowych przesyłania strumieniowego (przy użyciu witryny Azure Portal).
2. Tworzenie i konfigurowanie projektu programu Visual Studio.
3. Nawiązywanie połączenia z kontem usług Media Services.
2. Ładowanie pliku wideo.
3. Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.
4. Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby przesyłania strumieniowego i pobierania progresywnego.  
5. Odtwarzanie zawartości.

## <a name="overview"></a>Omówienie
Ten samouczek przedstawia kroki wdrażania aplikacji do dostarczania zawartości wideo na żądanie (VoD) przy użyciu zestawu SDK usług Azure Media Services (AMS) dla programu .NET.

Samouczek przedstawia podstawowy przepływ pracy usług Media Services oraz najczęściej występujące obiekty i zadania programowania wymagane w celu projektowania usług Media Services. Po zakończeniu samouczka będziesz umieć przesłać strumieniowo lub pobrać progresywnie przykładowy plik multimedialny, który został wcześniej przekazany, zakodowany oraz pobrany.

### <a name="ams-model"></a>Model AMS

Na poniższym obrazie przedstawiono niektóre z najczęściej używanych obiektów podczas tworzenia aplikacji VoD w modelu Media Services OData.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

[![Diagram przedstawiający niektóre z najczęściej używanych obiektów w modelu danych Azure Media Services obiektów na potrzeby tworzenia aplikacji wideo na żądanie.](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

Cały model możesz obejrzeć [tutaj](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Uruchamianie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal

Podczas pracy w usłudze Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie obrazu wideo za pośrednictwem przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać kodowaną zawartość plików MP4 z adaptacyjną szybkością transmisji bitów w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) w odpowiednim czasie bez konieczności przechowywania wersji wstępnie utworzonych pakietów poszczególnych formatów przesyłania strumieniowego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Aby uruchomić punkt końcowy przesyłania strumieniowego, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. W oknie Ustawienia kliknij pozycję Punkty końcowe przesyłania strumieniowego.
3. Kliknij domyślny punkt końcowy przesyłania strumieniowego.

    Zostanie wyświetlone okno SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO.

4. Kliknij ikonę Uruchom.
5. Kliknij przycisk Zapisz, aby zapisać zmiany.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 
2. Utwórz nowy folder (może się on znajdować w dowolnym miejscu na dysku lokalnym) i skopiuj plik mp4, który ma zostać zakodowany i przesłany strumieniowo lub pobrany progresywnie. W tym przykładzie użyto ścieżki „C:\VideoFiles”.

## <a name="connect-to-the-media-services-account"></a>Nawiązywanie połączenia z kontem usługi Media Services

Podczas korzystania z usługi Media Services z użyciem platformy .NET należy użyć klasy **CloudMediaContext** do większości zadań programowania usługi Media Services, takich jak nawiązywanie połączenia z kontem usługi Media Services oraz tworzenie, aktualizowanie, usuwanie i uzyskiwanie dostępu do następujących obiektów: elementów zawartości, plików elementów zawartości, zadań, zasad dostępu, lokalizatorów itp.

Zastąp domyślną klasę Program poniższym kodem: Kod przedstawia sposób odczytywania wartości połączenia z pliku App.config oraz sposób tworzenia obiektu **CloudMediaContext** na potrzeby połączenia z usługą Media Services. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z interfejsem API usług Media Services](media-services-use-aad-auth-to-access-ams-api.md).

Pamiętaj o zaktualizowaniu nazwy pliku i ścieżki do lokalizacji pliku multimedialnego.

Funkcja **Main** wywołuje metody, które będą zdefiniowane w dalszej części tej sekcji.

> [!NOTE]
> Błędy kompilacji będą się pojawiać do momentu dodania definicji dla wszystkich funkcji zdefiniowanych w dalszej części tego artykułu.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Tworzenie nowego elementu zawartości i przekazywanie pliku wideo

Za pomocą usługi Media Services można przekazać (lub pozyskać) pliki cyfrowe do elementu zawartości. Jednostka **zasobu** może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików).  Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego. Pliki w elementach zawartości są nazywane **plikami elementów zawartości**.

Zdefiniowana poniżej metoda **UploadFile** wywołuje metodę **CreateFromFile** (zdefiniowaną w rozszerzeniach zestawu SDK programu .NET). Metoda **CreateFromFile** tworzy nowy element zawartości, do którego jest przekazywany określony plik źródłowy.

Metoda **CreateFromFile** przyjmuje parametr **AssetCreationOptions**, który pozwala określić jedną z następujących opcji tworzenia elementów zawartości:

* **None** — szyfrowanie nie jest stosowane. Jest to wartość domyślna. Należy pamiętać, że w przypadku korzystania z tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.
  Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji.
* **StorageEncrypted** — ta opcja umożliwia lokalne szyfrowanie wyczyszczonej zawartości przy użyciu szyfrowania 256-bitowego Advanced Encryption Standard (AES), a następnie przekazanie jej do usługi Azure Storage, gdzie jest przechowywana w formie zaszyfrowanej. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
* **CommonEncryptionProtected** — za pomocą tej opcji można przekazać zawartość, która jest już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (np. pliki Smooth Streaming chronione za pomocą technologii PlayReady DRM).
* **EnvelopeEncryptionProtected** — ta opcja umożliwia przekazanie plików w formacie HLS zaszyfrowanych z użyciem standardu AES. Należy pamiętać, że pliki muszą być zakodowane i zaszyfrowane za pomocą narzędzia Transform Manager.

Metoda **CreateFromFile** pozwala także określić wywołanie zwrotne w celu raportowania postępu przekazywania pliku.

W poniższym przykładzie dla opcji elementu zawartości określono wartość **None**.

Dodaj następującą metodę do klasy Program.

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

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów
Po pozyskaniu elementów zawartości do usługi Media Services pliki multimedialne przed dostarczeniem do klientów mogą zostać zakodowane, poddane transmultipleksacji, oznaczone znakiem wodnym itp. Te działania są zaplanowane i uruchamiane w wielu wystąpieniach ról w tle, aby zapewnić wysoką wydajność oraz dostępność. Te działania są nazywane zadaniami, a każde zadanie składa się z niepodzielnych podzadań, które wykonują rzeczywistą pracę w pliku elementu zawartości.

Jak wspomniano wcześniej, podczas pracy z usługą Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie do klientów transmisji strumieniowej z adaptacyjną szybkością transmisji bitów. Usługa Media Services, korzystając z funkcji dynamicznego tworzenia pakietów, może utworzyć pakiet zestawu plików MP4 z adaptacyjną szybkością transmisji bitów w jednym z następujących formatów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG DASH.

Aby korzystać z dynamicznego tworzenia pakietów, zakoduj lub transkoduj plik (źródłowy) mezzanine do zestawu plików MP4 lub Smooth Streaming z adaptacyjną szybkością transmisji bitów.  

Poniższy kod ilustruje sposób przesyłania zadania kodowania. Zadanie zawiera jedno zadanie podrzędne, która określa transkodowanie pliku mezzanine do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów przy użyciu **standardu kodera multimediów**. Kod przesyła zadanie i oczekuje na ukończenie działania.

Po zakończeniu zadania użytkownik będzie mógł przesłać strumieniowo element zawartości lub pobrać progresywnie pliki MP4, które zostały utworzone w wyniku transkodowania.

Dodaj następującą metodę do klasy Program.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby pobierania stopniowego oraz przesyłania progresywnego

Aby przesłać strumieniowo lub pobrać element zawartości, należy go najpierw opublikować, tworząc lokalizator. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do strumieniowego przesyłania plików multimedialnych (na przykład w formacie MPEG DASH, HLS i Smooth Streaming) oraz lokalizatory sygnatury dostępu współdzielonego (SAS) używane do pobierania plików multimedialnych.

### <a name="some-details-about-url-formats"></a>Informacje na temat formatów adresów URL

Po utworzeniu lokalizatorów można tworzyć adresy URL umożliwiające przesyłanie strumieniowe lub pobieranie plików. Przykład w tym samouczku zwraca adresy URL, które można wkleić w odpowiednich przeglądarkach. W tej sekcji przedstawiono jedynie krótkie przykłady różnych formatów.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Adres URL dla protokołu MPEG DASH ma następujący format:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Adres URL dla protokołu HLS ma następujący format:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Adres URL dla protokołu Smooth Streaming ma następujący format:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Adres URL SAS używany do pobierania plików ma następujący format:

{nazwa kontenera obiektów blob}/{nazwa elementu zawartości}/{nazwa pliku}/{sygnatura dostępu współdzielonego}

Rozszerzenia zestawu .NET SDK usługi Media Services zawierają wygodne metody pomocnicze, które zwracają sformatowane adresy URL dla opublikowanego elementu zawartości.

W poniższym kodzie użyto rozszerzeń zestawu .NET SDK, aby utworzyć lokalizatory i pobrać adresy URL przesyłania strumieniowego oraz pobierania progresywnego. Kod przedstawia również sposób pobierania plików do folderu lokalnego.

Dodaj następującą metodę do klasy Program.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testowanie przez odtwarzanie zawartości

Po uruchomieniu programu zdefiniowanego w poprzedniej sekcji w oknie konsoli zostanie wyświetlony adres URL podobny do poniższego.

Adresy URL przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów:

Smooth Streaming

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

Adresy URL pobierania progresywnego (audio i wideo):

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


Aby przesyłać strumieniowo zawartość wideo, wklej adres URL w polu tekstowym adresu URL w odtwarzaczu [Azure Media Services Player](https://aka.ms/azuremediaplayer).

Aby przetestować pobieranie progresywne, wklej adres URL do przeglądarki (np. Internet Explorer, Chrome lub Safari).

Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Odtwarzanie zawartości w istniejących odtwarzaczach](media-services-playback-content-with-existing-players.md)
- [Osadzanie plików wideo adaptacyjnego przesyłania strumieniowego MPEG-DASH w aplikacji HTML5 z implementacją DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Pobieranie przykładu
Następujący przykład kodu zawiera kod utworzony w tym samouczku: [przykład](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
