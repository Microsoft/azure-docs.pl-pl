---
title: Skonfiguruj konto do przesyłania strumieniowego Widevine chronionej zawartości w trybie offline — Azure
description: W tym temacie przedstawiono sposób konfigurowania konta Azure Media Services na potrzeby przesyłania strumieniowego w trybie offline Widevine chronionej zawartości.
services: media-services
keywords: ŁĄCZNIK, DRM, Widevine tryb offline, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: dc21523d158330d70adfbc33734abe3d49535804
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696299"
---
# <a name="offline-widevine-streaming-for-android"></a>Przesyłanie strumieniowe przy użyciu technologii Widevine w trybie offline dla systemu Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 3](../latest/offline-widevine-for-android.md)
> * [Wersja 2](offline-widevine-for-android.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Oprócz ochrony zawartości w ramach przesyłania strumieniowego online, subskrypcji zawartości multimedialnej i usług najmu można pobrać zawartość, która działa, gdy nie masz połączenia z Internetem. Może być konieczne pobranie zawartości na telefonie lub tablecie do odtwarzania w trybie samolotowym, gdy napływa odłączony od sieci. Dodatkowe scenariusze, w których warto pobrać zawartość:

- Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownik chce obserwować zawartość w trakcie podróży, konieczne jest pobranie plików w trybie offline.
- W niektórych krajach/regionach dostępność i/lub przepustowość Internetu są ograniczone. Użytkownicy mogą zdecydować się na pobranie zawartości, aby mogli ją obejrzeć w wysokiej rozdzielczości.

W tym artykule omówiono sposób wdrażania odtwarzania w trybie offline dla zawartości PAUZy chronionej przez Widevine na urządzeniach z systemem Android. Usługa DRM w trybie offline umożliwia udostępnianie modeli subskrypcji, dzierżaw i zakupów dla zawartości, dzięki czemu klienci usług mogą łatwo z nich korzystać po rozłączeniu z Internetem.

W przypadku tworzenia aplikacji odtwarzacza dla urządzeń z systemem Android są dostępne trzy opcje:

> [!div class="checklist"]
> * Tworzenie odtwarzacza przy użyciu interfejsu API Java zestawu ExoPlayer SDK
> * Tworzenie odtwarzacza przy użyciu powiązania Xamarin zestawu ExoPlayer SDK
> * Tworzenie odtwarzacza przy użyciu rozszerzenia szyfrowane multimedia (EME) i rozszerzenia źródła multimediów (MSE) w przeglądarce Chrome Mobile Browser v62 lub nowszej

Artykuł odnosi się również do niektórych typowych pytań dotyczących przesyłania strumieniowego w trybie offline Widevine chronionej zawartości.

## <a name="requirements"></a>Wymagania 

Przed wdrożeniem funkcji DRM w trybie offline dla Widevine na urządzeniach z systemem Android należy najpierw:

- Zapoznaj się z pojęciami wprowadzonymi do ochrony zawartości online przy użyciu funkcji DRM Widevine. Opisano to szczegółowo w następujących dokumentach/przykładach:
    - [Używanie Azure Media Services do dostarczania licencji DRM lub kluczy AES](media-services-deliver-keys-and-licenses.md)
    - [Szyfrowanie CENC przy użyciu technologii Multi-DRM i kontroli dostępu: odwołanie do projektowania i implementacji na platformie Azure i w usłudze Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Korzystanie z usługi PlayReady i/lub Widevine Dynamic Common Encryption z platformą .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Używanie Azure Media Services do dostarczania licencji PlayReady i/lub Widevine za pomocą platformy .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Zapoznaj się z zestawem SDK usługi Google ExoPlayer dla systemu Android — zestawem SDK odtwarzacza wideo typu open source, który umożliwia obsługę odtwarzania w trybie offline Widevine funkcji DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Przewodnik dewelopera ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog dotyczący deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguracja ochrony zawartości w Azure Media Services

W przypadku konfigurowania ochrony Widevine w ramach elementu zawartości w Media Services należy utworzyć ContentKeyAuthorizationPolicyOption, który określił następujące trzy rzeczy:

1. System DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction określania sposobu autoryzacji dostarczania klucza zawartości w usłudze dostarczania licencji (uwierzytelnianie Open lub token)
3. Szablon licencji DRM (Widevine)

Aby włączyć tryb **offline** dla licencji Widevine, należy skonfigurować [szablon licencji Widevine](media-services-widevine-license-template-overview.md). W obiekcie **policy_overrides** ustaw właściwość **can_persist** na **true** (wartość domyślna to false). 

Poniższy przykład kodu używa platformy .NET do włączenia trybu **offline** dla licencji Widevine. Kod jest oparty na [ Widevine Common Encryption dynamicznym w języku PlayReady i/lub na platformie .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) . 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurowanie odtwarzacza systemu Android na potrzeby odtwarzania w trybie offline

Najprostszym sposobem na tworzenie natywnej aplikacji odtwarzacza dla urządzeń z systemem Android jest użycie [zestawu SDK usługi Google ExoPlayer](https://github.com/google/ExoPlayer)z zestawem SDK odtwarzacza wideo typu open source. Program ExoPlayer obsługuje funkcje, które nie są obecnie obsługiwane przez natywny interfejs API MediaPlayer systemu Android, w tym protokół MPEG-KRESKa i protokoły dostarczania Microsoft Smooth Streaming.

ExoPlayer w wersji 2,6 lub nowszej zawiera wiele klas, które obsługują odtwarzanie w trybie offline Widevine. W szczególności Klasa OfflineLicenseHelper oferuje funkcje narzędziowe ułatwiające korzystanie z DefaultDrmSessionManager do pobierania, odnawiania i zwalniania licencji w trybie offline. Klasy udostępnione w folderze zestawu SDK "Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/" obsługują pobieranie zawartości wideo w trybie offline.

Poniższa lista klas ułatwia tryb offline w ExoPlayer SDK dla systemu Android:

- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. Java  
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. Java
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. Java 
- Biblioteka/rdzeń/src/Main/Java/com/Google/Android/exoplayer2/offline/narzędzia do pobierania. Java
- Biblioteka/kreska/src/Main/Java/com/Google/Android/exoplayer2/Source/pauza/offline/DashDownloader. Java 

Deweloperzy powinni odwoływać się do [przewodnika dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html) oraz odpowiedniego [blogu dla deweloperów](https://medium.com/google-exoplayer) podczas opracowywania aplikacji. Firma Google nie wyłączyła w pełni udokumentowanej implementacji referencyjnej lub przykładowego kodu dla aplikacji ExoPlayer obsługującej Widevine w trybie offline, dzięki czemu informacje są ograniczone do przewodnika deweloperów i bloga. 

### <a name="working-with-older-android-devices"></a>Praca ze starszymi urządzeniami z systemem Android

W przypadku niektórych starszych urządzeń z systemem Android należy ustawić wartości następujących **policy_overrides** właściwości (zdefiniowane w [szablonie licencji Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** i **license_duration_seconds**. Alternatywnie można ustawić je na zero, co oznacza nieskończony/nieograniczony czas trwania.  

Wartości muszą być ustawione tak, aby uniknąć błędów przepełnienia liczby całkowitej. Aby uzyskać więcej informacji o tym problemie, zobacz https://github.com/google/ExoPlayer/issues/3150 i https://github.com/google/ExoPlayer/issues/3112 . <br/>Jeśli nie ustawisz jawnie wartości, zostaną przypisane bardzo duże wartości dla  **PlaybackDurationRemaining** i **LicenseDurationRemaining** , (na przykład 9223372036854775807, która jest maksymalną wartością dodatnią dla 64-bitowej liczby całkowitej). W związku z tym licencja Widevine zostanie wyświetlona, a więc odszyfrowywanie nie zostanie wykonane. 

Ten problem nie występuje w przypadku systemu Android 5,0 Lizak lub nowszego, ponieważ system Android 5,0 to pierwsza wersja systemu Android, która została zaprojektowana w celu zapewnienia pełnej obsługi ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) i 64-bitowych platform, podczas gdy system Android 4,4 KitKat został pierwotnie zaprojektowany do obsługi platform architektury armv7 i 32-bit, jak w przypadku innych starszych wersji systemu Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Tworzenie aplikacji do odtwarzania systemu Android za pomocą platformy Xamarin

Powiązania platformy Xamarin dla ExoPlayer można znaleźć, korzystając z następujących linków:

- [Biblioteka powiązań platformy Xamarin dla biblioteki Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Powiązania Xamarin dla narzędzia NuGet ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zobacz też następujący wątek: [powiązanie Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikacje odtwarzacza Chrome dla systemu Android

Począwszy od wersji programu [Chrome dla systemu Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), jest obsługiwana licencja trwała w eme. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) jest teraz również obsługiwane w programie Chrome dla systemu Android. Dzięki temu można tworzyć aplikacje do odtwarzania w trybie offline w programie Chrome, jeśli użytkownicy końcowi mają tę wersję programu Chrome (lub wyższą). 

Ponadto firma Google wygenerowała progresywną aplikację sieci Web (PWA) i Źródło "open source": 

- [Kod źródłowy](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Hostowana wersja usługi Google](https://biograf-155113.appspot.com/ttt/episode-2/) (działa tylko w programie Chrome v 62 i nowszych na urządzeniach z systemem Android)

W przypadku uaktualnienia przeglądarki Mobile Chrome do v62 (lub nowszej) na telefonie z systemem Android i przetestowania powyższej przykładowej aplikacji można zobaczyć, że dane przesyłane strumieniowo online i odtwarzanie w trybie offline zostaną wykonane.

Powyższa aplikacja PWA Open Source została utworzona w Node.js. Jeśli chcesz hostować własną wersję na serwerze Ubuntu, pamiętaj o następujących typowych problemach, które mogą uniemożliwić odtwarzanie:

1. Problem CORS: Przykładowe wideo w przykładowej aplikacji jest hostowane w programie https://storage.googleapis.com/biograf-video-files/videos/ . Firma Google skonfigurował mechanizm CORS dla wszystkich próbek testowych hostowanych w przedziale magazynu Google Cloud. Są one obsługiwane z nagłówkami CORS, określając jawnie wpis CORS: `https://biograf-155113.appspot.com` (domena, w której jest hostowana przez firmę Google), uniemożliwiając dostęp przez inne lokacje. Jeśli spróbujesz, zobaczysz następujący błąd HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Problem z certyfikatem: począwszy od programu Chrome v 58, EME for Widevine wymaga protokołu HTTPS. W związku z tym należy hostować przykładową aplikację za pośrednictwem protokołu HTTPS z certyfikatem x509. Zwykły certyfikat testowy nie działa z powodu następujących wymagań: należy uzyskać certyfikat spełniający następujące wymagania minimalne:
    - Przeglądarki Chrome i Firefox wymagają SAN-Subject ustawienie alternatywnej nazwy w certyfikacie
    - Certyfikat musi mieć zaufany urząd certyfikacji, a certyfikat programistyczny z podpisem własnym nie działa
    - Certyfikat musi mieć nazwę POSPOLITą zgodną z nazwą DNS serwera sieci Web lub bramy

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="question"></a>Pytanie

Jak można dostarczać trwałe licencje (z włączoną obsługą trybu offline) dla niektórych klientów i nietrwałych licencji (wyłączone w trybie offline) dla innych użytkowników? Czy muszę duplikować zawartość i używać oddzielnego klucza zawartości?

### <a name="answer"></a>Odpowiedź
Nie trzeba duplikować zawartości. Możesz po prostu użyć pojedynczej kopii zawartości i jednego ContentKeyAuthorizationPolicyu, ale dwa oddzielne ContentKeyAuthorizationPolicyOption:

1. IContentKeyAuthorizationPolicyOption 1: używa trwałej licencji i ContentKeyAuthorizationPolicyRestriction 1, która zawiera takie jak license_type = "persistent"
2. IContentKeyAuthorizationPolicyOption 2: używa licencji nietrwałej i ContentKeyAuthorizationPolicyRestriction 2, która zawiera takie jak license_type = "nietrwałe"

W ten sposób, gdy żądanie licencji pochodzi z aplikacji klienckiej, z żądania licencji nie ma żadnych różnic. Jednak dla różnych użytkowników końcowych/urządzeń usługa STS powinna mieć logikę biznesową, aby wystawiać różne tokeny JWT zawierające różne oświadczenia (jeden z powyższych dwóch license_type). Wartość oświadczenia w tokenie JWT zostanie użyta do decyzji usługi licencjonowania, aby wystawić typ licencji: trwały lub nietrwały.

Oznacza to, że usługa Secure Token Service (STS) musi mieć informacje o logice biznesowej i kliencie/urządzeniu, aby dodać odpowiednią wartość żądania do tokenu.

### <a name="question"></a>Pytanie

W przypadku poziomów zabezpieczeń Widevine w dokumentacji doc dotyczącej [architektury DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) firmy Google definiujemy trzy różne poziomy zabezpieczeń. Jednak w [Azure Media Services dokumentacji dotyczącej szablonu licencji Widevine](./media-services-widevine-license-template-overview.md)są podane pięć różnych poziomów zabezpieczeń. Jaka jest relacja lub mapowanie między dwoma różnymi zestawami poziomów zabezpieczeń?

### <a name="answer"></a>Odpowiedź

W temacie [Omówienie architektury DRM Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)firmy Google definiujemy następujące trzy poziomy zabezpieczeń:

1.  Poziom zabezpieczeń 1: cała zawartość, Kryptografia i kontrola są wykonywane w ramach zaufanego środowiska wykonawczego (TEE). W niektórych modelach implementacji można wykonać przetwarzanie zabezpieczeń w różnych mikroukładach.
2.  Poziom zabezpieczeń 2: wykonuje kryptografię (ale nie przetwarzanie wideo) w TEE: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane przez oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.
3.  Poziom zabezpieczeń 3 nie ma TEE na urządzeniu. Odpowiednie środki mogą być podejmowane w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również obejmować sprzętowy aparat kryptograficzny, ale tylko podnosi wydajność, a nie zabezpieczenia.

W tym samym czasie, w [Azure Media Services dokumentacji dotyczącej szablonu licencji Widevine](./media-services-widevine-license-template-overview.md), właściwość security_level content_key_specs może mieć następujące pięć różnych wartości (wymagania dotyczące niezawodności klienta na potrzeby odtwarzania):

1.  Wymagana jest Kryptografia Whitebox oparta na oprogramowaniu.
2.  Wymagane jest Kryptografia oprogramowania i niesłonięty dekoder.
3.  Materiał klucza i operacje kryptograficzne muszą być wykonywane w ramach sprzętowej TEE kopii zapasowej.
4.  Kryptograficzne i dekodowanie zawartości należy wykonać w ramach sprzętowej TEE kopii zapasowej.
5.  Kryptografia, dekodowanie i wszystkie obsługujące nośniki (skompresowane i nieskompresowane) muszą być obsługiwane w ramach sprzętowej TEE kopii zapasowej.

Oba poziomy zabezpieczeń są definiowane przez firmę Google Widevine. Różnica jest na poziomie użycia: poziom architektury lub poziom interfejsu API. W interfejsie API Widevine są używane pięć poziomów zabezpieczeń. Obiekt content_key_specs, który zawiera security_level, jest deserializowany i przeszedł do usługi dostarczania globalnego Widevine przez Azure Media Services usługę licencji Widevine. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w interfejsie API Widevine**|
|---|---| 
| **Poziom zabezpieczeń 1**: cała zawartość, Kryptografia i kontrola są wykonywane w ramach zaufanego środowiska wykonawczego (tee). W niektórych modelach implementacji można wykonać przetwarzanie zabezpieczeń w różnych mikroukładach.|**security_level = 5**: Kryptografia, dekodowanie i wszystkie obsługujące nośniki (skompresowane i nieskompresowane) muszą być obsługiwane w ramach SPRZĘTowej TEEowej kopii zapasowej.<br/><br/>**security_level = 4**: Kryptografia i dekodowanie zawartości należy wykonać w ramach SPRZĘTowej TEEowej kopii zapasowej.|
**Poziom zabezpieczeń 2**: wykonuje kryptografię (ale nie przetwarzanie wideo) w tee: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane przez oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.| **security_level = 3**: materiał klucza i operacje kryptograficzne muszą zostać wykonane w ramach sprzętowej tee kopii zapasowej. |
| **Poziom zabezpieczeń 3**: nie ma tee na urządzeniu. Odpowiednie środki mogą być podejmowane w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również obejmować sprzętowy aparat kryptograficzny, ale tylko podnosi wydajność, a nie zabezpieczenia. | **security_level = 2**: wymagane jest Kryptografia oprogramowania i niesłonięty dekoder.<br/><br/>**security_level = 1**: wymagane jest Kryptografia Whitebox oparta na oprogramowaniu.|

### <a name="question"></a>Pytanie

Dlaczego pobieranie zawartości trwa tak długo?

### <a name="answer"></a>Odpowiedź

Istnieją dwa sposoby poprawy szybkości pobierania:

1.  Włącz usługę CDN, aby użytkownicy końcowi mogli korzystać z usługi CDN zamiast punktu końcowego źródła i przesyłania strumieniowego na potrzeby pobierania zawartości. W przypadku, gdy użytkownik trafi punkt końcowy przesyłania strumieniowego, każdy segment HLS lub fragment KRESKi jest dynamicznie spakowany i szyfrowany. Pomimo tego, że opóźnienie jest skalowane w milisekundach dla każdego segmentu/fragmentu, jeśli masz długi czas wideo, skumulowane opóźnienie może być duże, co powoduje dłuższe pobieranie.
2.  Zapewnianie użytkownikom końcowym opcji selektywnego pobierania warstw jakości wideo i ścieżek audio zamiast całej zawartości. W przypadku trybu offline nie ma miejsca do pobrania wszystkich warstw jakości. Istnieją dwa sposoby osiągnięcia tego celu:
    1.  Kontrolowane przez klienta: autowybieranie aplikacji odtwarzacza lub wybór warstwy jakości wideo i ścieżek audio do pobrania;
    2.  Kontrolowane przez usługę: jeden może używać funkcji manifestu dynamicznego w Azure Media Services, aby utworzyć filtr (globalny), który ogranicza HLS listy odtwarzania lub PAUZy do jednej warstwy jakości wideo i wybrane ścieżki audio. Następnie adres URL pobierania prezentowany użytkownikom końcowym będzie zawierać ten filtr.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="summary"></a>Podsumowanie

W tym artykule opisano sposób wdrażania odtwarzania w trybie offline dla zawartości PAUZy chronionej przez Widevine na urządzeniach z systemem Android.  W odpowiedzi na niektóre często zadawane pytania dotyczące przesyłania strumieniowego w trybie offline Widevine chronionej zawartości.
