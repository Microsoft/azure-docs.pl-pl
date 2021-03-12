---
title: Ochrona zawartości za pomocą Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie ochrony zawartości w programie Azure Media Services V2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: edffa2dddd0ec877a4b825a69a76fb158928c89f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016767"
---
# <a name="content-protection-overview"></a>Omówienie ochrony zawartości

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Za pomocą Azure Media Services można zabezpieczyć nośnik od momentu, w którym komputer przejdzie przez magazyn, przetwarzanie i dostarczanie. Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługi do dostarczania kluczy AES oraz licencji DRM (PlayReady, Widevine i FairPlay) do autoryzowanych klientów. 

Na poniższej ilustracji przedstawiono przepływ pracy Media Services Content Protection: 

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

W tym artykule wyjaśniono pojęcia i Terminologia dotycząca ochrony zawartości za pomocą Media Services. Artykuł zawiera również linki do artykułów, w których omówiono sposób ochrony zawartości. 

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Możesz użyć Media Services, aby zapewnić zaszyfrowaną zawartość dynamicznie przy użyciu klucza Clear AES lub szyfrowania DRM za pomocą oprogramowania PlayReady, Widevine lub FairPlay. Jeśli zawartość jest zaszyfrowana przy użyciu klucza Clear AES i jest wysyłana za pośrednictwem protokołu HTTPS, nie jest to jasne, dopóki nie osiągnie klienta. 

Każda metoda szyfrowania obsługuje następujące protokoły przesyłania strumieniowego:
 
- AES: MPEG-KRESKa, Smooth Streaming i HLS
- PlayReady: MPEG-KRESKa, Smooth Streaming i HLS
- Widevine: MPEG-KRESKa
- FairPlay: HLS

Szyfrowanie przy pobieraniu progresywnym nie jest obsługiwane. 

Aby zaszyfrować zasób, należy skojarzyć klucz zawartości szyfrowania z zasobem, a także skonfigurować zasady autoryzacji klucza. Klucze zawartości mogą być określone lub generowane automatycznie przez Media Services.

Należy również skonfigurować zasady dostarczania elementu zawartości. Jeśli chcesz przesłać strumieniowo zasób szyfrowany przez magazyn, upewnij się, że określisz, w jaki sposób chcesz go dostarczyć przez skonfigurowanie zasad dostarczania elementów zawartości.

Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu klucza Wyczyść AES lub szyfrowania DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy Media Services. Aby zdecydować, czy użytkownik ma uprawnienia do uzyskiwania klucza, usługa szacuje zasady autoryzacji określone dla klucza.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Wyczyść klucz a DRM
Klienci często zastanawiają się, czy powinni używać szyfrowania AES, czy systemu DRM. Podstawowa różnica między tymi dwoma systemami polega na tym, że szyfrowanie AES klucza zawartości jest przesyłane do klienta w nieszyfrowanym formacie ("w czyszczeniu"). W związku z tym klucz użyty do zaszyfrowania zawartości może być wyświetlany w wyniku śledzenia sieci na kliencie w postaci zwykłego tekstu. Szyfrowanie za pomocą klucza AES-128 jest odpowiednie do przypadków użycia, w których przeglądarka jest zaufaną stroną (na przykład w przypadku szyfrowania wideo firmowych dystrybuowanych przez pracowników).

Technologie PlayReady, Widevine i FairPlay zapewniają wyższy poziom szyfrowania w porównaniu z szyfrowaniem klucza AES-128. Klucz zawartości jest przesyłany w postaci zaszyfrowanej. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, co utrudnia złośliwemu użytkownikowi ataki. Funkcja DRM jest zalecana w przypadku przypadków użycia, w których przeglądarka może nie być stroną zaufaną i wymagany jest najwyższy poziom zabezpieczeń.

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
Za pomocą szyfrowania magazynu można lokalnie zaszyfrować zawartość, używając szyfrowania AES 256-bitowego. Następnie można przekazać ją do usługi Azure Storage, gdzie jest przechowywana w stanie zaszyfrowanej. Zasoby chronione za pomocą szyfrowania magazynu są automatycznie odszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem. Zasoby są opcjonalnie ponownie szyfrowane przed przekazaniem ich jako nowego wyjściowego elementu zawartości. Podstawowym przypadkiem użycia szyfrowania magazynu jest Zabezpieczanie plików multimedialnych o wysokiej jakości z użyciem silnego szyfrowania na dysku.

Aby dostarczyć zasób szyfrowany przy użyciu magazynu, należy skonfigurować zasady dostarczania zasobów, aby Media Services wie, jak chcesz dostarczać zawartość. Przed przesłaniem strumieniowego zasobu serwer przesyłania strumieniowego odszyfrowuje i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, typowe szyfrowanie lub bez szyfrowania).

## <a name="types-of-encryption"></a>Typy szyfrowania
W przypadku oprogramowania PlayReady i Widevine wykorzystuje się typowe szyfrowanie (tryb AES). FairPlay wykorzystuje szyfrowanie AES w trybie CBC. Szyfrowanie przy nieszyfrowanej kluczu AES-128 wykorzystuje szyfrowanie kopert.

## <a name="licenses-and-keys-delivery-service"></a>Usługa dostarczania licencji i kluczy
Media Services zapewnia usługę dostarczania kluczy do dostarczania licencji DRM (PlayReady, Widevine, FairPlay) i kluczy AES do autoryzowanych klientów. Aby skonfigurować zasady autoryzacji i uwierzytelniania dla licencji i kluczy, można użyć [Azure Portal](media-services-portal-protect-content.md), interfejsu API REST lub zestawu SDK Media Services dla platformy .NET.

## <a name="control-content-access"></a>Kontrola dostępu do zawartości
Można kontrolować, kto ma dostęp do zawartości, konfigurując zasady autoryzacji klucza zawartości. Zasady autoryzacji klucza zawartości obsługują ograniczenie Open lub token.

### <a name="open-authorization"></a>Otwórz autoryzację
W przypadku otwartych zasad autoryzacji klucz zawartości jest wysyłany do dowolnego klienta (bez ograniczeń).

### <a name="token-authorization"></a>Autoryzacja tokenu
W przypadku zasad autoryzacji z ograniczeniami tokenu klucz zawartości jest wysyłany tylko do klienta, który przedstawia prawidłowy token sieci Web JSON (JWT) lub prosty token sieci Web (SWT) w żądaniu klucza/licencji. Token musi być wystawiony przez usługę tokenu zabezpieczającego (STS). Możesz użyć Azure Active Directory jako usługi STS lub wdrożyć niestandardową usługę STS. Usługa STS musi być skonfigurowana w taki sposób, aby utworzyć token podpisany przy użyciu określonego klucza i wystawiać oświadczenia określone w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services Zwraca żądany klucz/licencję do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla klucza/licencji.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacyjny zawiera klucz, za pomocą którego token został podpisany. Wystawca jest usługą bezpiecznego tokenu, która wystawia token. Odbiorcy, czasami nazywane zakresem, opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

### <a name="token-replay-prevention"></a>Zapobieganie powtarzaniu tokenu

Funkcja *zapobiegania powtarzaniu tokenów* umożliwia Media Services klientom Ustawianie limitu, ile razy można użyć tego samego tokenu do żądania klucza lub licencji. Klient może dodać `urn:microsoft:azure:mediaservices:maxuses` w tokenie roszczeń typu, gdzie wartość jest liczbą przypadków, w których token może być używany w celu uzyskania licencji lub klucza. Wszystkie kolejne żądania o takim samym tokenie do dostarczania kluczy zwrócą nieautoryzowaną odpowiedź. Zobacz, jak dodać to zgłoszenie w [próbce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci muszą mieć kontrolę nad generowaniem tokenu. Należy umieścić to zastrzeżenie w samym tokenie.
* Korzystając z tej funkcji, żądania z tokenami, których czas wygaśnięcia wynosi więcej niż godzinę od momentu odebrania żądania, są odrzucane z nieautoryzowaną odpowiedzią.
* Tokeny są jednoznacznie identyfikowane przez ich sygnaturę. Wszelkie zmiany w ładunku (na przykład aktualizacja czasu wygaśnięcia lub roszczeń) zmieniają sygnaturę tokenu i będą zliczane jako nowy token, który nie został wcześniej dostarczony.
* Odtwarzanie nie powiedzie się, jeśli token przekroczył `maxuses` wartość ustawioną przez klienta.
* Ta funkcja może być używana dla całej istniejącej zawartości chronionej (należy zmienić tylko token wystawiony).
* Ta funkcja działa z tokenami JWT i SWT.

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli zasób został zaszyfrowany za pomocą więcej niż jednego obiektu DRM, Użyj znacznika szyfrowania w adresie URL przesyłania strumieniowego: (format = "3u8-AAPL", szyfrowanie = "XXX").

Obowiązują następujące zastrzeżenia:

* Nie można określić więcej niż jednego typu szyfrowania.
* Nie trzeba określać typu szyfrowania w adresie URL, jeśli do zasobu zastosowano tylko jedno szyfrowanie.
* W przypadku typu szyfrowania wielkość liter nie jest rozróżniana.
* Można określić następujące typy szyfrowania:

  * **Cenc**: w przypadku oprogramowania PlayReady lub Widevine (typowe szyfrowanie)
  * **cbcs-AAPL**: for FairPlay (szyfrowanie AES CBC)
  * **CBC**: na potrzeby szyfrowania kopert AES

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły zawierają opis następnych kroków, które ułatwią rozpoczęcie pracy z usługą Content Protection:

* [Ochrona przy użyciu szyfrowania magazynu](media-services-rest-storage-encryption.md)
* [Ochrona przy użyciu szyfrowania AES](media-services-protect-with-aes128.md)
* [Ochrona za pomocą oprogramowania PlayReady i/lub Widevine](media-services-protect-with-playready-widevine.md)
* [Ochrona za pomocą FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Linki pokrewne

* [Uwierzytelnianie tokenu JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrowanie Azure Media Services OWIN aplikacji opartych na MVC z Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
