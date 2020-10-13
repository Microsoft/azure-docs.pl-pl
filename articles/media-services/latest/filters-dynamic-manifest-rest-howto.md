---
title: Tworzenie filtrów za pomocą interfejsu API REST Azure Media Services v3
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł używać ich do przesyłania strumieniowego określonych sekcji strumienia. Interfejs API REST Media Services v3 tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527330"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Tworzenie filtrów za pomocą interfejsu API REST Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku dostarczania zawartości klientom (przesyłania strumieniowego na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż to, co opisano w pliku manifestu domyślnego elementu zawartości. Azure Media Services umożliwia zdefiniowanie filtrów kont i filtrów zasobów dla zawartości. 

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w których są używane, zobacz [dynamiczne manifesty](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie przedstawiono sposób definiowania filtru dla zasobu wideo na żądanie i używania interfejsów API REST do tworzenia [filtrów kont](/rest/api/media/accountfilters) i [filtrów zasobów](/rest/api/media/assetfilters). 

> [!NOTE]
> Pamiętaj o przejrzeniu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać kroki opisane w tym temacie, należy:

- Przeglądanie [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).
- [Skonfiguruj funkcję publikowania dla Azure Media Services wywołań interfejsu API REST](media-rest-apis-with-postman.md).

    Pamiętaj, aby postępować zgodnie z ostatnim krokiem w temacie [pobieranie tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Zdefiniuj filtr  

Poniżej znajduje się przykład **treści żądania** , który definiuje warunki wyboru śledzenia, które są dodawane do manifestu. Ten filtr zawiera wszystkie ścieżki audio, które są zgodne ze standardem EC-3 i wszystkie ścieżki wideo, które mają szybkość transmisji bitów w zakresie 0-1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Tworzenie filtrów konta

W pobranej kolekcji Twojego wpisu wybierz pozycję **filtry kont** -> **Utwórz lub zaktualizuj filtr konta**.

Metoda **Put** żądania HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Wybierz kartę **treść** i wklej [wcześniej zdefiniowany](#define-a-filter)kod JSON.

Wybierz pozycję **Wyślij**. 

Filtr został utworzony.

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie](/rest/api/media/accountfilters/createorupdate). Zobacz też [przykłady JSON dla filtrów](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów  

W pobranej kolekcji "Media Services v3" Wybierz pozycję **zasoby** -> **Utwórz lub zaktualizuj filtr zasobów**.

Metoda **Put** żądania HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Wybierz kartę **treść** i wklej [wcześniej zdefiniowany](#define-a-filter)kod JSON.

Wybierz pozycję **Wyślij**. 

Filtr zasobów został utworzony.

Aby uzyskać szczegółowe informacje na temat tworzenia lub aktualizowania filtrów zasobów, zobacz [Tworzenie lub aktualizowanie](/rest/api/media/assetfilters/createorupdate). Zobacz też [przykłady JSON dla filtrów](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Można określić listę filtrów zasobów lub kont, które mają zastosowanie do lokalizatora przesyłania strumieniowego. [Pakowarka dynamiczna (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) stosuje tę listę filtrów razem z tymi, które są określone przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresach URL i filtrach określonych w lokalizatorze przesyłania strumieniowego. Zalecamy użycie tej funkcji, jeśli chcesz zastosować filtry, ale nie chcesz ujawniać nazw filtrów w adresie URL.

Aby utworzyć i skojarzyć filtry z lokalizatorem przesyłania strumieniowego przy użyciu usługi REST, użyj kontenerów [przesyłania strumieniowego — tworzenie](/rest/api/media/streaminglocators/create) interfejsu API i określanie `properties.filters` w [treści żądania](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Strumieniowe Używanie filtrów

Po zdefiniowaniu filtrów klienci mogą używać ich w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do protokołów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: Apple HTTP Live Streaming (HLS), MPEG-KRESKa i Smooth Streaming.

W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protokół|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Następne kroki

[Strumieniowe przesyłanie wideo](stream-files-tutorial-with-rest.md) 
