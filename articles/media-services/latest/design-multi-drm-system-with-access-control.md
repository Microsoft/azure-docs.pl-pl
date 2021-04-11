---
title: System ochrony zawartości z obsługą technologii DRM
description: W tym artykule przedstawiono szczegółowy opis sposobu projektowania systemu ochrony zawartości z obsługą technologii DRM przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 057a284f99dd7c3713249b19e8fc39212d9e6003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640268"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Projektowanie i kompilowanie podsystemu Digital Rights Management (DRM) dla wieloszczytowego (OTT) lub rozwiązania przesyłania strumieniowego online to złożone zadanie. Operatorzy/dostawcy wideo w trybie online zwykle są źródłem tego zadania do wyspecjalizowanych dostawców usług DRM. Celem tego dokumentu jest zaprezentowanie projektu referencyjnego i implementację referencyjną kompleksowego podsystemu DRM w rozwiązaniu OTT lub online streaming.

Czytelnicy docelowa do tego dokumentu to inżynierowie, którzy pracują w podsystemach DRM OTT lub rozwiązaniach do przesyłania strumieniowego online/wieloekranowych lub czytelników, którzy chcą w podsystemie DRM. Przyjęto założenie, że czytelnicy znają co najmniej jedną technologię DRM na rynku, taką jak PlayReady, Widevine, FairPlay lub Adobe Access.

W tej dyskusji przez wiele technologii DRM dodaliśmy 3 protokołów DRM obsługiwane przez Azure Media Services: Common Encryption (CENC) dla oprogramowania PlayReady i Widevine, FairPlay, a także szyfrowania klucza AES-128. Ważnym trendem w przypadku przesyłania strumieniowego online i branży OTT jest korzystanie z natywnej protokołów DRM na różnych platformach klienckich. Ten trend jest przesunięty od poprzedniego, który używa jednego elementu DRM i jego zestawu SDK klienta dla różnych platform klienckich. W przypadku używania CENC z wielonatywną funkcją DRM, zarówno PlayReady, jak i Widevine są szyfrowane zgodnie ze specyfikacją [Common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Zalety korzystania z natywnej technologii wielowątkowości dla ochrony zawartości to:

* Zmniejsza koszty szyfrowania, ponieważ pojedynczy proces szyfrowania jest używany do obsługi różnych platform, które mają natywną protokołów DRM.
* Zmniejszenie kosztów zarządzania zasobami, ponieważ w magazynie jest wymagana tylko jedna kopia elementu zawartości.
* Eliminuje koszt licencjonowania klienta DRM, ponieważ natywny klient DRM jest zwykle bezpłatny na swojej natywnej platformie.

### <a name="goals-of-the-article"></a>Cele artykułu

Celem tego artykułu jest:

* Podaj projekt referencyjny dla podsystemu DRM, który używa wszystkich 3 protokołów DRM (CENC dla KRESKi, FairPlay dla HLS i PlayReady w celu zapewnienia płynnego przesyłania strumieniowego).
* Podaj implementację referencyjną na platformie Azure i Azure Media Services platformę.
* Omawianie niektórych tematów dotyczących projektowania i implementacji.

Poniższa tabela zawiera podsumowanie natywnej obsługi technologii DRM na różnych platformach i obsługę EME w różnych przeglądarkach.

| **Klient platforma** | **Natywna platforma DRM** | **EME** |
| --- | --- | --- |
| **Inteligentne telewizory, STBs** | PlayReady, Widevine i/lub inne | Osadzona przeglądarka/EME dla oprogramowania PlayReady i/lub Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 for PlayReady|
| **Urządzenia z systemem Android (telefon, tablet, telewizja)** |Widevine |Chrome dla Widevine |
| **iOS** | FairPlay | Safari for FairPlay (od systemu iOS 11,2) |
| **macOS** | FairPlay | Safari for FairPlay (od przeglądarki Safari 9 + na macOS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Biorąc pod uwagę bieżący stan wdrożenia dla każdego DRM, usługa zazwyczaj chce zaimplementować dwa lub trzy protokołów DRM, aby upewnić się, że wszystkie typy punktów końcowych są zgodne z najlepszymi rozwiązaniami.

Istnieje kompromis między złożonością logiki usługi i złożonością po stronie klienta, aby uzyskać dostęp do określonego poziomu środowiska użytkownika na różnych klientach.

Aby wybrać tę opcję, należy pamiętać o następujących kwestiach:

* Oprogramowanie PlayReady jest natywnie implementowane na każdym urządzeniu z systemem Windows, na niektórych urządzeniach z systemem Android i dostępne za poorednictwem zestawów SDK oprogramowania na praktycznie dowolnej platformie.
* Widevine jest natywnie implementowany w każdym urządzeniu z systemem Android, w przeglądarce Chrome i w niektórych innych urządzeniach. Widevine jest również obsługiwane w przeglądarkach przeglądarki Firefox i Opera.
* FairPlay jest dostępny w systemach iOS, macOS i systemu tvOS.

## <a name="a-reference-design"></a>Projekt referencyjny

W tej części przedstawiono projekt referencyjny, który jest niezależny od do technologii użytych do jego wdrożenia.

Podsystem DRM może zawierać następujące składniki:

* Zarządzanie kluczami
* Pakowanie szyfrowania DRM
* Dostarczanie licencji DRM
* Sprawdzanie uprawnień/kontrola dostępu
* Uwierzytelnianie/Autoryzacja użytkownika
* Aplikacja odtwarzacza
* Pochodzenie/usługa Content Delivery Network (CDN)

Na poniższym diagramie przedstawiono interakcje wysokiego poziomu między składnikami w podsystemie DRM:

![Podsystem DRM z CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Usługa Secure Token Service (STS)** |Azure AD |
| **Przepływ pracy ochrony DRM** |Azure Media Services ochronę dynamiczną |
| **Dostarczanie licencji DRM** |* Media Services dostarczania licencji (PlayReady, Widevine, FairPlay) <br/>* Axinom serwera licencji <br/>* Niestandardowy serwer licencji PlayReady |
| **Źródł** |Azure Media Services punkt końcowy przesyłania strumieniowego |
| **Zarządzanie kluczami** |Niewymagane dla implementacji odwołania |
| **Zarządzanie zawartością** |Aplikacja konsolowa w języku C# |

Innymi słowy, zarówno dostawcy tożsamości, jak i STS są udostępniane przez usługę Azure AD. [Interfejs API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) jest używany dla odtwarzacza. Zarówno Azure Media Services, jak i Azure Media Player obsłużyć CENC za pośrednictwem ŁĄCZNIKa, FairPlay ponad HLS, PlayReady przez bezproblemowe przesyłanie strumieniowe i szyfrowanie AES-128 dla ŁĄCZNIKów, HLS i gładkich.

Na poniższym diagramie przedstawiono ogólną strukturę i przepływ przy użyciu poprzedniego mapowania technologii:

![CENC na Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Aby skonfigurować ochronę zawartości DRM, narzędzie do zarządzania zawartością używa następujących danych wejściowych:

* Otwórz zawartość
* Klucz zawartości z zarządzania kluczami
* Adresy URL pozyskiwania licencji
* Lista informacji z usługi Azure AD, takich jak odbiorcy, wystawcy i oświadczenia tokenów

Oto dane wyjściowe narzędzia do zarządzania zawartością:

* ContentKeyPolicy opisuje szablon licencji DRM dla każdego używanego rodzaju technologii DRM;
* ContentKeyPolicyRestriction opisuje kontrolę dostępu przed wystawieniem licencji DRM
* Streamingpolicy opisuje różne kombinacje protokołu DRM-Encryption Mode-Streaming Protocol-Container format, do przesyłania strumieniowego
* StreamingLocator opisuje klucz zawartości/IV używany do szyfrowania oraz adresy URL przesyłania strumieniowego 

Oto przepływ w czasie wykonywania:

* Po uwierzytelnieniu użytkownika jest generowany token JWT.
* Jedno z oświadczeń zawartych w tokenie JWT jest oświadczeniem grup zawierającym identyfikator obiektu grupy EntitledUserGroup. To zastrzeżenie służy do przekazania kontroli uprawnień.
* Odtwarzacz pobiera manifest klienta zawartości chronionej przez CENC i identyfikuje następujące elementy:
   * Identyfikator klucza.
   * Zawartość jest chroniona przez technologię DRM.
   * Adresy URL pozyskiwania licencji.
* Odtwarzacz wykonuje żądanie pozyskiwania licencji na podstawie obsługiwanej przeglądarki/funkcji DRM. W żądaniu pozyskiwania licencji zostanie również przesłany identyfikator klucza i token JWT. Usługa dostarczania licencji weryfikuje token JWT i oświadczenia zawarte przed wystawianiem wymaganej licencji.

## <a name="implementation"></a>Implementacja
### <a name="implementation-procedures"></a>Procedury implementacji
Implementacja obejmuje następujące kroki:

1. Przygotuj zasoby testowe. Koduj/Pakuj testowe wideo do pofragmentowanych plików MP4 o dużej szybkości transmisji bitów w Media Services. Ten zasób *nie* jest chroniony przez DRM. Ochronę za pomocą technologii DRM wykonuje się później.

2. Utwórz identyfikator klucza i klucz zawartości (opcjonalnie z inicjatora kluczy). W tym przypadku system zarządzania kluczami nie jest wymagany, ponieważ tylko jeden identyfikator klucza i klucz zawartości są wymagane dla kilku zasobów testowych.

3. Użyj interfejsu API Media Services, aby skonfigurować usługi dostarczania licencji na wiele DRM dla elementu zawartości testowej. Jeśli używasz niestandardowych serwerów licencji przez firmę lub dostawców firmy zamiast usług licencjonowania w Media Services, możesz pominąć ten krok. Możesz określić adresy URL pozyskiwania licencji w kroku podczas konfigurowania dostarczania licencji. Interfejs API Media Services jest wymagany do określenia niektórych szczegółowych konfiguracji, takich jak ograniczenia zasad autoryzacji i szablony odpowiedzi licencji dla różnych usług licencjonowania DRM. W tej chwili Azure Portal nie zapewnia interfejsu użytkownika wymaganego dla tej konfiguracji. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](protect-with-drm.md)).

4. Użyj interfejsu API Media Services, aby skonfigurować zasady dostarczania elementów zawartości dla testu. Aby uzyskać informacje na temat poziomu interfejsu API i przykładowy kod, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption (Widevine](protect-with-drm.md)).

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
    | **AES — 128** |Większość nowoczesnych przeglądarek  |Połączyć |Niepowodzenie |

Aby uzyskać informacje na temat sposobu konfigurowania usługi Azure AD dla aplikacji odtwarzacza ASP.NET MVC, zobacz [Integrowanie aplikacji opartej na platformie mvc Azure Media Services Owin z Azure Active Directory i ograniczanie dostarczania kluczy zawartości na podstawie oświadczeń JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie tokenu JWT w Azure Media Services i szyfrowanie dynamiczne](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Aby uzyskać informacje na temat usługi Azure AD:

* Informacje dla deweloperów można znaleźć w [przewodniku dewelopera Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informacje o administratorze można znaleźć w temacie [administrowanie katalogiem dzierżawy usługi Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Niektóre problemy w implementacji

Aby uzyskać pomoc dotyczącą problemów z implementacją, Skorzystaj z poniższych informacji dotyczących rozwiązywania problemów.

* Adres URL wystawcy musi kończyć się znakiem "/". Odbiorcy muszą być IDENTYFIKATORem klienta aplikacji odtwarzacza. Należy również dodać znak "/" na końcu adresu URL wystawcy.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    W [dekoderze JWT](http://jwt.calebb.net/)zobaczysz **AUD** i **ISS**, jak pokazano w tokenie JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Dodaj uprawnienia do aplikacji w usłudze Azure AD na karcie **Konfiguracja** aplikacji. Dla każdej aplikacji, w wersji lokalnej i wdrożonej wymagane są uprawnienia.

    ![Uprawnienia](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

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

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Ponieważ oprócz usługi SWT (ACS) dodano obsługę tokenu JWT (Azure AD), domyślnym ustawieniem TokenType jest TokenType. JWT. W przypadku korzystania z usługi SWT/ACS należy ustawić token na wartość TokenType. SWT.

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

![Niestandardowe konto domeny dzierżawy usługi Azure AD jeden](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Konto domeny Microsoft z kartą inteligentną**: Strona logowania dostosowana przez firmę Microsoft w firmie przy użyciu uwierzytelniania dwuskładnikowego.

![Niestandardowe konto domeny dzierżawy usługi Azure AD dwa](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Konto Microsoft**: strona logowania konto Microsoft dla użytkowników.

![Niestandardowe konto domeny dzierżawy usługi Azure AD trzy](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Korzystanie z rozszerzeń nośników zaszyfrowanych dla oprogramowania PlayReady

W nowoczesnej przeglądarce z rozszerzeniami szyfrowanych multimediów (EME) na potrzeby obsługi rozwiązań PlayReady, takich jak Internet Explorer 11 w Windows 8.1 lub nowszej i przeglądarka Microsoft Edge w systemie Windows 10, PlayReady jest podstawowym mechanizmem DRM dla EME.

![Korzystanie z EME dla oprogramowania PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Ciemny obszar odtwarzacza jest spowodowany tym, że ochrona PlayReady uniemożliwia przechwycenie ekranu chronionego wideo.

Poniższy zrzut ekranu przedstawia wtyczki odtwarzacza i Microsoft Security Essentials (MSE)/EME support:

![Wtyczki odtwarzacza dla oprogramowania PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME w przeglądarce Microsoft Edge i Internet Explorer 11 w systemie Windows 10 umożliwia wywoływanie oprogramowania [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) na urządzeniach z systemem Windows 10, które je obsługują. Usługa PlayReady SL3000 odblokowuje przepływ rozszerzonej zawartości w warstwie Premium (4K, HDR) i nowych modeli dostarczania zawartości (dla zawartości rozszerzonej).

Aby skoncentrować się na urządzeniach z systemem Windows, PlayReady jest jedynym urządzeniem DRM dostępnym na urządzeniach z systemem Windows (PlayReady SL3000). Usługa przesyłania strumieniowego może korzystać z oprogramowania PlayReady za pośrednictwem EME lub za pośrednictwem aplikacji platforma uniwersalna systemu Windows i oferować wyższą jakość wideo przy użyciu oprogramowania PlayReady SL3000 niż inne DRM. Zwykle zawartość do 2K przepływów za pomocą przeglądarki Chrome lub Firefox oraz zawartość do i do 4 KB przepływów za pomocą przeglądarki Microsoft Edge/Internet Explorer 11 lub aplikacji platforma uniwersalna systemu Windows na tym samym urządzeniu. Ta kwota zależy od ustawień i implementacji usługi.

#### <a name="use-eme-for-widevine"></a>Użyj EME dla Widevine

W nowoczesnej przeglądarce z obsługą EME/Widevine, taką jak Chrome 41 + w systemie Windows 10, Windows 8.1, Mac OSX Yosemite i Chrome w systemie Android 4.4.4, firma Google Widevine jest funkcją DRM za EME.

![Użyj EME dla Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nie uniemożliwia przechwycenia ekranu chronionego wideo.

![Wtyczki odtwarzacza dla Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Użyj EME dla FairPlay

Podobnie można testować zawartość chronioną FairPlay w tym odtwarzaczu testowym w programie Safari w systemach macOS i iOS 11,2 i nowszych.

Upewnij się, że umieścisz "FairPlay" jako protectionInfo. Wpisz i umieść w odpowiednim adresie URL certyfikatu aplikacji w ścieżce AC/s (ścieżka certyfikatu aplikacji przesyłania strumieniowego FairPlay).

### <a name="unentitled-users"></a>Nieuprawnieni użytkownicy

Jeśli użytkownik nie jest członkiem grupy "przysługuje Ci użytkownicy", nie zostanie on przekazany do sprawdzenia uprawnień. Usługa licencji z obsługą technologii DRM odmówi, aby wystawić żądaną licencję, jak pokazano. Szczegółowy opis to "nie można pobrać licencji", co jest tak zaprojektowane.

![Nieuprawnieni użytkownicy](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Uruchamianie niestandardowej usługi tokenu zabezpieczającego

W przypadku uruchomienia niestandardowej usługi STS token JWT zostanie wystawiony przez niestandardową usługę STS przy użyciu klucza symetrycznego lub asymetrycznego.

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza symetrycznego (przy użyciu przeglądarki Chrome):

![Niestandardowa usługa STS z kluczem symetrycznym](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Poniższy zrzut ekranu przedstawia scenariusz, który używa klucza asymetrycznego za pośrednictwem certyfikatu x509 (przy użyciu nowoczesnej przeglądarki firmy Microsoft):

![Niestandardowa usługa STS z kluczem asymetrycznym](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

W obu powyższych przypadkach uwierzytelnianie użytkownika pozostaje takie samo. Odbywa się to za pomocą usługi Azure AD. Jedyną różnicą jest to, że JWTs są wystawiane przez niestandardową usługę STS zamiast usługi Azure AD. W przypadku skonfigurowania dynamicznej ochrony CENC ograniczenie usługi dostarczania licencji określa typ JWT, czyli klucz symetryczny lub asymetryczny.
