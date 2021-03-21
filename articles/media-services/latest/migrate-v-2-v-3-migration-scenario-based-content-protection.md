---
title: Wskazówki dotyczące migracji ochrony zawartości
description: Ten artykuł zawiera wskazówki dotyczące scenariusza ochrony zawartości, które ułatwiają minimalną migrację z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940117"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji opartej na scenariuszu ochrony zawartości

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera wskazówki dotyczące scenariusza ochrony zawartości, które ułatwiają minimalną migrację z Azure Media Services V2 do wersji v3.

## <a name="protect-content-in-v3-api"></a>Ochrona zawartości w interfejsie API v3

Użyj obsługi funkcji [wielokluczowych](design-multi-drm-system-with-access-control.md) w nowym interfejsie API v3.

Zapoznaj się z pojęciami dotyczącymi ochrony zawartości, samouczkami i instrukcjami przedstawionymi poniżej.

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

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]