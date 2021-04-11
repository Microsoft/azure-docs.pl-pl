---
title: Wskazówki dotyczące migracji ochrony zawartości
description: Ten artykuł zawiera wskazówki dotyczące scenariusza ochrony zawartości, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490951"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji opartej na scenariuszu ochrony zawartości

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera szczegółowe informacje i wskazówki dotyczące migracji przypadków użycia ochrony zawartości z interfejsu API v2 do nowego interfejsu API Azure Media Services v3.

## <a name="protect-content-in-v3-api"></a>Ochrona zawartości w interfejsie API v3

Użyj obsługi funkcji [wielokluczowych](architecture-design-multi-drm-system.md) w nowym interfejsie API v3.

Zapoznaj się z pojęciami dotyczącymi ochrony zawartości, samouczkami i przewodnikami na końcu tego artykułu.

> [!NOTE]
> W dalszej części tego artykułu omówiono, jak przeprowadzić migrację ochrony zawartości w wersji 2 na wersję 3 przy użyciu platformy .NET.  Jeśli potrzebujesz instrukcji lub przykładowego kodu w innym języku lub metodzie, Utwórz problem z usługą GitHub dla tej strony.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>widoczność v3 zasobów, StreamingLocators i właściwości w wersji 2

W interfejsie API v2,, `Assets` `StreamingLocators` i `ContentKeys` były używane do ochrony zawartości przesyłania strumieniowego. Podczas migracji do interfejsu API v3, interfejsu API v2 `Assets` , `StreamingLocators` i `ContentKeys` są automatycznie uwidaczniane w interfejsie API v3, a wszystkie dane na nich są dostępne do uzyskania dostępu.

Nie można jednak *zaktualizować* żadnych właściwości na jednostkach w wersji 2 za pomocą interfejsu API v3, który został utworzony w wersji 2.

Jeśli musisz zaktualizować, zmienić lub zmodyfikować zawartość przechowywaną w systemie v2, zaktualizuj je za pomocą interfejsu API v2 lub Utwórz nowe jednostki interfejsu API v3, aby je zmigrować.

## <a name="asset-identifier-differences"></a>Różnice identyfikatorów zasobów

Aby przeprowadzić migrację, należy uzyskać dostęp do właściwości lub kluczy zawartości z zasobów w wersji 2.  Ważne jest, aby zrozumieć, że interfejs API v2 używa `AssetId` jako podstawowego klucza identyfikacji, ale nowy interfejs API v3 używa nazwy jednostki jako podstawowego identyfikatora *usługi Azure Resource Management* .  (Właściwość v2 `Asset.Name` nie jest używana jako unikatowy identyfikator). W przypadku interfejsu API v3 nazwa zasobu w wersji 2 jest teraz wyświetlana jako `Asset.Description` .

Na przykład jeśli poprzednio miało element zawartości w wersji 2 o IDENTYFIKATORze `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` , identyfikator znajduje się teraz na końcu identyfikatora GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Zobaczysz to podczas wyświetlania listy zasobów w wersji 2 za pomocą interfejsu API v3.

Wszystkie zasoby, które zostały utworzone i opublikowane przy użyciu interfejsu API v2, będą mieć zarówno `ContentKeyPolicy` a, jak i `ContentKey` w interfejsie API v3 zamiast domyślnego zasad klucza zawartości w programie `StreamingPolicy` .

Aby uzyskać więcej informacji, zobacz dokumentację [zasad dotyczących kluczy zawartości](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) i dokumentację [zasad przesyłania strumieniowego](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) .

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Używaj narzędzi Azure Media Services Explorer (AMSE) v2 i AMSE v3 obok siebie

Użyj [Narzędzia eksplorator Azure Media Services w wersji 2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) wraz z [narzędziem Eksplorator Azure Media Services v3](https://github.com/Azure/Azure-Media-Services-Explorer) , aby porównać dane obok siebie dla elementu zawartości utworzonego i opublikowanego za pomocą interfejsów API v2. Wszystkie właściwości powinny być widoczne, ale w różnych lokalizacjach.

## <a name="use-the-net-content-protection-migration-sample"></a>Korzystanie z przykładu migracji zawartości .NET Content Protection

Możesz znaleźć przykładowy kod, aby porównać różnice w identyfikatorach zasobów przy użyciu [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) w obszarze ContentProtection w przykładach kodu Media Services.

## <a name="list-the-streaming-locators"></a>Wyświetl listę lokalizatorów przesyłania strumieniowego

Można wykonać zapytanie dotyczące `StreamingLocators` skojarzonych z zasobami utworzonymi w interfejsie API v2 przy użyciu nowej metody v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) w jednostce zasobów.  Odwołuje się również do wersji zestawu SDK klienta .NET [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Wyniki `ListStreamingLocators` metody zapewniają do `Name` i `StreamingLocatorId` lokalizatora wraz z `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>Znajdowanie kluczy zawartości

Aby znaleźć `ContentKeys` używany z elementem `StreamingLocators` , można wywołać metodę [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Aby uzyskać więcej informacji na temat ochrony zawartości w interfejsie API v3, zapoznaj się z artykułem [Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Zmień ContentKeyPolicy v2, zachowując te same ContentKey

Najpierw należy cofnąć publikację (usunąć wszystkie lokalizatory przesyłania strumieniowego) dla zasobu za pomocą zestawu v2 SDK. Oto kroki tej procedury:

1. Usuń lokalizator.
1. Odłącz `ContentKeyAuthorizationPolicy` .
1. Odłącz `AssetDeliveryPolicy` .
1. Odłącz `ContentKey` .
1. Usuń `ContentKey` .
1. Utwórz nowy `StreamingLocator` w wersji 3 przy użyciu wersji 3 `StreamingPolicy` i `ContentKeyPolicy` , określając odpowiedni identyfikator klucza zawartości i wymaganą wartość klucza.

> [!NOTE]
> Istnieje możliwość usunięcia lokalizatora v2 przy użyciu interfejsu API v3, ale nie spowoduje to usunięcia klucza zawartości ani zasad klucza zawartości, jeśli zostały utworzone w interfejsie API v2.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Pojęcia dotyczące ochrony zawartości, samouczki i przewodniki

### <a name="concepts"></a>Pojęcia

- [Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services](drm-content-protection-concept.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](architecture-design-multi-drm-system.md)
- [Media Services v3 z szablonem licencji PlayReady](drm-playready-license-template-concept.md)
- [Omówienie szablonu licencji Media Services v3 with Widevine](drm-widevine-license-template-concept.md)
- [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](drm-fairplay-license-overview.md)
- [Zasady przesyłania strumieniowego](stream-streaming-policy-concept.md)
- [Zasady kluczy zawartości](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Samouczki

[Szybki Start: używanie portalu do szyfrowania zawartości](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Pobieranie klucza podpisywania z istniejących zasad](drm-get-content-key-policy-dotnet-how-to.md)
- [FairPlay przesyłania strumieniowego w trybie offline dla systemu iOS z Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Widevine przesyłania strumieniowego w trybie offline dla systemu Android z Media Services v3](drm-offline-widevine-for-android.md)
- [Tryb online PlayReady Streaming dla systemu Windows 10 z Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Samples

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Narzędzia

- [Narzędzie Eksploratora Azure Media Services w wersji 3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [Narzędzie Eksploratora Azure Media Services w wersji 2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
