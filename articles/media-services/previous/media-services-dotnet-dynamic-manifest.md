---
title: Tworzenie filtrów za pomocą zestawu .NET SDK usługi Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł używać ich do przesyłania strumieniowego określonych sekcji strumienia. Media Services .NET SDK tworzy dynamiczne manifesty do osiągnięcia tego selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.custom: devx-track-csharp
ms.openlocfilehash: 862385d1117e0a1b5d2220c06885e0fd0ecfa4d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531631"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Tworzenie filtrów za pomocą zestawu SDK programu Media Services .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2,17, Media Services umożliwia zdefiniowanie filtrów dla zasobów. Te filtry są regułami po stronie serwera, które umożliwiają klientom wykonywanie takich czynności jak: odtwarzanie tylko sekcji filmu wideo (zamiast odtwarzania całego filmu wideo) lub określanie tylko podzestawu plików audio i wideo, które może obsłużyć urządzenie klienta (zamiast wszystkich odwzorów skojarzonych z zasobem). Takie filtrowanie zasobów jest realizowane za pośrednictwem **dynamicznego manifestu**s, które są tworzone na żądanie klienta w celu przesyłania strumieniowego wideo na podstawie określonych filtrów.

Aby uzyskać bardziej szczegółowe informacje dotyczące filtrów i manifestu dynamicznego, zobacz [Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md).

W tym artykule pokazano, jak używać zestawu SDK programu Media Services .NET do tworzenia, aktualizowania i usuwania filtrów. 

Uwaga w przypadku aktualizowania filtru może upłynąć do dwóch minut, zanim punkt końcowy przesyłania strumieniowego odświeża reguły. Jeśli zawartość została obsłużona przy użyciu tego filtru (i jest buforowana w serwerach proxy i w pamięci podręcznej usługi CDN), aktualizacja tego filtru może spowodować awarie odtwarzacza. Po zaktualizowaniu filtru zawsze Wyczyść pamięć podręczną. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Następujące typy są używane podczas tworzenia filtrów: 

* **IStreamingFilter**.  Ten typ jest oparty na następującym [filtrze](/rest/api/media/operations/filter) interfejsu API REST
* **IStreamingAssetFilter**. Ten typ jest oparty na następującym interfejsie API REST [AssetFilter](/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Ten typ jest oparty na następującym interfejsie API REST [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** i **IFilterTrackPropertyCondition**. Te typy są oparte na następujących interfejsach API REST [FilterTrackSelect i FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Tworzenie/aktualizowanie/odczytywanie/usuwanie filtrów globalnych
Poniższy kod pokazuje, jak używać programu .NET do tworzenia, aktualizowania, odczytywania i usuwania filtrów zasobów.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Tworzenie/aktualizowanie/odczytywanie/usuwanie filtrów zasobów
Poniższy kod pokazuje, jak używać programu .NET do tworzenia, aktualizowania, odczytywania i usuwania filtrów zasobów.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Kompiluj adresy URL przesyłania strumieniowego używające filtrów
Informacje o sposobach publikowania i dostarczania zasobów znajdują się w temacie [dostarczanie zawartości do klientów Przegląd](media-services-deliver-content-overview.md).

W poniższych przykładach pokazano, jak dodać filtry do adresów URL przesyłania strumieniowego.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md)
