---
title: Publikowanie Azure Media Services zawartości przy użyciu platformy .NET | Microsoft Docs
description: Dowiedz się, jak utworzyć lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Przykłady kodu są zapisywane w języku C# i używają zestawu Media Services SDK dla platformy .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015798"
---
# <a name="publish-media-services-content-using-net"></a>Publikowanie Media Services zawartości przy użyciu platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Omówienie
Można przesyłać strumieniowo plik MP4 z adaptacyjną szybkością transmisji bitów, tworząc lokalizator przesyłania strumieniowego OnDemand i tworzący adres URL przesyłania strumieniowego. W temacie [kodowanie elementu zawartości](media-services-encode-asset.md) pokazano, jak kodować zestaw plików MP4 z adaptacyjną szybkością transmisji bitów. 

> [!NOTE]
> Jeśli zawartość jest zaszyfrowana, skonfiguruj zasady dostarczania zasobów (zgodnie z opisem w [tym](media-services-dotnet-configure-asset-delivery-policy.md) temacie) przed utworzeniem lokalizatora. 
> 
> 

Lokalizatora przesyłania strumieniowego OnDemand można także użyć do kompilowania adresów URL, które wskazują pliki MP4, które mogą być stopniowo pobierane.  

W tym temacie przedstawiono sposób tworzenia lokalizatora przesyłania strumieniowego OnDemand w celu opublikowania elementu zawartości i tworzenia gładkich adresów URL przesyłania strumieniowego MPEG i HLS. Przedstawiono w nim również adresy URL pobierania progresywnego. 

## <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego OnDemand
Aby utworzyć lokalizator przesyłania strumieniowego OnDemand i uzyskać adresy URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowana, Zdefiniuj zasady dostępu.
2. Utwórz lokalizator przesyłania strumieniowego OnDemand.
3. Jeśli planujesz przesyłanie strumieniowe, Pobierz plik manifestu przesyłania strumieniowego (. ISM) w elemencie zawartości. 
   
   Jeśli planujesz pobieranie progresywne, Pobierz nazwy plików MP4 w elemencie zawartości.  
4. Kompiluj adresy URL do pliku manifestu lub plików MP4. 


>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu. Na przykład zasady dla lokalizatorów, które mają być nadal wykonywane przez długi czas (zasady bez przekazywania). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="use-media-services-net-sdk"></a>Korzystanie z zestawu SDK Media Services platformy .NET
Kompiluj adresy URL przesyłania strumieniowego 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

Dane wyjściowe:

- Adres URL do manifestu dla przesyłania strumieniowego klienta przy użyciu protokołu Smooth Streaming: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- Adres URL manifestu dla przesyłania strumieniowego klienta przy użyciu protokołu HLS: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- Adres URL do manifestu dla przesyłania strumieniowego klienta przy użyciu protokołu MPEG PAUZ: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> Możesz również przesyłać strumieniowo zawartość za pośrednictwem połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Obecnie AMS nie obsługuje protokołu TLS z domenami niestandardowymi.
> 
> 

Kompiluj adresy URL pobierania progresywnego 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
Dane wyjściowe:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Użyj Media Services rozszerzeń zestawu SDK platformy .NET
Poniższy kod wywołuje metody rozszerzeń zestawu SDK platformy .NET, które tworzą lokalizator i generują adresy URL Smooth Streaming, HLS i MPEG na potrzeby adaptacyjnego przesyłania strumieniowego.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* [Pobierz zasoby](media-services-deliver-asset-download.md)
* [Konfigurowanie zasad dostarczania elementów zawartości](media-services-dotnet-configure-asset-delivery-policy.md)

