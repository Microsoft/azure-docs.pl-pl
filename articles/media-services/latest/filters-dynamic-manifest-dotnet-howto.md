---
title: Tworzenie filtrów za pomocą zestawu .NET SDK programu Azure Media Services v3
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł używać ich do przesyłania strumieniowego określonych sekcji strumienia. Zestaw SDK platformy .NET Media Services v3 tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 11c65498d5a31c2e2ee997bdaf18037b1f0f9060
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455216"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Tworzenie filtrów za pomocą zestawu SDK platformy Media Services .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku dostarczania zawartości klientom (przesyłania strumieniowego na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż to, co opisano w pliku manifestu domyślnego elementu zawartości. Azure Media Services umożliwia zdefiniowanie filtrów kont i filtrów zasobów dla zawartości.

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w których są używane, zobacz [dynamiczne manifesty](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak za Media Services pomocą zestawu SDK platformy .NET zdefiniować filtr dla zasobu wideo na żądanie i utworzyć [filtry konta](/dotnet/api/microsoft.azure.management.media.models.accountfilter) i [filtry zasobów](/dotnet/api/microsoft.azure.management.media.models.assetfilter). 

> [!NOTE]
> Pamiętaj o przejrzeniu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

- Przeglądanie [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).
- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services. 
- Uzyskaj informacje konieczne do [uzyskania dostępu do interfejsów API](./access-api-howto.md)
- Zapoznaj się z tematem [przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu Azure Media Services](stream-files-tutorial-with-api.md) , aby zobaczyć, jak [zacząć korzystać z zestawu SDK .NET](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>Zdefiniuj filtr  

W programie .NET można skonfigurować opcje śledzenia przy użyciu klas [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection) i [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition) . 

Poniższy kod definiuje filtr, który zawiera wszystkie ścieżki audio, które są zgodne ze standardem EC-3 i wszystkie ścieżki wideo, które mają szybkość transmisji bitów w zakresie 0-1000000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Tworzenie filtrów konta

Poniższy kod pokazuje, jak używać programu .NET do tworzenia filtru konta, który obejmuje wszystkie wybory śledzenia [zdefiniowane powyżej](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Poniższy kod pokazuje, jak używać programu .NET do tworzenia filtru zasobów, który obejmuje wszystkie wybory śledzenia [zdefiniowane powyżej](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Można określić listę filtrów zasobów lub kont, które mają zastosowanie do lokalizatora przesyłania strumieniowego. [Pakowarka dynamiczna (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) stosuje tę listę filtrów razem z tymi, które są określone przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresach URL i filtrach określonych w lokalizatorze przesyłania strumieniowego. Zalecamy użycie tej funkcji, jeśli chcesz zastosować filtry, ale nie chcesz ujawniać nazw filtrów w adresie URL.

Poniższy kod w języku C# pokazuje, jak utworzyć lokalizator przesyłania strumieniowego i określić `StreamingLocator.Filters` . Jest to opcjonalna właściwość, która przyjmuje `IList<string>` nazwy filtrów.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Strumieniowe Używanie filtrów

Po zdefiniowaniu filtrów klienci mogą używać ich w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do protokołów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: Apple HTTP Live Streaming (HLS), MPEG-KRESKa i Smooth Streaming.

W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protokół|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie wideo](stream-files-tutorial-with-api.md) 
