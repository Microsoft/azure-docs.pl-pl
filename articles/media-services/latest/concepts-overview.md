---
title: Media Services Terminologia i koncepcje
description: Poznaj terminologię i koncepcje dotyczące Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 2a1c0c0685ab5a908a89f4a43073f9f773cd58c3
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632003"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services Terminologia i koncepcje

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten temat zawiera krótkie omówienie Azure Media Services terminologii i koncepcji. Artykuł zawiera również linki do artykułów z szczegółowym wyjaśnieniem pojęć związanych z Media Services v3.

Podstawowe koncepcje opisane w tych tematach należy przejrzeć przed rozpoczęciem opracowywania.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia Media Services v3

|Okres|Opis|
|---|---|
|Wydarzenie na żywo|**Wydarzenie na żywo** reprezentuje potok do pozyskiwania, transkodowania (opcjonalnie) i tworzenia pakietów na żywo strumieni wideo, audio i metadanych w czasie rzeczywistym.<br/><br/>W przypadku klientów migrowania z Media Services V2 API **zdarzenie na żywo** zastępuje jednostkę **kanału** w wersji 2. Aby uzyskać więcej informacji, zobacz [Migrowanie z wersji 2 do V3](migrate-v-2-v-3-migration-introduction.md).|
|Punkt końcowy przesyłania strumieniowego/pakowanie/Źródło|**Punkt końcowy przesyłania strumieniowego** reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienckiego. Używa jednego z typowych protokołów multimediów przesyłania strumieniowego (HLS lub KRESKi). Ponadto **punkt końcowy przesyłania strumieniowego** zapewnia dynamiczne (just-in-Time) szyfrowanie do wiodących w branży systemów zarządzania prawami cyfrowymi (protokołów DRM).<br/><br/>W branży przesyłania strumieniowego multimediów ta usługa jest często określana jako **Pakowarka** lub **Źródło**.  Inne typowe warunki w branży dla tej możliwości obejmują JITP ("just-in-Time-deJITEd") lub (just-in-Time-Encryption).

## <a name="media-services-v3-concepts"></a>Pojęcia dotyczące Media Services v3

|Pojęcia|Opis|Linki|
|---|---|---|
|Zasoby i przekazywanie zawartości|Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services i przekazać pliki cyfrowe do **zasobów**.|[Przekazywanie do chmury i magazynowanie w niej](storage-account-concept.md)<br/><br/>[Koncepcja zasobów](assets-concept.md)|
|Kodowanie zawartości|Po przekazaniu plików multimediów cyfrowych o wysokiej jakości do zasobów można je zakodować do formatów, które mogą być odtwarzane w różnych przeglądarkach i urządzeniach. <br/><br/>Aby kodować z Media Services v3, należy utworzyć **transformacje** i **zadania**.|[Transformacje i zadania](transforms-jobs-concept.md)<br/><br/>[Kodowanie przy użyciu Media Services](encoding-concept.md)|
|Analizowanie zawartości (Video Indexer)|Media Services v3 umożliwia wyodrębnienie szczegółowych informacji z plików wideo i audio przy użyciu ustawień wstępnych Media Services v3. Aby analizować zawartość przy użyciu ustawień wstępnych Media Services v3, należy utworzyć **transformacje** i **zadania**.<br/><br/>Jeśli potrzebujesz bardziej szczegółowych informacji, użyj [Video Indexer](../video-indexer/index.yml) bezpośrednio.|[Analizowanie plików wideo i audio](analyzing-video-audio-files-concept.md)|
|Tworzenie pakietów i dostarczanie|Po zapisaniu zawartości można korzystać z funkcji **dynamicznego tworzenia pakietów**. W Media Services **punkt końcowy przesyłania strumieniowego** to dynamiczna usługa tworzenia pakietów służąca do dostarczania zawartości multimedialnej do graczy klientów. Aby umożliwić odtwarzanie filmów wideo w danych wyjściowych, należy utworzyć **lokalizator przesyłania strumieniowego** , a następnie skompilować adresy URL przesyłania strumieniowego. <br/><br/>Podczas tworzenia **lokalizatora przesyłania strumieniowego** oprócz nazwy zasobu należy określić **zasady przesyłania strumieniowego**. **Zasady przesyłania strumieniowego** umożliwiają definiowanie protokołów przesyłania strumieniowego i opcji szyfrowania (jeśli istnieją) dla **lokalizatorów przesyłania strumieniowego**. Dynamiczne pakowanie służy do przesyłania strumieniowego zawartości na żywo lub na żądanie. <br/><br/>Media Services **manifestów dynamicznych** można używać do przesyłania strumieniowego tylko określonych elementów lub podklipów wideo.|[Dynamiczne tworzenie pakietów](dynamic-packaging-overview.md)<br/><br/>[Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)<br/><br/>[Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)<br/><br/>[Zasady przesyłania strumieniowego](streaming-policy-concept.md)<br/><br/>[Manifesty dynamiczne](filters-dynamic-manifest-overview.md)<br/><br/>[Filtry](filters-concept.md)|
|Ochrona zawartości|Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub/i jednego z trzech głównych systemów DRM: Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługi do dostarczania kluczy AES oraz licencji DRM (PlayReady, Widevine i FairPlay) do autoryzowanych klientów. <br/><br/>W przypadku określania opcji szyfrowania w strumieniu Utwórz **zasady dotyczące kluczy zawartości** i skojarz je z **lokalizatorem przesyłania strumieniowego**. **Zasady klucza zawartości** umożliwiają skonfigurowanie sposobu dostarczania klucza zawartości do klientów końcowych.<br/><br/> Spróbuj ponownie użyć zasad, gdy są potrzebne te same opcje.| [Zasady kluczy zawartości](content-key-policy-concept.md)<br/><br/>[Ochrona zawartości](content-protection-overview.md)|
|Transmisja strumieniowa na żywo|Media Services pozwala na dostarczanie na żywo wydarzeń klientom w chmurze platformy Azure. **Wydarzenia na żywo** odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Podczas tworzenia zdarzenia na **żywo** jest tworzony wejściowy punkt końcowy, za pomocą którego można wysyłać sygnały na żywo ze zdalnego kodera. Gdy strumień przepływa do **zdarzenia na żywo**, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element **zawartości**, **dane wyjściowe** i **lokalizator przesyłania strumieniowego**. Na **żywo dane wyjściowe** będą archiwizowane w **strumieniu i udostępniane** użytkownikom za pomocą **punktu końcowego przesyłania strumieniowego**. Wydarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów) lub *kodowanie na żywo* (lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów). |[Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)<br/><br/>[Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)|
|Monitorowanie za pomocą Event Grid|Aby wyświetlić postęp zadania, użyj **Event Grid**. Media Services również emituje typy zdarzeń na żywo. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. |[Obsługa zdarzeń usługi Event Grid](reacting-to-media-services-events.md)<br/><br/>[Schematy](media-services-event-schemas.md)|
|Monitorowanie za pomocą Azure Monitor|Monitoruj metryki i dzienniki diagnostyczne, które pomagają zrozumieć, jak aplikacje działają w Azure Monitor.|[Metryki i dzienniki diagnostyczne](media-services-metrics-diagnostic-logs.md)<br/><br/>[Schematy dzienników diagnostycznych](media-services-diagnostic-logs-schema.md)|
|Odtwarzacze klienckie|Azure Media Player umożliwia odtwarzanie zawartości multimedialnej przesyłanej przez Media Services na wielu różnych przeglądarkach i urządzeniach. Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzeń zaszyfrowanej multimediów (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. |[Omówienie usługi Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Kodowanie pliku zdalnego i strumieniowe przesyłanie wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumieniowe przesyłanie wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](protect-with-aes128.md)
* [Szyfrowanie dynamiczne za pomocą technologii wielowątkowości platformy .NET](protect-with-drm.md)