---
title: Projektowanie systemu ochrony zawartości z kontrolą dostępu przy użyciu Azure Media Services | Microsoft Docs
description: Dowiedz się więcej na temat licencjonowania zestawu portów Microsoft Smooth Streaming Clienting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: b98b66d8f0350c32e89d62d776ee1288d9271712
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841156"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Projektowanie systemu ochrony zawartości z kontrolą dostępu przy użyciu Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie

Projektowanie i kompilowanie podsystemu do zarządzania prawami cyfrowymi (DRM, Digital Rights Management) dla wieloszczytowego (OTT) lub rozwiązania do przesyłania strumieniowego online to złożone zadanie. Operatorzy/dostawcy wideo w trybie online zwykle są źródłem tego zadania do wyspecjalizowanych dostawców usług DRM. Celem tego dokumentu jest zaprezentowanie projektu referencyjnego i implementacji kompleksowego podsystemu DRM w ramach rozwiązania przesyłania strumieniowego OTT lub online.

Czytelnicy docelowa do tego dokumentu to inżynierowie, którzy pracują w podsystemach DRM OTT lub rozwiązaniach do przesyłania strumieniowego online/wieloekranowych lub czytelników, którzy chcą w podsystemie DRM. Przyjęto założenie, że czytelnicy znają co najmniej jedną technologię DRM na rynku, taką jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tym omówieniu technologii DRM obejmujemy również typowe szyfrowanie (CENC) z obsługą technologii wielowątkowości. Ważnym trendem w przypadku przesyłania strumieniowego online i branży OTT jest użycie CENC z wieloma natywnymi rozwiązaniami DRM na różnych platformach klienckich. Ten trend jest przesunięty od poprzedniego, który używa jednego elementu DRM i jego zestawu SDK klienta dla różnych platform klienckich. W przypadku używania CENC z wielonatywną funkcją DRM, zarówno PlayReady, jak i Widevine są szyfrowane zgodnie ze specyfikacją [Common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Zalety CENC z obsługą technologii DRM:

* Zmniejsza koszty szyfrowania, ponieważ pojedynczy proces szyfrowania jest używany do obsługi różnych platform, które mają natywną protokołów DRM.
* Zmniejsza koszty zarządzania zaszyfrowanymi zasobami, ponieważ jest wymagana tylko jedna kopia zaszyfrowanych zasobów.
* Eliminuje koszt licencjonowania klienta DRM, ponieważ natywny klient DRM jest zwykle bezpłatny na swojej natywnej platformie.

Firma Microsoft jest aktywnym podwyższeniem poziomu KRESek i CENC razem z kilkoma głównymi graczami branżowymi. Azure Media Services zapewnia obsługę ŁĄCZNIKów i CENC. W przypadku ostatnich anonsów zobacz następujące blogi:

*  [Powiadomienie o usługach dostarczania licencji Google Widevine w usłudze Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services dodaje pakiet Google Widevine do dostarczania strumienia z obsługą technologii DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Cele artykułu

Celem tego artykułu jest:

* Podaj projekt referencyjny podsystemu DRM, który używa CENC z obsługą technologii wielowątkowości.
* Podaj implementację referencyjną na platformie Azure/Media Services.
* Omawianie niektórych tematów dotyczących projektowania i implementacji.

W tym artykule termin "wiele DRM" obejmuje następujące produkty:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

W poniższej tabeli zestawiono natywną platformę/natywną aplikację i przeglądarki obsługiwane przez poszczególne DRM.

| **Klient platforma** | **Natywna obsługa technologii DRM** | **Przeglądarka/aplikacja** | **Formaty przesyłania strumieniowego** |
| --- | --- | --- | --- |
| **Inteligentne telewizory, operator STBs, OTT STBs** |PlayReady głównie, i/lub Widevine i/lub inne |Linux, Opera, WebKit, inne |Różne formaty |
| **Urządzenia z systemem Windows 10 (komputery z systemem Windows, tablety z systemem Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Platforma uniwersalna systemu Windows |KRESKa (dla HLS, PlayReady nie jest obsługiwana)<br/><br/>ŁĄCZNIK, Smooth Streaming (dla HLS, PlayReady nie jest obsługiwany) |
| **Urządzenia z systemem Android (telefon, tablet, telewizja)** |Widevine |Chrome/EME |KRESKA, HLS |
| **iOS (iPhone, iPad), klienci OS X i Apple TV** |FairPlay |Przeglądarka Safari 8 +/EME |HLS |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego DRM, usługa zazwyczaj chce zaimplementować dwa lub trzy protokołów DRM, aby upewnić się, że wszystkie typy punktów końcowych są zgodne z najlepszymi rozwiązaniami.

Istnieje kompromis między złożonością logiki usługi i złożonością po stronie klienta, aby uzyskać dostęp do określonego poziomu środowiska użytkownika na różnych klientach.

Aby wybrać tę opcję, należy pamiętać o następujących kwestiach:

* Oprogramowanie PlayReady jest natywnie implementowane na każdym urządzeniu z systemem Windows, na niektórych urządzeniach z systemem Android i dostępne za poorednictwem zestawów SDK oprogramowania na praktycznie dowolnej platformie.
* Widevine jest natywnie implementowany w każdym urządzeniu z systemem Android, w przeglądarce Chrome i w niektórych innych urządzeniach.
* FairPlay jest dostępny tylko dla klientów z systemem iOS i Mac OS lub za poorednictwem programu iTunes.

Istnieją dwie opcje dla typowej technologii wieloskładnikowej:

* PlayReady i Widevine
* PlayReady, Widevine i FairPlay

## <a name="a-reference-design"></a>Projekt referencyjny
W tej części przedstawiono projekt referencyjny, który jest niezależny od do technologii użytych do jego wdrożenia.

Podsystem DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Pakiet DRM
* Dostarczanie licencji DRM
* Sprawdzanie uprawnień
* Uwierzytelnianie/autoryzacja
* Odtwarzacz
* Pochodzenie/usługa Content Delivery Network (CDN)

Na poniższym diagramie przedstawiono interakcje wysokiego poziomu między składnikami w podsystemie DRM:

![Podsystem DRM z CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Projekt ma trzy warstwy podstawowe:

* Warstwa zaplecza (czarna) nie jest narażona na zewnątrz.
* Warstwa DMZ (ciemnoniebieski) zawiera wszystkie punkty końcowe, które są skierowane do publicznego.
* Publiczna warstwa internetowa (jasnoniebieski) zawiera sieć CDN i odtwarzacze z ruchem przez publiczny Internet.

Należy również określić narzędzie do zarządzania zawartością, aby kontrolować ochronę przy użyciu funkcji DRM niezależnie od tego, czy jest to szyfrowanie statyczne czy dynamiczne. Dane wejściowe dla szyfrowania DRM obejmują:

* Zawartość wideo MBR
* Klucz zawartości
* Adresy URL pozyskiwania licencji

Oto przepływ wysokiego poziomu w czasie odtwarzania:

* Użytkownik jest uwierzytelniany.
* Dla użytkownika zostanie utworzony Token autoryzacji.
* Zawartość chroniona przez DRM (manifest) jest pobierana do odtwarzacza.
* Odtwarzacz przesyła żądanie pozyskiwania licencji do serwerów licencji wraz z IDENTYFIKATORem klucza i tokenem autoryzacji.

W poniższej sekcji omówiono projektowanie zarządzania kluczami.

| **ContentKey do zasobu** | **Scenariusz** |
| --- | --- |
| 1 do 1 |Najprostszym przypadkiem. Udostępnia formant Finest. Jednak to porozumienie zazwyczaj skutkuje kosztem dostarczenia najwyższej licencji. W przypadku każdego chronionego zasobu wymagane jest co najmniej jedno żądanie licencji. |
| od 1 do wielu |Tego samego klucza zawartości można użyć dla wielu zasobów. Na przykład dla wszystkich zasobów w grupie logicznej, takich jak gatunek lub podzbiór gatunku (lub genu), można użyć jednego klucza zawartości. |
| Wiele do jednego |Dla każdego zasobu jest wymaganych wiele kluczy zawartości. <br/><br/>Na przykład jeśli konieczne jest zastosowanie dynamicznej ochrony CENC z użyciem technologii wielowątkowości MPEG-KRESKa i dynamicznego szyfrowania AES-128 dla HLS, potrzebne są dwa oddzielne klucze zawartości. Każdy klucz zawartości musi mieć własny ContentKeyType. (W przypadku klucza zawartości używanego do dynamicznej ochrony CENC należy użyć ContentKeyType. CommonEncryption. W przypadku klucza zawartości używanego do szyfrowania dynamicznego AES-128 Użyj ContentKeyType. EnvelopeEncryption.)<br/><br/>Innym przykładem w zakresie ochrony zawartości CENC jest użycie jednego klucza zawartości do ochrony strumienia wideo i innego klucza zawartości w celu ochrony strumienia audio. |
| Wiele do wielu |Kombinacja poprzednich dwóch scenariuszy. Jeden zestaw kluczy zawartości jest używany dla każdego z wielu zasobów w tej samej grupie zasobów. |

Innym ważnym czynnikiem, które należy wziąć pod uwagę, jest użycie trwałych i nietrwałych licencji.

Dlaczego te zagadnienia są ważne?

W przypadku korzystania z chmury publicznej do dostarczania licencji trwałe i nietrwałe mają bezpośredni wpływ na koszt dostarczania licencji. Następujące dwa różne przypadki projektowania umożliwiają zilustrowanie:

* Subskrypcja miesięczna: Użyj trwałej licencji i 1-do-wielu mapowania klucza zawartości. Na przykład w przypadku wszystkich filmów dla dzieci dla szyfrowania używany jest jeden klucz zawartości. W takim przypadku:

    Łączna Liczba żądanych licencji dla wszystkich filmów/urządzeń dla dzieci: 1

* Subskrypcja miesięczna: Użyj nietrwałej licencji i mapowania od 1 do 1 między kluczem zawartości i zasobem. W takim przypadku:

    Łączna Liczba żądanych licencji dla wszystkich filmów/urządzeń dla dzieci — [liczba filmów obserwowanych] x [liczba sesji]

Dwa różne projekty powodują powstanie bardzo różnych wzorców żądań licencji. Różne wzorce powodują różne koszty dostarczania licencji, jeśli usługa dostarczania licencji jest świadczona przez chmurę publiczną, taką jak Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mapowanie projektu na technologię dla implementacji
Następnie projekt generyczny jest mapowany na technologie na platformie Azure/Media Services przez określenie, która technologia ma być używana dla każdego bloku konstrukcyjnego.

W poniższej tabeli przedstawiono mapowanie.

| **Blok konstrukcyjny** | **Technologia** |
| --- | --- |
| **Zawodnik** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Dostawca tożsamości (dostawcy tożsamości)** |Azure Active Directory (Azure AD) |
| **Usługa tokenu zabezpieczającego (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Media Services ochronę dynamiczną |
| **Dostarczanie licencji DRM** |* Media Services dostarczania licencji (PlayReady, Widevine, FairPlay) <br/>* Axinom serwera licencji <br/>* Niestandardowy serwer licencji PlayReady |
| **Źródł** |Media Services punkt końcowy przesyłania strumieniowego |
| **Zarządzanie kluczami** |Niewymagane dla implementacji odwołania |
| **Zarządzanie zawartością** |Aplikacja konsolowa w języku C# |

Innymi słowy, zarówno dostawcy tożsamości, jak i STS są używane z usługą Azure AD. [Interfejs API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla odtwarzacza. Obie Media Services i Media Player obsługują ŁĄCZNIKi i CENC za pomocą technologii wielowątkowości.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ przy użyciu poprzedniego mapowania technologii:

![CENC na Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować dynamiczne szyfrowanie CENC, narzędzie do zarządzania zawartością używa następujących danych wejściowych:

* Otwórz zawartość
* Klucz zawartości z generowania/zarządzania kluczami
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD

Oto dane wyjściowe narzędzia do zarządzania zawartością:

* ContentKeyAuthorizationPolicy zawiera specyfikację, w jaki sposób dostarczanie licencji weryfikuje token sieci Web JSON (JWT) i specyfikacje licencji DRM.
* AssetDeliveryPolicy zawiera specyfikacje dotyczące formatu przesyłania strumieniowego, ochrony DRM i adresów URL pozyskiwania licencji.

Oto przepływ w czasie wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany token JWT.
* Jedno z oświadczeń zawartych w tokenie JWT jest oświadczeniem grup zawierającym identyfikator obiektu grupy EntitledUserGroup. To zastrzeżenie służy do przekazania kontroli uprawnień.
* Odtwarzacz pobiera manifest klienta zawartości chronionej przez CENC i identyfikuje następujące elementy:
   * Identyfikator klucza.
   * Zawartość jest CENC chroniona.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz wykonuje żądanie pozyskiwania licencji na podstawie obsługiwanej przeglądarki/funkcji DRM. W żądaniu pozyskiwania licencji zostanie również przesłany identyfikator klucza i token JWT. Usługa dostarczania licencji weryfikuje token JWT i oświadczenia zawarte przed wystawianiem wymaganej licencji.

## <a name="implementation"></a>Implementacja
### <a name="implementation-procedures"></a>Procedury implementacji
Implementacja obejmuje następujące kroki:

1. Przygotuj zasoby testowe. Koduj/Pakuj testowe wideo do pofragmentowanych plików MP4 o dużej szybkości transmisji bitów w Media Services. Ten zasób *nie* jest chroniony przez DRM. Ochronę za pomocą technologii DRM wykonuje się później.

2. Utwórz identyfikator klucza i klucz zawartości (opcjonalnie z inicjatora kluczy). W tym przypadku system zarządzania kluczami nie jest wymagany, ponieważ tylko jeden identyfikator klucza i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Użyj interfejsu API Media Services, aby skonfigurować usługi dostarczania licencji na wiele DRM dla elementu zawartości testowej. Jeśli używasz niestandardowych serwerów licencji przez firmę lub dostawców firmy zamiast usług licencjonowania w Media Services, możesz pominąć ten krok. Możesz określić adresy URL pozyskiwania licencji w kroku podczas konfigurowania dostarczania licencji. Interfejs API Media Services jest wymagany do określenia niektórych szczegółowych konfiguracji, takich jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencjonowania DRM. W tej chwili Azure Portal nie zapewnia interfejsu użytkownika wymaganego dla tej konfiguracji. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](media-services-protect-with-playready-widevine.md)).

4. Użyj interfejsu API Media Services, aby skonfigurować zasady dostarczania elementów zawartości dla testu. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](media-services-protect-with-playready-widevine.md)).

5. Utwórz i skonfiguruj dzierżawę usługi Azure AD na platformie Azure.

6. Utwórz kilka kont użytkowników i grup w dzierżawie usługi Azure AD. Utwórz co najmniej grupę "zatytułowany użytkownik" i Dodaj użytkownika do tej grupy. Użytkownicy w tej grupie przekazują uprawnienia do pozyskiwania licencji. Użytkownicy niebędący w tej grupie nie przekazują sprawdzania uwierzytelniania i nie mogą uzyskać licencji. Członkostwo w grupie "uprawniony użytkownik" to wymagane grupy w tokenie JWT wystawionym przez usługę Azure AD. To wymaganie dotyczące roszczeń należy określić w kroku podczas konfigurowania usług dostarczania licencji na wiele DRM.

7. Utwórz aplikację ASP.NET MVC do hostowania odtwarzacza wideo. Ta aplikacja ASP.NET jest chroniona przy użyciu uwierzytelniania użytkownika w przypadku dzierżawy usługi Azure AD. W tokenach dostępu uzyskanych po uwierzytelnieniu użytkownika są zawarte odpowiednie oświadczenia. Zalecamy OpenID Connect Connect API dla tego kroku. Zainstaluj następujące pakiety NuGet:

   * Install-Package Microsoft. Azure. ActiveDirectory. GraphClient
   * Install-Package Microsoft. Owin. Security. OpenIdConnect
   * Install-Package Microsoft. Owin. Security. cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft. IdentityModel. clients. ActiveDirectory

8. Utwórz odtwarzacz przy użyciu [interfejsu API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Użyj [interfejsu API Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) , aby określić technologię DRM, która ma być używana na różnych platformach DRM.

9. W poniższej tabeli przedstawiono macierz testową.

    | **ZASTOSOWANIE** | **Przeglądarka** | **Wynik dla uprawnionego użytkownika** | **Wynik nieuprawnionego użytkownika** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge lub Internet Explorer 11 w systemie Windows 10 |Połączyć |Niepowodzenie |
    | **Widevine** |Chrome, Firefox, Opera |Połączyć |Niepowodzenie |
    | **FairPlay** |Safari w witrynie macOS      |Połączyć |Niepowodzenie |
    | **AES-128** |Większość nowoczesnych przeglądarek  |Połączyć |Niepowodzenie |

Aby uzyskać informacje na temat sposobu konfigurowania usługi Azure AD dla aplikacji odtwarzacza ASP.NET MVC, zobacz [Integrowanie aplikacji opartej na platformie mvc Azure Media Services Owin z Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie tokenu JWT w Azure Media Services i szyfrowanie dynamiczne](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje na temat usługi Azure AD:

* Informacje dla deweloperów można znaleźć w [przewodniku dewelopera Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md).
* Informacje o administratorze można znaleźć w temacie [administrowanie katalogiem dzierżawy usługi Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Niektóre problemy w implementacji
Aby uzyskać pomoc dotyczącą problemów z implementacją, Skorzystaj z poniższych informacji dotyczących rozwiązywania problemów.

* Adres URL wystawcy musi kończyć się znakiem "/". Odbiorcy muszą być IDENTYFIKATORem klienta aplikacji odtwarzacza. Należy również dodać znak "/" na końcu adresu URL wystawcy.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    W [dekoderze JWT](http://jwt.calebb.net/)zobaczysz **AUD** i **ISS**, jak pokazano w tokenie JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na karcie **Konfiguracja** aplikacji. Dla każdej aplikacji, w wersji lokalnej i wdrożonej wymagane są uprawnienia.

    ![Uprawnienia](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Podczas konfigurowania dynamicznej ochrony CENC należy użyć właściwego wystawcy.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Następujące elementy nie działają:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    Ten identyfikator GUID jest IDENTYFIKATORem dzierżawy usługi Azure AD. Identyfikator GUID można znaleźć w menu podręcznym **punkty końcowe** w Azure Portal.

* Przyznaj uprawnienia do oświadczeń członkostwa w grupie. Upewnij się, że w pliku manifestu aplikacji usługi Azure AD: 

    "groupMembershipClaims": "All" (wartość domyślna to null)

* Ustaw odpowiedni TokenType podczas tworzenia wymagań dotyczących ograniczeń.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Ponieważ oprócz usługi SWT (ACS) dodano obsługę tokenu JWT (Azure AD), domyślnym ustawieniem TokenType jest TokenType. JWT. W przypadku korzystania z usługi SWT/ACS należy ustawić token na wartość TokenType. SWT.

## <a name="additional-topics-for-implementation"></a>Dodatkowe tematy dotyczące implementacji
W tej sekcji omówiono niektóre dodatkowe tematy dotyczące projektowania i implementacji.

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza ASP.NET MVC musi obsługiwać następujące elementy:

* Uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD, która jest objęta protokołem HTTPS.
* Wymiana JWT między klientem a usługą Azure AD, która jest objęta protokołem HTTPS.
* Pozyskiwanie licencji DRM przez klienta, które musi znajdować się w protokole HTTPS, jeśli dostarczanie licencji jest zapewnione przez Media Services. Pakiet produktów PlayReady nie wymaga protokołu HTTPS do dostarczania licencji. Jeśli Twój serwer licencji PlayReady znajduje się poza Media Services, możesz użyć protokołu HTTP lub HTTPS.

Aplikacja odtwarzacza ASP.NET używa protokołu HTTPS zgodnie z najlepszymi rozwiązaniami, więc Media Player znajduje się na stronie w obszarze protokołu HTTPS. Jednak protokół HTTP jest preferowany do przesyłania strumieniowego, dlatego należy wziąć pod uwagę problem związany z zawartością mieszaną.

* Przeglądarka nie zezwala na zawartość mieszaną. Ale wtyczki, takie jak Silverlight i wtyczka OSMF, obsługują gładkie i PAUZy. Zawartość mieszana to problem z bezpieczeństwem ze względu na zagrożenie wynikające z możliwości iniekcji złośliwego kodu JavaScript, co może spowodować zagrożenie danych klientów. Przeglądarki blokują tę funkcję domyślnie. Jedynym sposobem obejścia tego problemu jest to, że po stronie serwera (pochodzenia) można zezwalać na wszystkie domeny (niezależnie od protokołu HTTPS lub HTTP). To prawdopodobnie nie jest dobrym pomysłem.
* Unikaj mieszanej zawartości. Zarówno aplikacja odtwarzacza, jak i Media Player powinny używać protokołu HTTP lub HTTPS. W przypadku odtwarzania zawartości mieszanej Technologia Silverlight wymaga wyczyszczenia ostrzeżenia o zawartości mieszanej. Błyskowa techniczna obsługuje zawartość mieszaną bez ostrzeżenia o zawartości mieszanej.
* Jeśli punkt końcowy przesyłania strumieniowego został utworzony przed 2014 sierpnia, nie będzie obsługiwał protokołu HTTPS. W takim przypadku Utwórz i Użyj nowego punktu końcowego przesyłania strumieniowego dla protokołu HTTPS.

W implementacji odniesienia dla zawartości chronionej za pomocą technologii DRM zarówno aplikacja, jak i Transmisja strumieniowa są objęte protokołem HTTPS. W przypadku otwartych zawartości odtwarzacz nie wymaga uwierzytelniania ani licencji, więc można użyć protokołu HTTP lub HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Przerzucanie klucza podpisywania Azure Active Directory
Przerzucanie klucza podpisywania jest ważnym punktem, który należy wziąć pod uwagę w implementacji. Jeśli zignorujesz go, gotowy system ostatecznie przestanie działać w ciągu sześciu tygodni.

Usługa Azure AD korzysta ze standardów branżowych, aby ustanowić relację zaufania między sobą i aplikacjami korzystającymi z usługi Azure AD. W przypadku usługi Azure AD jest wykorzystywany klucz podpisywania składający się z pary kluczy publicznych i prywatnych. Gdy usługa Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku, jest podpisany przez usługę Azure AD z kluczem prywatnym przed wysłaniem ich z powrotem do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzi z usługi Azure AD, aplikacja musi sprawdzić poprawność podpisu tokenu. Aplikacja używa klucza publicznego uwidacznianego przez usługę Azure AD, który znajduje się w dokumencie metadanych Federacji dzierżawcy. Ten klucz publiczny i klucz podpisywania, z którego pochodzi, jest taki sam, jak używany dla wszystkich dzierżawców w usłudze Azure AD.

Aby uzyskać więcej informacji na temat przerzucania kluczy usługi Azure AD, zobacz [Ważne informacje dotyczące przerzucania klucza podpisywania w usłudze Azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md).

Między [pary kluczy publiczny-prywatny](https://login.microsoftonline.com/common/discovery/keys/):

* Klucz prywatny jest używany przez usługę Azure AD do generowania tokenu JWT.
* Klucz publiczny jest używany przez aplikację, taką jak usługi dostarczania licencji DRM w Media Services, aby zweryfikować token JWT.

Ze względów bezpieczeństwa usługa Azure AD okresowo obraca certyfikat (co sześć tygodni). W przypadku naruszeń zabezpieczeń, Przerzucanie klucza może wystąpić w dowolnym momencie. W związku z tym usługi dostarczania licencji w Media Services muszą zaktualizować klucz publiczny używany jako usługa Azure AD. W przeciwnym razie uwierzytelnianie tokenu w Media Services kończy się niepowodzeniem i nie wydano licencji.

Aby skonfigurować tę usługę, należy ustawić TokenRestrictionTemplate. OpenIdConnectDiscoveryDocument podczas konfigurowania usług dostarczania licencji DRM.

Oto przepływ JWT:

* Usługa Azure AD wystawia token JWT z bieżącym kluczem prywatnym dla uwierzytelnionego użytkownika.
* Gdy gracz zobaczy CENC z zawartością chronioną za pomocą technologii DRM, najpierw lokalizuje token JWT wystawiony przez usługę Azure AD.
* Odtwarzacz wysyła żądanie pozyskiwania licencji z tokenem JWT do usług dostarczania licencji w Media Services.
* Usługi dostarczania licencji w Media Services używają bieżącego/prawidłowego klucza publicznego z usługi Azure AD do weryfikacji tokenu JWT przed wystawieniem licencji.

Usługi dostarczania licencji DRM zawsze sprawdzają bieżący/prawidłowy klucz publiczny z usługi Azure AD. Klucz publiczny przedstawiony przez usługę Azure AD jest kluczem używanym do weryfikowania tokenu JWT wystawionego przez usługę Azure AD.

Co się stanie, gdy nastąpi Przerzucanie klucza po wygenerowaniu przez usługę Azure AD tokenu JWT, ale przed wysłaniem tokenu JWT przez graczy do usług dostarczania licencji DRM w Media Services do weryfikacji?

Ponieważ klucz może być rzutowany w dowolnym momencie, w dokumencie metadanych Federacji zawsze jest dostępny więcej niż jeden prawidłowy klucz publiczny. Media Services dostawy licencji mogą korzystać z dowolnego klucza określonego w dokumencie. Ponieważ jeden klucz może być rzutowany wkrótce, inny może być zastępujący i tak dalej.

### <a name="where-is-the-access-token"></a>Gdzie jest token dostępu?
Jeśli dowiesz się, jak aplikacja sieci Web wywołuje aplikację interfejsu API w obszarze [tożsamość aplikacji z uwierzytelnianiem przy użyciu poświadczeń klienta OAuth 2,0](../../active-directory/azuread-dev/web-api.md), przepływ uwierzytelniania jest następujący:

* Użytkownik loguje się do usługi Azure AD w aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [przeglądarka sieci Web w aplikacji sieci Web](../../active-directory/azuread-dev/web-app.md).
* Punkt końcowy autoryzacji usługi Azure AD przekierowuje agenta użytkownika z powrotem do aplikacji klienckiej przy użyciu kodu autoryzacji. Agent użytkownika zwraca kod autoryzacji do identyfikatora URI przekierowania aplikacji klienta.
* Aplikacja sieci Web musi uzyskać token dostępu, aby można było uwierzytelnić się w interfejsie API sieci Web i pobrać żądany zasób. Wysyła żądanie do punktu końcowego tokenu usługi Azure AD oraz udostępnia poświadczenia, identyfikator klienta i identyfikator URI aplikacji interfejsu API sieci Web. Przedstawia kod autoryzacji, aby udowodnić, że użytkownik wyraził zgodę.
* Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT używany do wywoływania interfejsu API sieci Web.
* Za pośrednictwem protokołu HTTPS aplikacja sieci Web używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku "Authorization" żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT. Jeśli walidacja zakończyła się pomyślnie, zwraca żądany zasób.

W tym przepływie tożsamości aplikacji internetowy interfejs API ufa, że aplikacja sieci Web uwierzytelnił użytkownika. Z tego powodu ten wzorzec jest nazywany zaufanym podsystemem. [Diagram przepływu autoryzacji](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) opisuje sposób działania przepływu przydzielenia kodu autoryzacji.

Pozyskiwanie licencji przy użyciu ograniczenia tokenu jest zgodne z tym samym wzorcem zaufanego podsystemu. Usługa dostarczania licencji w Media Services jest zasobem internetowego interfejsu API lub "zasobem zaplecza", do którego aplikacja sieci Web musi uzyskać dostęp. Więc gdzie jest token dostępu?

Token dostępu jest uzyskiwany z usługi Azure AD. Po pomyślnym uwierzytelnieniu użytkownika zostaje zwrócony kod autoryzacji. Kod autoryzacji jest następnie używany wraz z IDENTYFIKATORem klienta i kluczem aplikacji do wymiany dla tokenu dostępu. Token dostępu służy do uzyskiwania dostępu do aplikacji "wskaźnik" wskazującej lub reprezentującej usługę dostarczania licencji Media Services.

Aby zarejestrować i skonfigurować aplikację wskaźnika w usłudze Azure AD, wykonaj następujące czynności:

1. W dzierżawie usługi Azure AD:

   * Dodaj aplikację (zasób) przy użyciu adresu URL logowania https://[resource_name]. azurewebsites. NET/. 
   * Dodaj identyfikator aplikacji o adresie URL https://[aad_tenant_name]. onmicrosoft. com/[resource_name].

2. Dodaj nowy klucz dla aplikacji zasobu.

3. Zaktualizuj plik manifestu aplikacji tak, aby Właściwość groupMembershipClaims miała wartość "groupMembershipClaims": "All".

4. W aplikacji usługi Azure AD, która wskazuje aplikację sieci Web odtwarzacza, w sekcji **uprawnienia do innych aplikacji**Dodaj aplikację zasobu, która została dodana w kroku 1. W obszarze **delegowane uprawnienia**wybierz pozycję **dostęp [resource_name]**. Ta opcja zapewnia aplikacji sieci Web uprawnienia do tworzenia tokenów dostępu, które uzyskują dostęp do aplikacji zasobów. Zrób to zarówno dla lokalnej, jak i wdrożonej wersji aplikacji sieci Web, jeśli pracujesz z programem Visual Studio i aplikacją internetową platformy Azure.

Token dostępu wystawiony przez usługę Azure AD jest używany do uzyskiwania dostępu do zasobu wskaźnika.

### <a name="what-about-live-streaming"></a>Co z przesyłaniem strumieniowym na żywo?
Poprzednie dyskusje koncentrują się na zasobach na żądanie. Co z przesyłaniem strumieniowym na żywo?

Aby chronić przesyłanie strumieniowe na żywo w Media Services, można użyć dokładnie tego samego projektu i implementacji, traktując zasób skojarzony z programem jako zasób VOD.

W celu przeprowadzenia przesyłania strumieniowego na żywo w Media Services należy utworzyć kanał, a następnie utworzyć program w ramach kanału. Aby utworzyć program, należy utworzyć element zawartości zawierający Archiwum na żywo dla programu. Aby zapewnić CENC z ochroną za pomocą technologii DRM na żywo, Zastosuj tę samą konfigurację/przetwarzanie do elementu zawartości, tak jakby był to zasób VOD przed uruchomieniem programu.

### <a name="what-about-license-servers-outside-media-services"></a>Co o serwerach licencji poza Media Services?
Często klienci zainwestowali w farmie serwerów licencji w ich własnym centrum danych lub jednym hostowanym przez dostawców usług DRM. Za pomocą Content Protection Media Services można działać w trybie hybrydowym. Zawartość może być hostowana i dynamicznie chroniona w Media Services, natomiast licencje DRM są dostarczane przez serwery poza Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Usługa STS musi wydać tokeny, które są akceptowalne i mogą być weryfikowane przez farmę serwerów licencji. Na przykład serwery licencji Widevine udostępniane przez Axinom wymagają określonego tokenu JWT, który zawiera komunikat o uprawnieniach. W związku z tym musisz mieć usługę STS, aby wystawić taki token JWT. Aby uzyskać informacje dotyczące tego typu implementacji, przejdź do [centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/) i zobacz temat [Używanie Axinom do dostarczania licencji Widevine do Azure Media Services](media-services-axinom-integration.md).
* Nie musisz już konfigurować usługi dostarczania licencji (ContentKeyAuthorizationPolicy) w Media Services. Należy podać adresy URL pozyskiwania licencji (dla oprogramowania PlayReady, Widevine i FairPlay) podczas konfigurowania usługi AssetDeliveryPolicy w celu skonfigurowania CENC przy użyciu funkcji wieloskładnikowej DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co zrobić, jeśli chcę używać niestandardowej usługi STS?
Klient może zdecydować się na użycie niestandardowej usługi STS w celu zapewnienia JWTs. Przyczyny:

* DOSTAWCY tożsamości używany przez klienta nie obsługuje usługi STS. W takim przypadku niestandardowa usługa STS może być opcją.
* Klient może potrzebować bardziej elastycznej lub ściślejszej kontroli integracji usługi STS z systemem rozliczeń subskrybenta klienta. Na przykład operator MVPD może oferować wiele pakietów subskrybentów OTT, takich jak Premium, Basic i sport. Operator może chcieć dopasować oświadczenia w tokenie przy użyciu pakietu abonenta, aby udostępnić tylko zawartość określonego pakietu. W takim przypadku niestandardowa usługa STS zapewnia niezbędną elastyczność i kontrolę.

W przypadku korzystania z niestandardowej usługi STS należy wprowadzić dwie zmiany:

* Podczas konfigurowania usługi dostarczania licencji dla zasobu należy określić klucz zabezpieczeń używany do weryfikacji przez niestandardową usługę STS zamiast bieżącego klucza z usługi Azure AD. (Dalsze szczegóły znajdują się poniżej). 
* Po wygenerowaniu tokenu JTW zostanie określony klucz zabezpieczeń zamiast klucza prywatnego bieżącego certyfikatu x509 w usłudze Azure AD.

Istnieją dwa typy kluczy zabezpieczeń:

* Klucz symetryczny: ten sam klucz jest używany do generowania i weryfikowania tokenu JWT.
* Klucz asymetryczny: para publiczny-prywatny klucza w certyfikacie x509 jest używana z kluczem prywatnym do szyfrowania/generowania tokenu JWT oraz klucza publicznego w celu weryfikacji tokena.

> [!NOTE]
> Jeśli używasz .NET Framework/C# jako platformy deweloperskiej, certyfikat x509 użyty dla asymetrycznego klucza zabezpieczeń musi mieć długość klucza wynoszącą co najmniej 2048. Jest to wymaganie klasy System. IdentityModel. Tokens. X509AsymmetricSecurityKey w .NET Framework. W przeciwnym razie zgłaszany jest następujący wyjątek:
> 
> IDX10630: element "System. IdentityModel. Tokens. X509AsymmetricSecurityKey" dla podpisywania nie może być mniejszy niż "2048" bitów.

## <a name="the-completed-system-and-test"></a>Ukończony system i test
Ta sekcja przeprowadzi Cię przez następujące scenariusze w zakończonym kompleksowym systemie, dzięki czemu można mieć podstawowy obraz zachowania przed uzyskaniem konta logowania:

* Jeśli potrzebujesz scenariusza niezintegrowanego:

    * W przypadku zasobów wideo hostowanych w Media Services, które nie są chronione lub objęte ochroną DRM, ale bez uwierzytelniania przy użyciu tokenu Przełączenie do protokołu HTTP w przypadku przesyłania strumieniowego wideo za pośrednictwem protokołu HTTP.

* Jeśli potrzebujesz kompleksowego scenariusza zintegrowanego:

    * W przypadku zasobów wideo w ramach dynamicznej ochrony DRM w Media Services z uwierzytelnianiem tokenu i tokenem JWT generowanym przez usługę Azure AD musisz się zalogować.

W przypadku aplikacji sieci Web odtwarzacza i jej logowania zobacz [tę witrynę sieci Web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Logowanie użytkowników
W celu przetestowania kompleksowego zintegrowanego systemu DRM należy utworzyć lub dodać konto.

Co to jest konto?

Mimo że na platformie Azure zezwolono na dostęp tylko do konto Microsoft użytkowników, użytkownicy z obu systemów mogą teraz uzyskać dostęp. Wszystkie właściwości platformy Azure ufają teraz usłudze Azure AD na potrzeby uwierzytelniania, a usługa Azure AD uwierzytelnia użytkowników w organizacji. Utworzono relację Federacji, w której usługa Azure AD ufa systemowi tożsamości konsumenta konto Microsoft do uwierzytelniania użytkowników indywidualnych. W związku z tym usługa Azure AD może uwierzytelniać konta Microsoft gościa oraz natywne konta usługi Azure AD.

Ponieważ usługa Azure AD ufa domenie konto Microsoft, można dodać dowolne konta z dowolnej z następujących domen do niestandardowej dzierżawy usługi Azure AD i użyć konta do logowania:

| **Nazwa domeny** | **Domeny** |
| --- | --- |
| **Niestandardowa domena dzierżawy usługi Azure AD** |somename.onmicrosoft.com |
| **Domena firmowa** |microsoft.com |
| **Domena konto Microsoft** |outlook.com, live.com, hotmail.com |

Możesz skontaktować się z dowolnym autorem, aby utworzyć lub dodać konto.

Poniższe zrzuty ekranu przedstawiają różne strony logowania używane przez różne konta domeny:

**Niestandardowe konto domeny dzierżawy usługi Azure AD**: dostosowana Strona logowania niestandardowej domeny dzierżawy usługi Azure AD.

![Zrzut ekranu pokazujący dostosowaną stronę logowania niestandardowej domeny dzierżawy platformy Azure A D.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny Microsoft z kartą inteligentną**: Strona logowania dostosowana przez firmę Microsoft w firmie przy użyciu uwierzytelniania dwuskładnikowego.

![Zrzut ekranu przedstawiający stronę logowania dostosowaną przez firmę Microsoft w firmach I T z uwierzytelnianiem dwuskładnikowym.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: strona logowania konto Microsoft dla użytkowników.

![Niestandardowe konto domeny dzierżawy usługi Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Korzystanie z rozszerzeń nośników zaszyfrowanych dla oprogramowania PlayReady
W nowoczesnej przeglądarce z rozszerzeniami szyfrowanych multimediów (EME) na potrzeby obsługi rozwiązań PlayReady, takich jak Internet Explorer 11 w Windows 8.1 lub nowszej i przeglądarka Microsoft Edge w systemie Windows 10, PlayReady jest podstawowym mechanizmem DRM dla EME.

![Korzystanie z EME dla oprogramowania PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Ciemny obszar odtwarzacza jest spowodowany tym, że ochrona PlayReady uniemożliwia przechwycenie ekranu chronionego wideo.

Poniższy zrzut ekranu przedstawia wtyczki odtwarzacza i Microsoft Security Essentials (MSE)/EME support:

![Wtyczki odtwarzacza dla oprogramowania PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME w przeglądarce Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia wywoływanie oprogramowania [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) na urządzeniach z systemem Windows 10, które je obsługują. Usługa PlayReady SL3000 odblokowuje przepływ rozszerzonej zawartości w warstwie Premium (4K, HDR) i nowych modeli dostarczania zawartości (dla zawartości rozszerzonej).

Aby skoncentrować się na urządzeniach z systemem Windows, PlayReady jest jedynym urządzeniem DRM dostępnym na urządzeniach z systemem Windows (PlayReady SL3000). Usługa przesyłania strumieniowego może korzystać z oprogramowania PlayReady za pośrednictwem EME lub za pośrednictwem aplikacji platforma uniwersalna systemu Windows i oferować wyższą jakość wideo przy użyciu oprogramowania PlayReady SL3000 niż inne DRM. Zwykle zawartość do 2K przepływów za pomocą przeglądarki Chrome lub Firefox oraz zawartość do i do 4 KB przepływów za pomocą przeglądarki Microsoft Edge/Internet Explorer 11 lub aplikacji platforma uniwersalna systemu Windows na tym samym urządzeniu. Ta kwota zależy od ustawień i implementacji usługi.

#### <a name="use-eme-for-widevine"></a>Użyj EME dla Widevine
W nowoczesnej przeglądarce z obsługą EME/Widevine, taką jak Chrome 41 + w systemie Windows 10, Windows 8.1, Mac OSX Yosemite i Chrome w systemie Android 4.4.4, firma Google Widevine jest funkcją DRM za EME.

![Użyj EME dla Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwia przechwycenia ekranu chronionego wideo.

![Wtyczki odtwarzacza dla Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Nieuprawnieni użytkownicy
Jeśli użytkownik nie jest członkiem grupy "przysługuje Ci użytkownicy", nie zostanie on przekazany do sprawdzenia uprawnień. Usługa licencji z obsługą technologii DRM odmówi, aby wystawić żądaną licencję, jak pokazano. Szczegółowy opis to "nie można pobrać licencji", co jest tak zaprojektowane.

![Nieuprawnieni użytkownicy](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchamianie niestandardowej usługi tokenu zabezpieczającego
W przypadku uruchomienia niestandardowej usługi STS token JWT zostanie wystawiony przez niestandardową usługę STS przy użyciu klucza symetrycznego lub asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (przy użyciu przeglądarki Chrome):

![Niestandardowa usługa STS z kluczem symetrycznym](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego za pośrednictwem certyfikatu x509 (przy użyciu nowoczesnej przeglądarki firmy Microsoft):

![Niestandardowa usługa STS z kluczem asymetrycznym](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

W obu powyższych przypadkach uwierzytelnianie użytkownika pozostaje takie samo. Odbywa się to za pomocą usługi Azure AD. Jedyną różnicą jest to, że JWTs są wystawiane przez niestandardową usługę STS zamiast usługi Azure AD. W przypadku skonfigurowania dynamicznej ochrony CENC ograniczenie usługi dostarczania licencji określa typ JWT, czyli klucz symetryczny lub asymetryczny.

## <a name="summary"></a>Podsumowanie

Ten dokument omawiający CENC z wielonatywną obsługą technologii DRM i kontroli dostępu za pośrednictwem uwierzytelniania za pomocą tokenu, jego projektu i jego implementacji przy użyciu platformy Azure, Media Services i Media Player.

* Przedstawiono projekt referencyjny, który zawiera wszystkie niezbędne składniki w podsystemie DRM/CENC.
* Zaprezentowano implementację referencyjną na platformie Azure, Media Services i Media Player.
* Omówione zostały również niektóre tematy bezpośrednio związane z projektowaniem i implementacją.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
