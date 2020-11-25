---
title: Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services v3
titleSuffix: Azure Media Services
description: Informacje o ochronie zawartości przy użyciu szyfrowania dynamicznego, protokołów przesyłania strumieniowego i typów szyfrowania w Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5d6530cf7b8d8611ff23a3517112cb0aa7442d6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971199"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Użyj Azure Media Services, aby pomóc w zabezpieczeniu nośnika od momentu, gdy komputer przejdzie przez cały proces w ramach magazynu, przetwarzania i dostarczania. Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługi do dostarczania kluczy AES oraz licencji DRM (PlayReady, Widevine i FairPlay) do autoryzowanych klientów. Jeśli zawartość jest zaszyfrowana przy użyciu klucza Clear AES i jest wysyłana za pośrednictwem protokołu HTTPS, nie jest to jasne, dopóki nie osiągnie klienta.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

W Media Services v3 klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego (patrz [ten przykład](protect-with-aes128.md)). Jeśli korzystasz z usługi Media Services Key Delivery, możesz Azure Media Services wygenerować klucz zawartości. Klucz zawartości powinien zostać wygenerowany samodzielnie, jeśli używasz własnej usługi dostarczania kluczy lub jeśli musisz obsługiwać scenariusz wysokiej dostępności, w którym musisz mieć ten sam klucz zawartości w dwóch centrach danych.

Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu klucza Wyczyść AES lub szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy Media Services lub usługi dostarczania kluczy określonej przez użytkownika. Aby zdecydować, czy użytkownik jest autoryzowany do pobrania klucza, usługa szacuje Zasady kluczy zawartości określone dla klucza.

Aby skonfigurować zasady autoryzacji i uwierzytelniania dla swoich licencji i kluczy, można użyć interfejsu API REST lub biblioteki klienta Media Services.

Na poniższej ilustracji przedstawiono przepływ pracy Media Services ochrony zawartości:

![Przepływ pracy dla Media Services Content Protection](./media/content-protection/content-protection.svg)
  
&#42; *szyfrowanie dynamiczne obsługuje algorytm AES-128 Clear Key, CBCS i Cenc. Aby uzyskać szczegółowe informacje, zobacz [Macierz obsługi](#streaming-protocols-and-encryption-types).*

W tym artykule wyjaśniono pojęcia i terminologia ułatwiająca zrozumienie ochrony zawartości za pomocą Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Główne składniki systemu ochrony zawartości

Aby pomyślnie zakończyć system ochrony zawartości, należy w pełni zrozumieć zakres nakładu pracy. Poniższe sekcje zawierają Omówienie trzech części, które należy wdrożyć.

> [!NOTE]
> Zdecydowanie zalecamy skoncentrowanie i pełne testowanie każdej części w poniższych sekcjach przed przejściem do następnej części. Aby przetestować system ochrony zawartości, użyj narzędzi określonych w sekcjach.

### <a name="media-services-code"></a>Kod Media Services
  
W [przykładzie technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) przedstawiono sposób implementacji systemu wieloskładnikowego z Media Services v3 przy użyciu platformy .NET. Przedstawiono w nim również, jak korzystać z usługi Media Services License/Delivery Key.
  
Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Aby zobaczyć, co ma być przydatne, zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

W przykładzie pokazano, jak:

1. Utwórz i skonfiguruj [zasady klucza zawartości](content-key-policy-concept.md).

   Tworzenie zasad klucza zawartości w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do zasobów) jest dostarczany do klientów końcowych:  

   * Zdefiniuj autoryzację dostarczania licencji. Określ logikę sprawdzania autoryzacji na podstawie oświadczeń w tokenie sieci Web JSON (JWT).
   * Skonfiguruj licencje [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)i/lub [FairPlay](fairplay-license-overview.md) . Szablony umożliwiają skonfigurowanie praw i uprawnień dla każdego z protokołów DRM.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md) skonfigurowany do przesyłania strumieniowego zaszyfrowanego zasobu.
  
   Lokalizator przesyłania strumieniowego musi być skojarzony z [zasadami przesyłania strumieniowego](streaming-policy-concept.md). W tym przykładzie ustawimy `StreamingLocator.StreamingPolicyName` zasady "Predefined_MultiDrmCencStreaming".

   Są stosowane szyfrowanie PlayReady i Widevine, a klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz również zaszyfrować strumień przy użyciu CBCS (FairPlay), użyj zasad "Predefined_MultiDrmStreaming".

   Lokalizator przesyłania strumieniowego jest również skojarzony z zdefiniowanymi zasadami klucza zawartości.

3. Utwórz token testowy.

   `GetTokenAsync`Metoda pokazuje, jak utworzyć token testowy.
4. Utwórz adres URL przesyłania strumieniowego.

   `GetDASHStreamingUrlAsync`Metoda pokazuje, jak utworzyć adres URL przesyłania strumieniowego. W takim przypadku adres URL strumieniuje zawartość KRESKi.

### <a name="player-with-an-aes-or-drm-client"></a>Odtwarzacz z klientem AES lub DRM

Aplikacja odtwarzacza wideo oparta na zestawie SDK odtwarzacza (natywnym lub opartym na przeglądarce) musi spełniać następujące wymagania:

* Zestaw SDK odtwarzacza obsługuje wymaganych klientów DRM.
* Zestaw SDK odtwarzacza obsługuje wymagane protokoły przesyłania strumieniowego: gładki, łącznik i/lub HTTP Live Streaming (HLS).
* Zestaw SDK odtwarzacza może obsłużyć przekazywanie tokenu JWT w żądaniu pozyskiwania licencji.

Odtwarzacz można utworzyć za pomocą [interfejsu API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) , aby określić technologię DRM, która ma być używana na różnych platformach DRM.

Do testowania zaszyfrowanej zawartości AES lub CENC (Widevine i/lub PlayReady) można użyć [Azure Media Player](https://aka.ms/azuremediaplayer). Upewnij się, że wybrano **Opcje zaawansowane** i Sprawdź opcje szyfrowania.

Jeśli chcesz przetestować zaszyfrowaną zawartość FairPlay, użyj [tego odtwarzacza testowego](https://aka.ms/amtest). Odtwarzacz obsługuje Widevine, PlayReady i FairPlay protokołów DRM oraz szyfrowanie za pomocą klucza AES-128.

Wybierz odpowiednią przeglądarkę, aby przetestować różne protokołów DRM:

* Chrome, Opera lub Firefox dla Widevine.
* Microsoft Edge lub Internet Explorer 11 dla oprogramowania PlayReady.
* Safari on macOS for FairPlay.

### <a name="security-token-service"></a>Usługa tokenu zabezpieczającego

Usługa tokenu zabezpieczającego (STS) wystawia token dostępu dla dostępu do zasobów zaplecza. Jako zasobu zaplecza można użyć usługi licencjonowania Azure Media Services/klucza dostawy. Usługa STS musi definiować następujące elementy:

* Wystawca i odbiorcy (lub zakres).
* Oświadczenia, które są zależne od wymagań firmy dotyczących ochrony zawartości.
* Symetryczne lub asymetryczne weryfikacje weryfikacji podpisu.
* Obsługa przerzucania kluczy (w razie potrzeby).

Możesz użyć [tego narzędzia STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) do przetestowania usługi STS. Obsługuje ona wszystkie trzy typy kluczy weryfikacyjnych: symetryczne, asymetryczne lub Azure Active Directory (Azure AD) z przerzucaniem kluczy.

## <a name="streaming-protocols-and-encryption-types"></a>Protokoły przesyłania strumieniowego i typy szyfrowania

Możesz użyć Media Services, aby zapewnić zaszyfrowaną zawartość dynamicznie przy użyciu klucza Clear AES lub szyfrowania DRM za pomocą oprogramowania PlayReady, Widevine lub FairPlay. Obecnie można szyfrować HLS, KRESKę MPEG i formaty Smooth Streaming. Każdy protokół obsługuje następujące metody szyfrowania.

### <a name="hls"></a>HLS

Protokół HLS obsługuje następujące formaty kontenerów i schematy szyfrowania:

|Format kontenera|Schemat szyfrowania|Przykład adresu URL|
|---|---|---|
|Wszystko|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 — TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 — TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (w tym HEVC/H. 265) są obsługiwane na następujących urządzeniach:

* System iOS 11 lub nowszy.
* iPhone 8 lub nowszy.
* macOS wysoka firma Sierra z procesorem Intel siódmej generacji.

### <a name="mpeg-dash"></a>MPEG-KRESKA

Protokół PAUZy MPEG obsługuje następujące formaty kontenerów i schematy szyfrowania:

|Format kontenera|Schemat szyfrowania|Przykłady adresów URL
|---|---|---|
|Wszystko|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF (FMP4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Protokół Smooth Streaming obsługuje następujące formaty kontenerów i schematy szyfrowania.

|Protokół|Format kontenera|Schemat szyfrowania|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1,1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Obsługa usługi PIFF 1,1 jest zapewniana jako zgodne rozwiązanie z poprzednimi wersjami dla telewizji inteligentnej (Samsung, LG), które implementuje wczesną wersję "Silverlight" Common Encryption. Zalecane jest używanie tylko formatu PIFF, gdy jest to konieczne do obsługi legacey Samsung lub LG inteligentnych telewizorów dostarczonych między 2009-2015, które obsługują wersję PIFF 1,1 szyfrowania PlayReady. 

### <a name="browsers"></a>Przeglądarki

Popularne przeglądarki obsługują następujących klientów DRM:

|Przeglądarka|Szyfrowanie|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Kontrolowanie dostępu do zawartości

Można kontrolować, kto ma dostęp do zawartości, konfigurując Zasady kluczy zawartości. Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Klient (odtwarzacz) musi spełnić zasady, aby można było dostarczyć klucz do klienta programu. Zasady klucza zawartości mogą mieć ograniczenia dotyczące *otwartych* lub *tokenów* .

Zasady klucza zawartości z ograniczeniami (Open) mogą być używane do wystawiania licencji osobom bez autoryzacji. Na przykład, jeśli przychód jest oparty na usłudze AD, a nie na podstawie subskrypcji.  

Przy użyciu zasad klucza zawartości z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token JWT lub prosty token sieci Web (SWT) w żądaniu licencji/klucza. Ten token musi zostać wystawiony przez usługę STS.

Możesz użyć usługi Azure AD jako usług STS lub wdrożyć [niestandardową usługę STS](#using-a-custom-sts). Usługa STS musi być skonfigurowana w taki sposób, aby utworzyć token podpisany przy użyciu określonego klucza i wystawiać oświadczenia określone w konfiguracji ograniczenia tokenu. Media Services licencji/usługi dostarczania kluczy zwraca żądaną licencję lub klucz do klienta w przypadku istnienia obu następujących warunków:

* Token jest prawidłowy.
* Oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla licencji lub klucza.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacyjny zawiera klucz, za pomocą którego token został podpisany. Wystawca to usługa STS, która wystawia token. Odbiorcy, czasami nazywane zakresem, opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa Media Services License/Key Delivery sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu tokenu

Funkcja *zapobiegania powtarzaniu tokenów* umożliwia Media Services klientom Ustawianie limitu, ile razy można użyć tego samego tokenu do żądania klucza lub licencji. Klient może dodać `urn:microsoft:azure:mediaservices:maxuses` w tokenie roszczeń typu, gdzie wartość jest liczbą przypadków, w których token może być używany w celu uzyskania licencji lub klucza. Wszystkie kolejne żądania o takim samym tokenie do dostarczania kluczy zwrócą nieautoryzowaną odpowiedź. Zobacz, jak dodać to zgłoszenie w [próbce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenu. Należy umieścić to zastrzeżenie w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia wynosi więcej niż godzinę od momentu odebrania żądania, są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich sygnaturę. Wszelkie zmiany w ładunku (na przykład aktualizacja czasu wygaśnięcia lub roszczeń) zmieniają sygnaturę tokenu i będą zliczane jako nowy token, który nie został wcześniej dostarczony.
* Odtwarzanie nie powiedzie się, jeśli token przekroczył `maxuses` wartość ustawioną przez klienta.
* Ta funkcja może być używana dla całej istniejącej zawartości chronionej (należy zmienić tylko token wystawiony).
* Ta funkcja działa z tokenami JWT i SWT.

## <a name="using-a-custom-sts"></a>Korzystanie z niestandardowej usługi STS

Klient może zdecydować się na użycie niestandardowej usługi STS w celu zapewnienia tokenów. Przyczyny:

* Dostawca tożsamości (dostawcy tożsamości) używany przez klienta nie obsługuje usługi STS. W takim przypadku niestandardowa usługa STS może być opcją.
* Klient może potrzebować bardziej elastycznej lub ściślejszej kontroli integracji usługi STS z systemem rozliczeń subskrybenta klienta.

   Na przykład operator usługi [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) może oferować wiele pakietów subskrybentów, takich jak Premium, Basic i sport. Operator może chcieć dopasować oświadczenia w tokenie przy użyciu pakietu abonenta, aby udostępnić tylko zawartość określonego pakietu. W takim przypadku niestandardowa usługa STS zapewnia niezbędną elastyczność i kontrolę.

* Aby uwzględnić niestandardowe oświadczenia w tokenie, aby wybrać różne ContentKeyPolicyOptions z różnymi parametrami licencji DRM (Licencja subskrypcyjna w porównaniu z licencją dzierżawy).
* Aby uwzględnić w nim zastrzeżenie reprezentujące identyfikator klucza zawartości klucza, do którego token przyznaje dostęp.

W przypadku korzystania z niestandardowej usługi STS należy wprowadzić dwie zmiany:

* Podczas konfigurowania usługi dostarczania licencji dla zasobu należy określić klucz zabezpieczeń używany do weryfikacji przez niestandardową usługę STS zamiast bieżącego klucza z usługi Azure AD.
* Po wygenerowaniu tokenu JTW zostanie określony klucz zabezpieczeń zamiast klucza prywatnego bieżącego certyfikatu x509 w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: ten sam klucz jest używany do generowania i weryfikowania tokenu JWT.
* Klucz asymetryczny: para publiczny-prywatny klucza w certyfikacie x509 jest używana z kluczem prywatnym do szyfrowania/generowania tokenu JWT oraz klucza publicznego w celu weryfikacji tokena.

Jeśli używasz .NET Framework/C# jako platformy deweloperskiej, certyfikat x509 użyty dla asymetrycznego klucza zabezpieczeń musi mieć długość klucza wynoszącą co najmniej 2048. Ta długość klucza jest wymaganiem klasy System. IdentityModel. Tokens. X509AsymmetricSecurityKey w .NET Framework. W przeciwnym razie zgłaszany jest następujący wyjątek: IDX10630: element "System. IdentityModel. Tokens. X509AsymmetricSecurityKey" dla podpisywania nie może być mniejszy niż "2048" bitów.

## <a name="custom-key-and-license-acquisition-url"></a>Adres URL pozyskiwania klucza niestandardowego i licencji

Użyj następujących szablonów, jeśli chcesz określić inną licencję/usługę dostarczania kluczy (nie Media Services). Dostępne są dwa pola, które można umieścić w szablonach, aby umożliwić udostępnianie zasad przesyłania strumieniowego w wielu zasobach zamiast tworzenia zasad przesyłania strumieniowego na element zawartości. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Szablon adresu URL usługi niestandardowej, która dostarcza klucze do odtwarzaczy użytkowników końcowych. Nie jest to wymagane, jeśli używasz Azure Media Services do wystawiania kluczy. 

   Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania.  Obecnie obsługiwane są następujące wartości tokenu:
   * `{AlternativeMediaId}`, który jest zastępowany przez wartość StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, która jest zastępowana wartością identyfikatora żądanego klucza.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Szablon adresu URL niestandardowej usługi dostarczającej licencje do graczy użytkowników końcowych. Nie jest to wymagane w przypadku korzystania z Azure Media Services w celu wystawiania licencji.

   Szablon obsługuje tokeny wymienne, które usługa będzie aktualizować w czasie wykonywania, przy użyciu wartości właściwej dla żądania. Obecnie obsługiwane są następujące wartości tokenu:  
   * `{AlternativeMediaId}`, który jest zastępowany przez wartość StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, która jest zastępowana wartością identyfikatora żądanego klucza. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Taki sam jak poprzedni szablon, tylko dla Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Taki sam jak poprzedni szablon, tylko dla FairPlay.  

Na przykład:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` ma wartość żądanego klucza. Możesz użyć, `AlternativeMediaId` Jeśli chcesz zmapować żądanie do jednostki po stronie. Na przykład `AlternativeMediaId` może służyć do wyszukiwania uprawnień.

Aby zapoznać się z przykładami użycia niestandardowych licencji/adresów URL pozyskiwania kluczy, zobacz [zasady przesyłania strumieniowego — tworzenie](/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` wystąpi błąd, upewnij się, że określono odpowiednie zasady przesyłania strumieniowego.

Jeśli pojawią się błędy, upewnij się `_NOT_SPECIFIED_IN_URL` , że w adresie URL został określony format szyfrowania. Może to być na przykład `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
* [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
* [Projektowanie systemu ochrony zawartości z obsługą technologii DRM z użyciem kontroli dostępu](design-multi-drm-system-with-access-control.md)
* [Szyfrowanie po stronie magazynu](storage-account-concept.md#storage-side-encryption)
* [Często zadawane pytania](frequently-asked-questions.md)
* [Program obsługi tokenów sieci Web JSON](/dotnet/framework/security/json-web-token-handler)
