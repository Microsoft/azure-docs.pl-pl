---
title: Lokalizatory przesyłania strumieniowego w Azure Media Services
description: Ten artykuł zawiera wyjaśnienie, czym są lokalizatory przesyłania strumieniowego i jak są używane przez Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773283"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby udostępnić klientom filmy wideo w wyjściowym elemencie zawartości w celu odtwarzania, trzeba utworzyć [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators) i skompilować adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego. Aby uzyskać przykład platformy .NET, zobacz [Pobieranie lokalizatora przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego** należy określić **nazwę** zasobu i nazwę zasad przesyłania **strumieniowego.** Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](stream-streaming-policy-concept.md)
* [Zasady kluczy zawartości](drm-content-key-policy-concept.md)

Możesz również określić czas rozpoczęcia i zakończenia w lokalizatorze przesyłania strumieniowego, co pozwoli użytkownikowi na odtwarzanie zawartości tylko między tymi godzinami (na przykład od 1.05.2019 do 5.05.2019).  

## <a name="considerations"></a>Zagadnienia do rozważenia

* **Lokalizatory przesyłania strumieniowego** nie są dostępne. 
* Właściwości **lokalizatorów przesyłania strumieniowego,** które mają typ Datetime, są zawsze w formacie UTC.
* Należy zaprojektować ograniczony zestaw zasad dla konta usługi Media Service i używać ich ponownie dla lokalizatorów przesyłania strumieniowego zawsze wtedy, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [Limity przydziału i limity](limits-quotas-constraints-reference.md).

## <a name="create-streaming-locators"></a>Tworzenie lokalizatorów przesyłania strumieniowego  

### <a name="not-encrypted"></a>Nieszyfrowane

Jeśli chcesz przesyłać strumieniowo plik w postaci niezaszyfrowanej (niezaszyfrowanej), ustaw wstępnie zdefiniowane zasady przesyłania strumieniowego bez szyfrowania: na wartość "Predefined_ClearStreamingOnly" (na platformie .NET można użyć wyliku PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Zaszyfrowane 

Jeśli musisz zaszyfrować zawartość za pomocą szyfrowania CENC, ustaw zasady na wartość "Predefined_MultiDrmCencStreaming". Szyfrowanie Widevine zostanie zastosowane do strumienia DASH, a szyfrowanie PlayReady do smooth. Klucz zostanie dostarczony do klienta odtwarzania na podstawie skonfigurowanych licencji DRM.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Jeśli chcesz również zaszyfrować strumień HLS przy użyciu systemu CBCS (FairPlay), użyj "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine to usługa dostarczana przez firmę Google Inc. podlegając warunkiom świadczenia usług i zasadom ochrony prywatności firmy Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Kojarzenie filtrów z lokalizatorami przesyłania strumieniowego

Zobacz [Filtry: kojarzenie z lokalizatorami przesyłania strumieniowego.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrowanie, kolejność i stronicowanie jednostek lokalizatora przesyłania strumieniowego

Zobacz [Filtrowanie, zamawianie i stronicowanie Media Services jednostek.](filter-order-page-entitites-how-to.md)

## <a name="list-streaming-locators-by-asset-name"></a>Lista lokalizatorów przesyłania strumieniowego według nazwy zasobu

Aby uzyskać lokalizatory przesyłania strumieniowego na podstawie skojarzonej nazwy zasobu, użyj następujących operacji:

|Język|Interfejs API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|Interfejs wiersza polecenia|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Zobacz też

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](stream-streaming-policy-concept.md)
* [Zasady kluczy zawartości](drm-content-key-policy-concept.md)
* [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe wideo za pomocą platformy .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć lokalizator przesyłania strumieniowego i tworzyć adresy URL](create-streaming-locator-build-url.md)
