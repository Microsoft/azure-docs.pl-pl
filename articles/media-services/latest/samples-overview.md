---
title: Przykłady usługi Media Services w wersji 3
description: Ten artykuł zawiera listę wszystkich przykładów dostępnych dla Media Services v3 zorganizowanych według metody i zestawu SDK.  Przykłady obejmują platformy .NET, Node.JS, Python i Java oraz załączają się do programu Poster.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 401033e210db0a9d65d138fd12c9d3365b311a62
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962156"
---
# <a name="media-services-v3-samples"></a>Przykłady usługi Media Services w wersji 3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten artykuł zawiera listę wszystkich przykładów dostępnych dla Media Services zorganizowanych według metody i zestawu SDK.  Przykłady obejmują platformy .NET, Node.JS, Python i Java oraz załączają się do programu Poster.

## <a name="rest-postman-collection"></a>Kolekcja ogłoszeń REST

Próbki [Poster programu REST](https://github.com/Azure-Samples/media-services-v3-rest-postman) obejmują środowisko i kolekcję programu post do zaimportowania do klienta programu Poster.

## <a name="samples-by-sdk"></a>Przykłady według zestawu SDK

Na każdej z tych kart znajdziesz opis i linki do przykładów, które mogą być wyszukiwane.

## <a name="net"></a>[.NET](#tab/net/)

| Folder | Opis |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Jak przesłać zadanie przy użyciu wbudowanego ustawienia wstępnego i danych wejściowych protokołu HTTP, Opublikuj element zawartości wyjściowej na potrzeby przesyłania strumieniowego i Pobierz wyniki weryfikacji.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Jak przesłać zadanie przy użyciu niestandardowego ustawienia wstępnego kodowania H. 264 oraz danych wejściowych adresu URL protokołu HTTP, Opublikuj element zawartości wyjściowej na potrzeby przesyłania strumieniowego i Pobierz wyniki weryfikacji.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Jak przesłać zadanie przy użyciu niestandardowego ustawienia wstępnego kodowania HEVC oraz danych wejściowych adresu URL protokołu HTTP, Opublikuj element zawartości wyjściowej dla przesyłania strumieniowego i Pobierz wyniki weryfikacji.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Jak przesłać zadanie przy użyciu JobInputSequence do łączenia dwóch lub więcej zasobów, które mogą być przycinane według czasu początkowego lub końcowego. Wynikający z tego zakodowany plik to pojedyncze wideo ze wszystkimi zasobami połączonymi ze sobą.  Próbka spowoduje również opublikowanie wyjściowego elementu zawartości dla przesyłania strumieniowego i pobranie wyników weryfikacji.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Jak przesłać zadanie przy użyciu niestandardowego ustawienia wstępnego z miniaturową ikoną i danymi wejściowymi protokołu HTTP, Opublikuj element zawartości wyjściowej na potrzeby przesyłania strumieniowego i Pobierz wyniki weryfikacji.|
| [Na żywo/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Jak utworzyć LiveEvent z pełnym archiwum do 25 godzin i filtrem dla elementu zawartości za pomocą okna DVR o 5 minut. Jak użyć filtru, aby utworzyć lokalizator do przesyłania strumieniowego.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Jak utworzyć transformację analizatora wideo, przekazać plik wideo do wejściowego elementu zawartości, przesłać zadanie z przekształceniem i pobrać wyniki weryfikacji.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Jak utworzyć transformację analizatora audio, przekazać plik multimedialny do wejściowego elementu zawartości, przesłać zadanie z przekształceniem i pobrać wyniki weryfikacji.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Jak utworzyć transformację przy użyciu wbudowanych ustawień wstępnych AdaptiveStreaming, przesłać zadanie, utworzyć ContentKeyPolicy przy użyciu klucza tajnego, skojarzyć ContentKeyPolicy z StreamingLocator, uzyskać token i wydrukować adres URL na potrzeby odtwarzania w Azure Media Player. Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu algorytmu AES-128, a Azure Media Player używa tokenu do odszyfrowania.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Jak utworzyć transformację przy użyciu wbudowanego ustawienia wstępnego AdaptiveStreaming, przesłać zadanie, utworzyć ContentKeyPolicy z konfiguracją Widevine przy użyciu klucza tajnego, skojarzyć ContentKeyPolicy z StreamingLocator, uzyskać token i wydrukować adres URL na potrzeby odtwarzania w odtwarzaczu Widevine. Gdy użytkownik zażąda zawartości chronionej przez Widevine, aplikacja odtwarzacza żąda licencji z usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawi licencję odtwarzaczowi. Licencja Widevine zawiera klucz odszyfrowujący, który może być używany przez odtwarzacz klienta w celu odszyfrowania i strumieniowego przesyłania zawartości.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Jak utworzyć transformację przy użyciu wbudowanego ustawienia wstępnego AdaptiveStreaming, przesłać zadanie, utworzyć ContentKeyPolicy z konfiguracją oprogramowania PlayReady przy użyciu klucza tajnego, skojarzyć ContentKeyPolicy z StreamingLocator, uzyskać token i wydrukować adres URL na potrzeby odtwarzania w Azure Media Player. Gdy użytkownik zażąda zawartości chronionej przy użyciu oprogramowania PlayReady, aplikacja odtwarzacza żąda licencji z usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawi licencję odtwarzaczowi. Licencja PlayReady zawiera klucz odszyfrowujący, który może być używany przez odtwarzacz klienta w celu odszyfrowania i strumieniowego przesyłania zawartości.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Jak dynamicznie szyfrować zawartość za pomocą oprogramowania PlayReady i Widevine DRM i odtwarzać zawartość bez żądania licencji od usługi licencjonowania. Pokazano, jak utworzyć transformację przy użyciu wbudowanego ustawienia wstępnego AdaptiveStreaming, przesłać zadanie, utworzyć ContentKeyPolicy z opcją Open ograniczenia oraz konfiguracją trwałą PlayReady/Widevine, skojarzyć ContentKeyPolicy z StreamingLocator i wydrukować adres URL na potrzeby odtwarzania.|
| [Przesyłanie strumieniowe/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Jak utworzyć transformację przy użyciu wbudowanego ustawienia wstępnego AdaptiveStreaming, przesłać zadanie, utworzyć filtr zasobów i filtr konta, skojarzyć filtry z kontenerami przesyłania strumieniowego i drukować adresy URL na potrzeby odtwarzania.|
| [Przesyłanie strumieniowe/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Jak utworzyć transformację przy użyciu wbudowanego ustawienia wstępnego AdaptiveStreaming, Prześlij zadanie, Opublikuj element zawartości wyjściowej dla HLS i przesyłania strumieniowego.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Wskazówki i najlepsze rozwiązania dotyczące systemu produkcyjnego przy użyciu kodowania na żądanie lub analizy. Czytelnicy powinni zacząć od artykułu pomocnika [wysokiej dostępności dzięki Media Services i VOD](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept). Dostępny jest osobny plik rozwiązania dla przykładu [HighAvailabilityEncodingStreaming](/HighAvailabilityEncodingStreaming/Readme.md) . |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Folder|Opis|
|---|---|
|[HelloWorld — ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Jak nawiązać połączenie i wyświetlić listę zasobów |
|[Na żywo](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Jak wykonać podstawowe przesyłanie strumieniowe na żywo. Upewnij **się, że** wszystkie zasoby są czyszczone i nie są już naliczane w portalu przy korzystaniu z usługi Live.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Jak przekazać plik lokalny lub kodowanie ze źródłowego adresu URL, jak używać zestawu SDK usługi Storage do pobierania zawartości oraz jak przesyłać strumieniowo do odtwarzacza. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Kodowanie i przesyłanie strumieniowe przy użyciu Widevine i PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Jak używać ustawień predefiniowanych analizatora wideo i audio do generowania metadanych i szczegółowych informacji z pliku wideo lub audio. |

## <a name="python"></a>[Python](#tab/python)

Obecnie istnieje jedno przykładowe środowisko Python, [podstawowe kodowanie przy użyciu języka Python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Folder|Opis|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Jak analizować dźwięk w pliku multimedialnym. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Jak dynamicznie szyfrować zawartość za pomocą algorytmu AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Jak dynamicznie szyfrować zawartość za pomocą technologii PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Jak dynamicznie szyfrować zawartość za pomocą technologii Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Jak dynamicznie szyfrować zawartość za pomocą FairPlay DRM i odtwarzać zawartość bez żądania licencji z usługi licencjonowania.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Jak dynamicznie szyfrować zawartość za pomocą oprogramowania PlayReady i Widevine DRM i odtwarzać zawartość bez żądania licencji od usługi licencjonowania.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Jak filtrować zawartość przy użyciu filtrów zasobów i kont.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Jak dynamicznie spakować Zawartość VOD do HLS/PAUZy na potrzeby przesyłania strumieniowego.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Jak utworzyć wydarzenie na żywo z pełnymi archiwummi do 25 godzin i filtrem dla zasobu za pomocą okna DVR o 5 minut i jak utworzyć lokalizator do przesyłania strumieniowego i użyć filtru.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Jak utworzyć wydarzenie na żywo z pełnymi archiwummi do 25 godzin i filtrem dla zasobu za pomocą okna DVR o 5 minut i jak utworzyć lokalizator do przesyłania strumieniowego i użyć filtru.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Jak utworzyć niestandardowe przekształcenie kodowania przy użyciu ustawień StandardEncoderPreset.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Jak przesłać zadanie przy użyciu wbudowanego ustawienia wstępnego i danych wejściowych protokołu HTTP, Opublikuj element zawartości wyjściowej na potrzeby przesyłania strumieniowego i Pobierz wyniki weryfikacji.|

---
