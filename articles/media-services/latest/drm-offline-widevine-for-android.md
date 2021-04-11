---
title: Widevine strumienia Android w trybie offline
description: W tym temacie przedstawiono sposób konfigurowania konta Azure Media Services v3 na potrzeby przesyłania strumieniowego w trybie offline Widevine chronionej zawartości.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 297520764272e2d6df54fe3a8ad734088163638d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069161"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Widevine przesyłania strumieniowego w trybie offline dla systemu Android z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Oprócz ochrony zawartości w ramach przesyłania strumieniowego online, subskrypcji zawartości multimedialnej i usług najmu można pobrać zawartość, która działa, gdy nie masz połączenia z Internetem. Może być konieczne pobranie zawartości na telefonie lub tablecie do odtwarzania w trybie samolotowym, gdy napływa odłączony od sieci. Dodatkowe scenariusze, w których warto pobrać zawartość:

- Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownik chce obserwować zawartość w trakcie podróży, konieczne jest pobranie plików w trybie offline.
- W niektórych krajach/regionach dostępność i/lub przepustowość Internetu są ograniczone. Użytkownicy mogą zdecydować się na pobranie zawartości, aby mogli ją obejrzeć w wysokiej rozdzielczości.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

W tym artykule omówiono sposób wdrażania odtwarzania w trybie offline dla zawartości PAUZy chronionej przez Widevine na urządzeniach z systemem Android. Usługa DRM w trybie offline umożliwia udostępnianie modeli subskrypcji, dzierżaw i zakupów dla zawartości, dzięki czemu klienci usług mogą łatwo z nich korzystać po rozłączeniu z Internetem.

W przypadku tworzenia aplikacji odtwarzacza dla urządzeń z systemem Android są dostępne trzy opcje:

> [!div class="checklist"]
> * Tworzenie odtwarzacza przy użyciu interfejsu API Java zestawu ExoPlayer SDK
> * Tworzenie odtwarzacza przy użyciu powiązania Xamarin zestawu ExoPlayer SDK
> * Tworzenie odtwarzacza przy użyciu rozszerzenia szyfrowane multimedia (EME) i rozszerzenia źródła multimediów (MSE) w przeglądarce Chrome Mobile Browser v62 lub nowszej

Artykuł odnosi się również do niektórych typowych pytań dotyczących przesyłania strumieniowego w trybie offline Widevine chronionej zawartości.

> [!NOTE]
> W przypadku funkcji DRM w trybie offline jest naliczana tylko jedna prośba o licencję podczas pobierania zawartości. Nie są naliczane opłaty za żadne błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem funkcji DRM w trybie offline dla Widevine na urządzeniach z systemem Android należy najpierw:

- Zapoznaj się z pojęciami wprowadzonymi do ochrony zawartości online przy użyciu funkcji DRM Widevine. Opisano to szczegółowo w następujących dokumentach/przykładach:
    - [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](architecture-design-multi-drm-system.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](drm-protect-with-drm-tutorial.md)
- Sklonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git .

    Aby dodać konfiguracje Widevine, należy zmodyfikować kod [zaszyfrowany za pomocą technologii DRM przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  
- Zapoznaj się z zestawem SDK usługi Google ExoPlayer dla systemu Android — zestawem SDK odtwarzacza wideo typu open source, który umożliwia obsługę odtwarzania w trybie offline Widevine funkcji DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Przewodnik dewelopera ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog dotyczący deweloperów EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w Azure Media Services

W metodzie [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) są obecne następujące niezbędne kroki:

1. Określ sposób autoryzowania dostarczania klucza zawartości w usłudze dostarczania licencji: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Skonfiguruj szablon licencji Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Utwórz ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Aby włączyć tryb **offline** dla licencji Widevine, należy skonfigurować [szablon licencji Widevine](drm-widevine-license-template-concept.md). W obiekcie **policy_overrides** ustaw właściwość **can_persist** na **true** (wartość domyślna to false), jak pokazano w [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

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

Deweloperzy powinni odwoływać się do [przewodnika dla deweloperów ExoPlayer](https://google.github.io/ExoPlayer/guide.html) oraz odpowiedniego [blogu dla deweloperów](https://medium.com/google-exoplayer) podczas opracowywania aplikacji. Firma Google nie wyłączyła w pełni udokumentowanej implementacji referencyjnej lub przykładowego kodu dla aplikacji ExoPlayer obsługującej Widevine w trybie offline, więc informacje są ograniczone do podręcznika i bloga deweloperów. 

### <a name="working-with-older-android-devices"></a>Praca ze starszymi urządzeniami z systemem Android

W przypadku niektórych starszych urządzeń z systemem Android należy ustawić wartości następujących **policy_overrides** właściwości (zdefiniowane w [szablonie licencji Widevine](drm-widevine-license-template-concept.md): **rental_duration_seconds**, **playback_duration_seconds** i **license_duration_seconds**. Alternatywnie można ustawić je na zero, co oznacza nieskończony/nieograniczony czas trwania.  

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

## <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji, zobacz [Widevine w kolekcji pytań](questions-collection.md#widevine-streaming-for-android).

Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.
