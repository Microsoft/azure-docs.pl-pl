---
title: Tworzenie filtrów za pomocą interfejsu wiersza polecenia Azure Media Services
description: W tym artykule pokazano, jak używać interfejsu wiersza polecenia do tworzenia filtrów z Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 54e99c645b93bfcb3aee4e81cef3b2b5d6589d95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211468"
---
# <a name="creating-filters-with-cli"></a>Tworzenie filtrów za pomocą interfejsu wiersza polecenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku dostarczania zawartości klientom (przesyłania strumieniowego na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż to, co zostało opisane w pliku manifestu domyślnego elementu zawartości. Azure Media Services umożliwia zdefiniowanie filtrów kont i filtrów zasobów dla zawartości.

Aby uzyskać szczegółowy opis tej funkcji i scenariuszy, w których są używane, zobacz [dynamiczne manifesty](filters-dynamic-manifest-overview.md) i [filtry](filters-concept.md).

W tym temacie pokazano, jak skonfigurować filtr dla zasobu wideo na żądanie i użyć interfejsu wiersza polecenia dla Media Services v3, aby utworzyć [filtry konta](/cli/azure/ams/account-filter) i [filtry zasobów](/cli/azure/ams/asset-filter).

> [!NOTE]
> Pamiętaj o przejrzeniu [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.

## <a name="define-a-filter"></a>Zdefiniuj filtr

W poniższym przykładzie zdefiniowano warunki wyboru śledzenia, które są dodawane do manifestu końcowego. Ten filtr zawiera wszystkie ścieżki audio, które są zgodne ze standardem EC-3 i wszystkie ścieżki wideo, które mają szybkość transmisji bitów w zakresie 0-1000000.

> [!TIP]
> Jeśli planujesz Definiowanie **filtrów** w spoczynku, zwróć uwagę na to, że musisz uwzględnić obiekt JSON otoki "Properties" (właściwości).  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Tworzenie filtrów konta

Następujące polecenie [AZ AMS Account-Filter](/cli/azure/ams/account-filter) tworzy filtr konta ze [zdefiniowanymi wcześniej](#define-a-filter)opcjami Śledź śledzenie.

Polecenie pozwala przekazać opcjonalny `--tracks` parametr zawierający kod JSON reprezentujący wybrane opcje śledzenia.  Użyj @ {File}, aby załadować plik JSON z pliku. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, określ pełną ścieżkę pliku:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zobacz też [przykłady JSON dla filtrów](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów

Poniższy polecenie [AZ AMS Asset-Filter](/cli/azure/ams/asset-filter) tworzy filtr zasobów z [zdefiniowanymi wcześniej](#define-a-filter)opcjami filtru śledzenia. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zobacz też [przykłady JSON dla filtrów](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Można określić listę filtrów zasobów lub kont, które mają zastosowanie do lokalizatora przesyłania strumieniowego. [Pakowarka dynamiczna (punkt końcowy przesyłania strumieniowego)](dynamic-packaging-overview.md) stosuje tę listę filtrów razem z tymi, które są określone przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-overview.md), który jest oparty na filtrach w adresach URL i filtrach określonych w lokalizatorze przesyłania strumieniowego. Zalecamy użycie tej funkcji, jeśli chcesz zastosować filtry, ale nie chcesz ujawniać nazw filtrów w adresie URL.

Poniższy kod interfejsu wiersza polecenia pokazuje, jak utworzyć lokalizator przesyłania strumieniowego i określić `filters` . Jest to opcjonalna właściwość, która pobiera listę nazw filtrów zasobów i/lub nazwy filtrów kont rozdzielonych spacjami.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Strumieniowe Używanie filtrów

Po zdefiniowaniu filtrów klienci mogą używać ich w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do protokołów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: Apple HTTP Live Streaming (HLS), MPEG-KRESKa i Smooth Streaming.

W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protokół|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Następny krok

[Strumieniowe przesyłanie wideo](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>Zobacz też

[Interfejs wiersza polecenia platformy Azure](/cli/azure/ams)
