---
title: Hybrydowe projektowanie podsystemów DRM przy użyciu Azure Media Services | Microsoft Docs
description: W tym temacie omówiono hybrydowe projektowanie podsystemów DRM przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: 1bf01e98e418edfe8f15043d16a199b276b71020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266905"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Konstrukcja hybrydowa podsystemów DRM

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym temacie omówiono hybrydowe projektowanie podsystemów DRM przy użyciu Azure Media Services.

## <a name="overview"></a>Omówienie

Azure Media Services zapewnia pomoc techniczną dla następujących trzech systemów DRM:

* PlayReady
* Widevine (modularna)
* FairPlay

Obsługa technologii DRM obejmuje szyfrowanie DRM (szyfrowanie dynamiczne) i dostarczanie licencji, w tym Azure Media Player obsługujące wszystkie 3 protokołów DRM jako zestaw SDK odtwarzacza przeglądarki.

Aby uzyskać szczegółowe podejście do projektowania i implementacji podsystemu DRM/CENC, zobacz dokument zatytułowany [Cenc z obsługą technologii wielowątkowości i Access Control](media-services-cenc-with-multidrm-access-control.md).

Mimo że oferujemy pełną pomoc techniczną dla trzech systemów DRM, czasami klienci muszą korzystać z różnych części własnej infrastruktury/podsystemów, oprócz Azure Media Services do tworzenia hybrydowego podsystem DRM.

Poniżej przedstawiono niektóre często zadawane pytania dotyczące klientów:

* "Czy mogę używać własnych serwerów licencji DRM?" (W tym przypadku klienci zainwestowali w farmę serwerów licencji DRM z osadzoną logiką biznesową).
* "Czy można używać tylko dostarczania licencji DRM w Azure Media Services bez hostowania zawartości w usłudze AMS?".

## <a name="modularity-of-the-ams-drm-platform"></a>Modularność platformy DRM w usłudze AMS

W ramach kompleksowej platformy wideo w chmurze, Azure Media Services DRM ma projekt z elastycznością i modularniem. Można użyć Azure Media Services z następującymi różnymi kombinacjami opisanymi w poniższej tabeli (wyjaśnienie notacji używanej w tabeli poniżej). 

|**Źródło & hostingu zawartości**|**Szyfrowanie zawartości**|**Dostarczanie licencji DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Inne firmy|
|AMS|Inne firmy|AMS|
|AMS|Inne firmy|Inne firmy|
|Inne firmy|Inne firmy|AMS|

### <a name="content-hosting--origin"></a>Źródło & hostingu zawartości

* AMS: zasób wideo jest hostowany w usłudze AMS, a przesyłanie strumieniowe odbywa się za poorednictwem punktów końcowych przesyłania strumieniowego AMS (ale niekoniecznie dynamiczne pakowanie).
* Inna firma: wideo jest hostowane i dostarczane na platformie przesyłania strumieniowego innej firmy poza AMS.

### <a name="content-encryption"></a>Szyfrowanie zawartości

* AMS: szyfrowanie zawartości jest wykonywane dynamicznie/na żądanie przez dynamiczne szyfrowanie AMS.
* Inna firma: szyfrowanie zawartości jest wykonywane poza usługą AMS przy użyciu przepływu pracy przed przetwarzaniem.

### <a name="drm-license-delivery"></a>Dostarczanie licencji DRM

* AMS: Licencja DRM jest dostarczana przez usługę dostarczania licencji usługi AMS.
* Inna firma: Licencja DRM jest dostarczana przez serwer licencji DRM innej firmy poza AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Skonfiguruj na podstawie scenariusza hybrydowego

### <a name="content-key"></a>Klucz zawartości

Za pomocą konfiguracji klucza zawartości można kontrolować następujące atrybuty zarówno dynamicznego szyfrowania usługi AMS, jak i do dostarczania licencji AMS:

* Klucz zawartości używany do dynamicznego szyfrowania DRM.
* Zawartość licencji DRM do dostarczenia przez usługi dostarczania licencji: prawa, klucz zawartości i ograniczenia.
* Typ **ograniczeń zasad autoryzacji klucza zawartości**: otwarte, IP lub ograniczenia tokenu.
* W przypadku użycia typu **tokenu** **ograniczenia zasad autoryzacji klucza zawartości**należy spełnić **ograniczenie zasad autoryzacji klucza zawartości** przed wystawieniem licencji.

### <a name="asset-delivery-policy"></a>Zasady dostarczania zasobów

Za pomocą konfiguracji zasad dostarczania elementów zawartości można kontrolować następujące atrybuty używane przez Pakowarka dynamiczną AMS i dynamiczne szyfrowanie punktu końcowego przesyłania strumieniowego AMS:

* Protokół przesyłania strumieniowego i kombinacja szyfrowania DRM, takie jak KRESKa w obszarze CENC (PlayReady i Widevine), płynne przesyłanie strumieniowe w technologii PlayReady, HLS w obszarze Widevine lub PlayReady.
* Domyślne/osadzone adresy URL dostarczania licencji dla każdego z protokołów DRM.
* Określa, czy adresy URL pozyskiwania licencji (LA_URLs) na liście odtwarzania PAUZ lub HLS zawierają ciąg zapytania o IDENTYFIKATORze klucza (dziecko) dla Widevine i FairPlay.

## <a name="scenarios-and-samples"></a>Scenariusze i przykłady

Na podstawie wyjaśnień w poprzedniej sekcji, następujące pięć scenariuszy hybrydowych używa odpowiednich **Content key** - kombinacji konfiguracji**zasad dostarczania zasobów** klucza zawartości (przykłady wymienione w ostatniej kolumnie są zgodne z tabelą):

|**Źródło & hostingu zawartości**|**Szyfrowanie DRM**|**Dostarczanie licencji DRM**|**Konfiguruj klucz zawartości**|**Konfigurowanie zasad dostarczania elementów zawartości**|**Przykład**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Tak|Tak|Przykład 1|
|AMS|AMS|Inne firmy|Tak|Tak|Przykład 2|
|AMS|Inne firmy|AMS|Tak|Nie|Przykład 3|
|AMS|Inne firmy|Dział|Nie|Nie|Przykład 4|
|Inne firmy|Inne firmy|AMS|Tak|Nie|    

W tych przykładach ochrona PlayReady działa w przypadku obu KRESek i płynnego przesyłania strumieniowego. Adresy URL wideo poniżej to płynne adresy URL przesyłania strumieniowego. Aby uzyskać odpowiednie KRESKi URL, po prostu Dołącz wartość "(format = MPD-Time-CSF)". Możesz użyć [odtwarzacza testowego multimediów Azure](https://aka.ms/amtest) do testowania w przeglądarce. Umożliwia skonfigurowanie używanego protokołu przesyłania strumieniowego, w którym należy przeprowadzić techniczne. IE11 i Microsoft Edge w systemie Windows 10 obsługują oprogramowanie PlayReady w EME. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje o narzędziu testowym](./offline-playready-streaming-windows-10.md).

### <a name="sample-1"></a>Przykład 1

* Źródłowy adres URL (podstawowy): `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* LA_URL PlayReady (gładki &): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (PAUZa): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Przykład 2

* Źródłowy adres URL (podstawowy): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* LA_URL PlayReady (gładki &): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Przykład 3

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (gładki &): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Przykład 4

* Źródłowy adres URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (gładki &): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="summary"></a>Podsumowanie

Podsumowując, Azure Media Services składniki DRM są elastyczne, można ich używać w scenariuszu hybrydowym przez prawidłowe skonfigurowanie klucza zawartości i zasad dostarczania zasobów zgodnie z opisem w tym temacie.

## <a name="next-steps"></a>Następne kroki
Wyświetl ścieżki uczenia Media Servicesowego.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]