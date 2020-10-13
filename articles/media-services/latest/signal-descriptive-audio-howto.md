---
title: Śledzenie sygnałów dźwiękowych z Azure Media Services v3 | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby przekazać plik, zakodować wideo, dodać opisowe ścieżki audio i przesłać strumieniowo zawartość do Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0bd2002e8f6f233361dd85cc08cfbd97ca23ed60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291266"
---
# <a name="signal-descriptive-audio-tracks"></a>Śledzenie sygnałów dźwiękowych

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Możesz dodać ścieżkę narracji do wideo, aby ułatwić wizualnym klientom niesparowane nagranie wideo przez nasłuchiwanie narracji. W Media Services v3 można sygnalizować opisowe ścieżki audio przez adnotację ścieżki audio w pliku manifestu.

W tym artykule pokazano, jak zakodować wideo, przekazać plik MP4 tylko w formacie audio (koder-dekoder AAC) zawierający opisowy dźwięk do wyjściowego elementu zawartości i edytować plik. ISM, aby uwzględnić dźwięk opisowy.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto Media Services](./create-account-howto.md).
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.
- Przejrzyj [dynamiczne pakowanie](dynamic-packaging-overview.md).
- Zapoznaj się z samouczkiem [przekazywanie, kodowanie i przesyłanie strumieniowe wideo](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego 

Funkcja **CreateInputAsset** tworzy nowy [zasób](/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten **zasób** służy jako dane wejściowe zadania kodowania. W usłudze Media Services w wersji 3 danymi wejściowymi **zadania** może być **zasób** lub zawartość udostępniona dla konta usługi Media Services za pośrednictwem adresów URL protokołu HTTPS. 

Jeśli chcesz dowiedzieć się, jak kodować przy użyciu adresu URL HTTPS, zobacz [ten artykuł](job-input-from-http-how-to.md) .  

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Poniższa funkcja wykonuje następujące akcje:

* Tworzy element **zawartości** 
* Pobranie zapisywalnego [adresu URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) do [kontenera zasobu w magazynie](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Jeśli musisz przekazać nazwę utworzonego elementu zawartości wejściowej do innych metod, upewnij się, że używasz `Name` właściwości obiektu zasobu zwróconego z `CreateInputAssetAsync` , na przykład inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Utwórz zasób wyjściowy do przechowywania wyniku zadania kodowania

Wyjściowy element [zawartości](/rest/api/media/assets) przechowuje wynik zadania kodowania. Poniższa funkcja przedstawia sposób tworzenia wyjściowego elementu zawartości.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Jeśli musisz przekazać nazwę utworzonego elementu wyjściowego do innych metod, upewnij się, że używasz `Name` właściwości obiektu zasobu zwróconego z `CreateIOutputAssetAsync` , na przykład outputAsset.Name. 

W przypadku tego artykułu Przekaż `outputAsset.Name` wartość do `SubmitJobAsync` `UploadAudioIntoOutputAsset` funkcji i.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Tworzenie transformacji i zadania, które kodują przekazany plik

W przypadku kodowania lub przetwarzania zawartości w usłudze Media Services typowym wzorcem postępowania jest skonfigurowanie ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesłanie nowego zadania dla każdego nowego wideo powoduje zastosowanie tego przepisu do wszystkich wideo w bibliotece. Przepis w usłudze Media Services nazywa się **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](./transforms-jobs-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android. 

Poniższy przykład tworzy transformację (jeśli nie istnieje).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Następująca funkcja przesyła zadanie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Zalecamy używanie Event Grid, aby poczekać na ukończenie zadania.

Zadanie zazwyczaj przechodzi przez następujące stany: **zaplanowane**, **kolejkowane**, **przetwarzane**, **zakończone** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie anulowania, może być w stanie **Canceling (Anulowanie)**, a po zakończeniu tej operacji w stanie **Canceled (Anulowane)**.

Aby uzyskać więcej informacji, zobacz [Obsługa zdarzeń Event Grid](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Przekaż plik MP4 tylko do nagrania dźwiękowego

Przekaż dodatkowy plik MP4 tylko audio (koder-dekoder AAC) zawierający opisowy dźwięk do wyjściowego elementu zawartości.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Oto przykład wywołania `UpoadAudioIntoOutputAsset` funkcji:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Edytuj plik. ISM

Gdy zadanie kodowania zostanie wykonane, zasoby wyjściowe będą zawierać pliki wygenerowane przez zadanie kodowania. 

1. W Azure Portal przejdź do konta magazynu skojarzonego z Twoim kontem Media Services. 
1. Znajdź kontener z nazwą wyjściowego elementu zawartości. 
1. W kontenerze Znajdź plik. ISM i kliknij pozycję **Edytuj obiekt BLOB** (w oknie po prawej stronie). 
1. Edytuj plik. ISM, dodając informacje o przekazanym pliku MP4 tylko audio (koder-dekoder AAC) zawierający opisowy dźwięk, a następnie naciśnij przycisk **Zapisz** po zakończeniu.

    Aby sygnalizować opisowe ścieżki audio, należy dodać parametry "Accessibility" i "role" do pliku ISM. Ponosisz odpowiedzialność za poprawne ustawianie tych parametrów, aby sygnalizować ścieżkę audio jako opis. Na przykład Dodaj `<param name="accessibility" value="description" />` i `<param name="role" value="alternate" />` do pliku ISM dla określonej ścieżki audio, jak pokazano w poniższym przykładzie.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Uzyskaj lokalizator przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w zasobie wyjściowym pliku wideo, który można odtwarzać. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators), a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci. 

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia obiektu [StreamingLocator](/rest/api/media/streaminglocators) musisz określić żądany element **StreamingPolicyName**. W tym przykładzie zawartość będzie przesyłana strumieniowo w postaci jawnej (bez szyfrowania), dzięki czemu można użyć wstępnie zdefiniowanych zasad przesyłania strumieniowego bez szyfrowania (**PredefinedStreamingPolicy.ClearStreamingOnly**).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji zasad przesyłania strumieniowego. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

W poniższym kodzie przyjęto, że funkcja jest wywoływana za pomocą unikatowego elementu locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Przykład przedstawiony w tym temacie dotyczy przesyłania strumieniowego, lecz tego samego wywołania można użyć do utworzenia lokalizatora przesyłania strumieniowego na potrzeby dostarczania wideo przy użyciu pobierania progresywnego.

### <a name="get-streaming-urls"></a>Pobieranie adresów URL przesyłania strumieniowego

Teraz, po utworzeniu [lokalizatora przesyłania strumieniowego](/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego, jak pokazano w metodzie **GetStreamingURLs**. Aby utworzyć adres URL, musisz połączyć nazwę hosta [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego**. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

> [!NOTE]
> W przypadku tej metody jest potrzebny element locatorName, który został użyty podczas tworzenia **lokalizatora przesyłania strumieniowego** dla zasobu wyjściowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową i przejdź do [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. W polu **adres URL:** wklej jeden z wartości adresu URL przesyłania strumieniowego uzyskanych z aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="next-steps"></a>Następne kroki

[Analizowanie wideo](analyze-videos-tutorial-with-api.md)
