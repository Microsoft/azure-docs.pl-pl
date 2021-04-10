---
title: Wskazówki dotyczące migracji ochrony zawartości
description: Ten artykuł zawiera wskazówki dotyczące scenariusza ochrony zawartości, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 9141fb025cb2c7976f88d894768972b10ea3a3d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729409"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji opartej na scenariuszu ochrony zawartości

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera szczegółowe informacje i wskazówki dotyczące migracji przypadków użycia ochrony zawartości z interfejsu API v2 do nowego interfejsu API Azure Media Services v3.

## <a name="protect-content-in-v3-api"></a>Ochrona zawartości w interfejsie API v3

Użyj obsługi funkcji [wielokluczowych](design-multi-drm-system-with-access-control.md) w nowym interfejsie API v3.

Zapoznaj się z pojęciami dotyczącymi ochrony zawartości, samouczkami i instrukcjami przedstawionymi poniżej.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Widoczność zasobów w wersji 2, StreamingLocators i właściwości w interfejsie API v3 dla scenariuszy ochrony zawartości

W trakcie migracji do interfejsu API v3 należy uzyskać dostęp do niektórych właściwości lub kluczy zawartości z zasobów w wersji 2. Jedną z kluczowych różnic polega na tym, że interfejs v2 używa **AssetID** jako podstawowego klucza identyfikacji, a nowy interfejs API v3 używa nazwy jednostki jako podstawowego identyfikatora usługi Azure Resource Management.  Właściwość **Asset.Name** v2 nie jest zwykle używana jako unikatowy identyfikator, więc w przypadku migrowania do wersji 3 w polu element **zawartości. Description** zostaną wyświetlone nazwy elementów zawartości v2.

Na przykład jeśli wcześniej miało się element zawartości w wersji 2 o IDENTYFIKATORze **"NB: CID: UUID: 8cb39104-122c-496e-9ac5-7f9e2c2547b8"**, wówczas podczas wyświetlania listy starych zasobów w wersji 2 za pomocą interfejsu API v3 nazwa będzie teraz częścią identyfikatora GUID na końcu (w tym przypadku **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"**).

Można wysyłać zapytania dotyczące **StreamingLocators** skojarzonych z zasobami UTWORZONYMI w interfejsie API v2 przy użyciu nowej metody v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) w jednostce zasobów.  Odwołuje się również do wersji zestawu SDK klienta .NET [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Wyniki metody **ListStreamingLocators** zapewniają **nazwę** i **StreamingLocatorId** lokalizatora wraz z **StreamingPolicyName**.

Aby znaleźć **ContentKeys** używany w **StreamingLocators** na potrzeby ochrony zawartości, można wywołać metodę [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Wszystkie **zasoby** , które zostały utworzone i opublikowane przy użyciu interfejsu API v2, będą mieć zarówno [zasadę klucza zawartości](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) , jak i klucz zawartości zdefiniowany w interfejsie API v3, zamiast korzystać z domyślnych zasad kluczy zawartości w ramach [zasad przesyłania strumieniowego](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept).

Aby uzyskać więcej informacji na temat ochrony zawartości w interfejsie API v3, zapoznaj się z artykułem [Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services.](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Jak wyświetlić listę zasobów i ustawień ochrony zawartości w wersji 2 za pomocą interfejsu API v3

W interfejsie API v2 zwykle używasz **zasobów**, **StreamingLocators** i **ContentKeys** do ochrony zawartości przesyłanej strumieniowo.
W przypadku migrowania do interfejsu API V3 wszystkie zasoby interfejsu API v2, StreamingLocators i ContentKeys są automatycznie uwidaczniane w interfejsie API v3, a wszystkie dane na nich są dostępne do uzyskania dostępu.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Czy mogę zaktualizować właściwości wersji 2 przy użyciu interfejsu API v3?

Nie, nie można zaktualizować żadnych właściwości w jednostkach v2 za pośrednictwem interfejsu API v3, który został utworzony przy użyciu StreamingLocators, StreamingPolicies, zasad klucza zawartości i kluczy zawartości w wersji 2.
Jeśli trzeba zaktualizować, zmienić lub zmodyfikować zawartość przechowywaną w systemie v2, należy ją zaktualizować za pośrednictwem interfejsu API v2 lub utworzyć nowe jednostki interfejsu API V3 w celu przeprowadzenia migracji do przodu.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Jak mogę zmienić ContentKeyPolicy używany dla elementu zawartości w wersji 2, który jest opublikowany i zachować ten sam klucz zawartości?

W takiej sytuacji należy najpierw cofnąć publikację (usunąć wszystkie lokalizatory przesyłania strumieniowego) dla zasobu za pomocą zestawu v2 SDK (usunąć lokalizator, odłączyć zasady autoryzacji klucza zawartości, odłączyć zasady dostarczania zasobów, odłączyć klucz zawartości, usunąć klucz zawartości), a następnie utworzyć nowy **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** w wersji 3 przy użyciu [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) v3 i [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept).

Podczas tworzenia **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** należy określić konkretny identyfikator klucza zawartości i wartość klucza.

Należy zauważyć, że istnieje możliwość usunięcia lokalizatora v2 przy użyciu interfejsu API v3, ale nie spowoduje to usunięcia klucza zawartości ani użytej zasady klucza zawartości, jeśli zostały utworzone w interfejsie API v2.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Korzystanie z AMSE v2 i AMSE v3 obok siebie

Podczas migrowania zawartości z wersji 2 do V3 zaleca się zainstalowanie [Narzędzia eksplorator Azure Media Services w wersji 2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) wraz z [narzędziem Eksplorator Azure Media Services v3](https://github.com/Azure/Azure-Media-Services-Explorer) , aby pomóc porównać dane wyświetlane obok siebie dla zasobu, który jest tworzony i publikowany za pośrednictwem interfejsów API v2. Wszystkie właściwości powinny być widoczne, ale w nieco różnych lokalizacjach.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Pojęcia dotyczące ochrony zawartości, samouczki i przewodniki

### <a name="concepts"></a>Pojęcia

- [Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services](content-protection-overview.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)
- [Media Services v3 z szablonem licencji PlayReady](playready-license-template-overview.md)
- [Omówienie szablonu licencji Media Services v3 with Widevine](widevine-license-template-overview.md)
- [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
- [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Zasady kluczy zawartości](content-key-policy-concept.md)

### <a name="tutorials"></a>Samouczki

[Szybki Start: używanie portalu do szyfrowania zawartości](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Pobieranie klucza podpisywania z istniejących zasad](get-content-key-policy-dotnet-howto.md)
- [FairPlay przesyłania strumieniowego w trybie offline dla systemu iOS z Media Services v3](offline-fairplay-for-ios.md)
- [Widevine przesyłania strumieniowego w trybie offline dla systemu Android z Media Services v3](offline-widevine-for-android.md)
- [Tryb online PlayReady Streaming dla systemu Windows 10 z Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Narzędzia

- [Narzędzie Eksploratora Azure Media Services w wersji 3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [Narzędzie Eksploratora Azure Media Services w wersji 2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
