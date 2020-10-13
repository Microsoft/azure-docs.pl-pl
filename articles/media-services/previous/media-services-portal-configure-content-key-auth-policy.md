---
title: Skonfiguruj zasady autoryzacji klucza zawartości przy użyciu Azure Portal | Microsoft Docs
description: W tym artykule przedstawiono sposób konfigurowania zasad autoryzacji klucza zawartości.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0218c24637b9b49972e93b8dd53e5970c9ab89d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89260904"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Omówienie
 Za pomocą Azure Media Services można dostarczyć strumienie MPEG-PAUZy, Smooth Streaming i HTTP Live Streaming (HLS) chronione za pomocą Advanced Encryption Standard (AES) przy użyciu kluczy szyfrowania 128-bitowych lub usługi [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/). Za pomocą Media Services można również dostarczyć strumienie KRESKOWAne zaszyfrowane za pomocą funkcji DRM Widevine. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7).

Media Services udostępnia również usługę dostarczania kluczy/licencji, z której klienci mogą uzyskać klucze AES lub licencje PlayReady/Widevine, aby odtworzyć zaszyfrowaną zawartość.

W tym artykule pokazano, jak za pomocą Azure Portal skonfigurować zasady autoryzacji klucza zawartości. Klucz może być później używany do dynamicznego szyfrowania zawartości. Obecnie można szyfrować formaty HLS, MPEG-KRESKa i Smooth Streaming. Nie można zaszyfrować pobrań progresywnych.

Gdy odtwarzacz żąda strumienia, który jest skonfigurowany do dynamicznego szyfrowania, Media Services używa skonfigurowanego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES lub DRM. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik ma uprawnienia do uzyskiwania klucza, usługa szacuje zasady autoryzacji określone dla klucza.

Jeśli planujesz używanie wielu kluczy zawartości lub chcesz określić adres URL usługi dostarczania kluczy/licencji inny niż usługa dostarczania kluczy Media Services, użyj zestawu SDK Media Services .NET lub interfejsów API REST. Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu zestawu SDK platformy Media Services .NET](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurowanie zasad autoryzacji klucza zawartości przy użyciu interfejsu API REST Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Niektóre zagadnienia mają zastosowanie
* Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego musi być w stanie "uruchomiony". 
* Element zawartości musi zawierać zestaw plików z adaptacyjną szybkością transmisji bitów pliki MP4 lub z adaptacyjną szybkością transmisji bitów Smooth Streaming. Aby uzyskać więcej informacji, zobacz [kodowanie elementu zawartości](media-services-encode-asset.md).
* Usługa dostarczania kluczy buforuje ContentKeyAuthorizationPolicy i powiązane z nią obiekty (opcje i ograniczenia zasad) przez 15 minut. Można utworzyć ContentKeyAuthorizationPolicy i określić, aby użyć ograniczenia tokenu, przetestować go, a następnie zaktualizować zasady do ograniczenia Open. Ten proces trwa około 15 minut przed przełączeniem zasad do otwartej wersji.
* Punkt końcowy przesyłania strumieniowego Media Services ustawia wartość nagłówka "Access-Control-Allow-Origin" w odpowiedzi wstępnej jako symbol wieloznaczny " \* ". Ta wartość działa dobrze w przypadku większości graczy, w tym Azure Media Player, roku i JWPlayer i innych. Jednak niektórzy gracze, którzy używają dash.js nie działają, ponieważ z trybem poświadczeń ustawionym na "include", XMLHttpRequest w ich dash.js nie zezwalają na symbol wieloznaczny " \* " jako wartość parametru Access-Control-Allow-Origin. Aby obejść to ograniczenie w dash.js, jeśli klient jest hostem z pojedynczej domeny, Media Services może określić tę domenę w nagłówku odpowiedzi inspekcji wstępnej. Aby uzyskać pomoc, Otwórz bilet pomocy technicznej za pomocą Azure Portal.

## <a name="configure-the-key-authorization-policy"></a>Konfigurowanie zasad autoryzacji klucza
Aby skonfigurować zasady autoryzacji klucza, wybierz stronę **Ochrona zawartości** .

Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania kluczy. Zasady autoryzacji klucza zawartości mogą mieć ograniczenia dotyczące otwierania, tokenu lub autoryzacji adresów IP. (Adres IP można skonfigurować przy użyciu interfejsu REST lub zestawu .NET SDK).

### <a name="open-restriction"></a>Otwórz ograniczenie
Ograniczenie Open oznacza, że system dostarcza klucz do każdego, kto wykonuje żądanie Key. To ograniczenie może być przydatne do celów testowych.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Ograniczenie tokenu
Aby wybrać zasady z ograniczeniami, wybierz przycisk **tokenu** .

Do zasad z ograniczeniami tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Media Services obsługuje tokeny w formatach prostego tokenu sieci Web ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) i tokenu sieci Web JSON (JWT). Aby uzyskać więcej informacji, zobacz [uwierzytelnianie JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services nie zapewnia usługi STS. Możesz utworzyć niestandardową usługę STS, aby wystawiać tokeny. Usługa STS musi być skonfigurowana w taki sposób, aby utworzyć token podpisany przy użyciu określonego klucza i wystawiać oświadczenia określone w konfiguracji ograniczenia tokenu. Jeśli token jest prawidłowy i oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla klucza zawartości, usługa dostarczania kluczy Media Services Zwraca klucz szyfrowania do klienta.

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacyjny zawiera klucz, za pomocą którego token został podpisany. Wystawca to usługa STS, która wystawia token. Odbiorcy (czasami nazywane zakresem) opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

### <a name="playready"></a>PlayReady
W przypadku ochrony zawartości za pomocą oprogramowania PlayReady jednym z rzeczy, które należy określić w zasadach autoryzacji, jest ciąg XML, który definiuje szablon licencji PlayReady. Domyślnie ustawione są następujące zasady:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

Można wybrać przycisk **Importuj XML zasad** i podać inny kod XML, który jest zgodny ze schematem XML zdefiniowanym w zasobie [Media Services Omówienie szablonu licencji PlayReady](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
