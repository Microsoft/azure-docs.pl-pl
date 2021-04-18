---
title: End-to-end content protection using Azure AD
description: W tym artykule opisano, jak chronić zawartość za pomocą Azure Media Services i Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599489"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Samouczek: end-to-end content protection using Azure AD (Samouczek: end-to-end content protection using Azure AD ))

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Za pomocą tego samouczka i dostarczonego przykładu odtwarzacza można skonfigurować podsystem ochrony zawartości multimediów typu end-to-end w usługach Azure Media Services (AMS) i Azure Active Directory (AAD) w celu strumieniowego przesyłania zawartości multimedialnej przy użyciu wszystkich obsługiwanych przez usługę AMS formatów DRM/AES-128, protokołów przesyłania strumieniowego, koderów-koderów i kontenerów. Przykład jest wystarczająco ogólny, aby zapewnić bezpieczny dostęp do dowolnego interfejsu API REST chronionego przez uwierzytelnianie OAuth 2 za pośrednictwem przepływu kodu autoryzacji z kluczem dowodu dla wymiany kodu (PKCE). (Azure Media Services dostarczania licencji to tylko jedna z nich). Działa również w przypadku Microsoft Graph API lub dowolnego niestandardowego interfejsu API REST zabezpieczonego za pomocą przepływu kodu autoryzacji OAuth 2. Jest to dokument towarzyszący [przykładoweowi kodowi](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Należy wziąć pod uwagę wymagania dotyczące uwierzytelniania
> * Zrozumienie sposobu działania aplikacji
> * Rejestrowanie aplikacji zasobów zaplecza
> * Rejestrowanie aplikacji klienckiej
> * Konfigurowanie zasad klucza zawartości konta usługi Media Services i zasad przesyłania strumieniowego
> * Konfigurowanie aplikacji odtwarzacza

Jeśli nie masz subskrypcji usługi Azure Media Services, utwórz konto bezpłatnej wersji próbnej platformy [Azure,](https://azure.microsoft.com/free/) a następnie utwórz Media Services próbne.

### <a name="duration"></a>Czas trwania
Ukończenie samouczka powinno zająć około dwóch godzin po przygotowaniu wstępnie wymaganej technologii.

## <a name="prerequisites"></a>Wymagania wstępne

Używane są następujące najnowsze wersje technologii i koncepcje. Zaleca się zapoznanie się z nimi przed rozpoczęciem tego samouczka.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza

Przed rozpoczęciem tego samouczka jest to opcjonalne, ale zalecane, aby zapoznać się z następującymi pojęciami:

* Cyfrowy Rights Management (DRM)
* [Azure Media Services (AMS) w wersji 3](./media-services-overview.md)
* Zasady klucza [zawartości usługi](drm-content-key-policy-concept.md) AMS przy użyciu interfejsu API usługi AMS w wersji 3, Azure Portal lub narzędzia Azure Media Services [Explorer (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Punkty końcowe usługi Azure AD w wersji 2 na [platformie Tożsamości firmy Microsoft](../../active-directory/develop/index.yml)
* Nowoczesne uwierzytelnianie w chmurze, [takie jak OAuth 2.0 i OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Przepływ kodu autoryzacji w protokołu OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) i dlaczego jest wymagany pkce
  * [Uprawnienia delegowane a uprawnienia aplikacji](../../active-directory/develop/developer-glossary.md#permissions)
* [Token JWT,](../../active-directory/develop/access-tokens.md)jego oświadczenia i przechowanie klucza podpisywania (uwzględnione w przykładzie).

### <a name="prerequisite-code-and-installations"></a>Kod wymagań wstępnych i instalacje

* Przykładowy kod. Pobierz [przykładowy kod](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Instalacja Visual Studio Code. Pobierz Visual Studio Code [https://code.visualstudio.com/download](https://code.visualstudio.com/download) tutaj.
* Instalacja Node.js. Pobierz Node.js [https://nodejs.org](https://nodejs.org) tutaj. Do instalacji jest dostarczany program NPM.
* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
* Konto Azure Media Services (AMS).
* @azure/msal-browser Wersja 2.0, jeden z członków rodziny zestawu SDK biblioteki [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) dla różnych platform klienckich
* Najnowsza wersja [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(uwzględniona w przykładzie).
* Poświadczenia SSL od firmy Apple, jeśli chcesz uwzględnić certyfikat FAIRPlay DRM i certyfikat aplikacji hostowany przy użyciu corsu, który jest dostępny za pośrednictwem języka JavaScript po stronie klienta.

> [!IMPORTANT]
> W tym samouczku do utworzenia ograniczeń zasad klucza zawartości jest używany program .NET.  Jeśli nie jesteś deweloperem platformy .NET, ale chcesz spróbować Node.js nawiązać połączenie z programem Azure Media Services, przeczytaj connect [to Media Services v3 API - Node.js](configure-connect-nodejs-howto.md)(Nawiązywanie połączenia z interfejsem API programu Media Services w wersji 3 — Node.js). Dostępny jest również moduł Node.js do automatycznego przechowania kluczy. Zobacz Node.js [modułu passport-ad.](https://github.com/AzureAD/passport-azure-ad)

## <a name="consider-the-authentication-and-authorization-requirements"></a>Weź pod uwagę wymagania dotyczące uwierzytelniania i autoryzacji

W projektowaniu podsystemu przedstawiono kilka wyzwań. Ma on wiele ruchomych części, istnieją ograniczenia aplikacji klienckich i przewłaczanie klucza usługi Azure AD, które odbywa się co sześć tygodni.

Aplikacja Single-Page używana w tym samouczku uwzględnia wyzwania związane z wymaganiami uwierzytelniania i ograniczeniami, które obowiązują. Procesor zdarzeń korzysta z następujących elementów:

* Punkty końcowe usługi Azure AD w wersji 2 jako platforma deweloperów usługi Azure AD (punkty końcowe w wersji 1) zmieniają się na microsoft Identity Platform (punkty końcowe w wersji 2).
* Przepływ kodu autoryzacji, ponieważ niejawny przepływ przyznawania protokołu OAuth 2 jest przestarzały.
* Aplikacja podlegająca następującym ograniczeniom:
    * Klient publiczny nie może ukryć tajnego klienta.  Sam przepływ kodu autoryzacji wymaga ukrycia tajnego kodu klienta, dlatego jest używany przepływ kodu autoryzacji z PKCE.
    * Aplikacja oparta na przeglądarce, która podlega piaskownicy zabezpieczeń przeglądarki (ograniczenie CORS/wstępne).
    * Aplikacja oparta na przeglądarce korzystająca z nowoczesnego języka JavaScript, która podlega ograniczeniom zabezpieczeń języka JavaScript (niestandardowa dostępność nagłówka, identyfikator korelacji).

## <a name="understand-the-subsystem-design"></a>Opis projektu podsystemu

Projekt podsystemu przedstawiono na poniższym diagramie.  Ma trzy warstwy:

* Warstwa back-office (w kolorze czarnym) do konfigurowania zasad klucza zawartości i publikowania zawartości do przesyłania strumieniowego
* Publiczne punkty końcowe (w kolorze niebieskim), które są punktami końcowymi odtwarzacza/klienta zapewniającymi uwierzytelnianie, autoryzację, licencję DRM i zaszyfrowaną zawartość
* Aplikacja Player (jasnoniebieski), która integruje wszystkie składniki i
    * obsługuje uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD.
    * obsługuje access_token z usługi Azure AD.
    * odbiera manifest i zaszyfrowaną zawartość z usługi AMS/CDN.
    * uzyskuje licencję DRM od Azure Media Services.
    * Obsługuje odszyfrowywanie, dekodowanie i wyświetlanie zawartości.

![ekran do analizowania tokenów JWT](media/aad-ams-content-protection/subsystem.svg)

Aby uzyskać więcej informacji na temat podsystemu, przeczytaj Design [of a multi-DRM content protection system with access control](./architecture-design-multi-drm-system.md) (Projektowanie systemu ochrony zawartości z wieloma drmami z kontrolą dostępu).

## <a name="understand-the-single-page-app"></a>Opis aplikacji jednostronicowej

Aplikacja odtwarzacza jest aplikacją jednostronicową (SPA), opracowaną w Visual Studio Code przy użyciu:

* Node.js z ES 6 JavaScript
* @azure/msal-browser 2.0 beta
* Azure Media Player SDK
* Przepływ OAuth 2 względem punktów końcowych usługi Azure AD w wersji 2 (Platforma tożsamości firmy Microsoft)

Aplikacja odtwarzacza SPA kończy następujące akcje:

* Uwierzytelnianie użytkowników dla użytkowników natywnych dla dzierżawy oraz użytkowników-gości z innych dzierżaw usługi AAD lub kont MSA. Użytkownicy mogą logować się za pomocą okna podręcznego przeglądarki lub przekierowania (w przypadku przeglądarek, które nie zezwalają na wyskakujące okienka, takie jak Safari).
* Pozyskiwanie za `access_token` pośrednictwem przepływu kodu autoryzacji za pomocą PKCE.
* Odnawianie `access_token` (tokeny wystawione przez usługi AAD są ważne przez 1 godzinę), dla których `refresh_token` również są nabywane.
* Analizowanie tokenów JWT (zarówno `access_token` i ) w celu `id_token` testowania/inspekcji.
* Pozyskiwanie licencji DRM dla wszystkich trzech drmów lub klucza zawartości AES-128.
* Przesyłanie strumieniowe zawartości w różnych kombinacjach protokołu DRM z protokołem przesyłania strumieniowego a formatem kontenera. Dla każdej kombinacji jest generowany prawidłowy ciąg formatu.
* Odszyfrowywanie, dekodowanie i wyświetlanie.
* Microsoft Graph wywołań interfejsu API na potrzeby rozwiązywania problemów. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Ekran logowania, pozyskiwania tokenu, odnawiania tokenu i wyświetlania tokenu:

 ![Ekran logowania, pozyskiwania tokenu, odnawiania tokenu i wyświetlania tokenu](media/aad-ams-content-protection/token-acquisition.png)

Ekran do analizowania tokenów JWT (access_token lub id_token):

![Zrzut ekranu przedstawiający analizowanie tokenów J W T.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Ekran do testowania zawartości chronionej przy użyciu różnych kombinacji protokołów DRM/AES i protokołów przesyłania strumieniowego w porównaniu z formatem kontenera:

![Zrzut ekranu przedstawiający testowanie zawartości chronionej przy użyciu różnych kombinacji języka D R M lub A E S w porównaniu z protokołami przesyłania strumieniowego a formatem kontenera](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Wybieranie dzierżawy usługi Azure AD

> [!NOTE]
> Od tego dnia zakłada się, że zalogowano się do usługi Azure Portal i masz co najmniej jedną dzierżawę usługi Azure AD.

Wybierz dzierżawę usługi Azure AD do użycia w naszym przykładzie end-to-end. Dostępne są dwie opcje:

* Istniejąca dzierżawa usługi Azure AD. Każda subskrypcja platformy Azure musi mieć jedną dzierżawę usługi Azure AD, ale dzierżawa usługi Azure AD może być używana przez wiele subskrypcji platformy Azure.
* Nowa dzierżawa usługi Azure AD, która *nie jest używana* przez żadne subskrypcje platformy Azure. Jeśli wybierzesz nową opcję dzierżawy, konto usługi multimediów i przykładowa aplikacja odtwarzacza muszą znajdować się w subskrypcji platformy Azure, która używa oddzielnej dzierżawy usługi Azure AD. Zapewnia to pewien elastyczność. Możesz na przykład użyć własnej dzierżawy usługi Azure AD, ale również konta usługi multimediów klienta w subskrypcji platformy Azure klienta.

## <a name="register-the-backend-resource-app"></a>Rejestrowanie aplikacji zasobów zaplecza

1. Przejdź do wybranej lub utworzonej dzierżawy usługi Azure AD.
1. Wybierz **Azure Active Directory** z menu.
1. Wybierz **Rejestracje aplikacji** z menu.
1. Kliknij **pozycję + Nowa rejestracja.**
1. Nadaj aplikacji *nazwę LicenseDeliveryResource2* (gdzie 2 oznacza punkty końcowe usługi AAD w wersji 2).
1. Wybierz **pozycję Konta tylko w tym katalogu organizacyjnym ([ twoja nazwa *dzierżawy*] — pojedyncza dzierżawa).** Jeśli chcesz włączyć dostęp do wielu dzierżaw, wybierz jedną z pozostałych opcji wielodostępnych.
1. Parametr **URI przekierowania** jest opcjonalny i można go później zmienić.
1. Kliknij pozycję **Zarejestruj**. Zostanie Rejestracje aplikacji widok.
1. Wybierz **pozycję Manifest** z menu. Zostanie wyświetlony widok manifestu.
1. Zmień wartość na `accessTokenAcceptedVersion` *2* (bez cudzysłowów).
1. Zmień wartość na `groupMembershipClaims` *"SecurityGroup"* (z cudzysłowami).
1. Kliknij pozycję **Zapisz**.
1. Wybierz z menu **pozycję Uwidocznij** interfejs API. Zostanie wyświetlony widok Dodaj zakres. (Platforma Azure udostępnia identyfikator URI identyfikatora aplikacji, ale jeśli chcesz go zmienić, możesz go edytować w polu Identyfikator URI identyfikatora aplikacji).
1. Kliknij **przycisk Zapisz i kontynuuj.** Widok zmieni się. Dla każdego z ustawień w kolumnie Ustawienie w poniższej tabeli wprowadź wartość w kolumnie Wartość, a następnie kliknij **pozycję Dodaj zakres**.

| Ustawienie | Wartość | Opis |
| ------- | ----- | ----------- |
| Nazwa zakresu | *Drm. License.Delivery* | Sposób, w jaki zakres będzie wyświetlany podczas żądania dostępu do tego interfejsu API, oraz w tokenach dostępu, gdy zakres zostanie przyznany aplikacji klienckiej. Ta aplikacja musi być unikatowa. Najlepszym rozwiązaniem jest użycie "resource.operation.constraint" jako wzorca do wygenerowania nazwy. |
| Kto może wyrazić zgodę? | *Administratorzy i użytkownicy* | Określa, czy użytkownicy mogą wyrazić zgodę na ten zakres w katalogach, w których włączono zgodę użytkownika. |
| Nazwa wyświetlana zgody administratora | *Dostarczanie licencji DRM* | Zakres, który zostanie wywołany na ekranie zgody, gdy administratorzy zgodą na ten zakres. |
| Opis zgody administratora** | *Zakres zasobów zaplecza dostarczania licencji DRM* | Szczegółowy opis zakresu, który jest wyświetlany, gdy administratorzy dzierżawy rozszerzają zakres na ekranie wyrażania zgody. |
| Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika | *Drm. License.Delivery* | Zakres, który zostanie wywołany na ekranie wyrażania zgody, gdy użytkownik wyraża zgodę na ten zakres. |
| Opis na potrzeby wyrażenia zgody przez użytkownika | *Zakres zasobów zaplecza dostarczania licencji DRM* | Jest to szczegółowy opis zakresu, który jest wyświetlany, gdy użytkownicy rozszerzają zakres na ekranie wyrażania zgody. |
| Stan | *Włączono* | Określa, czy ten zakres jest dostępny dla klientów do żądania. Ustaw ją na wartość "Wyłączone" dla zakresów, które nie mają być widoczne dla klientów. Można usunąć tylko wyłączone zakresy. Zalecamy odczekanie co najmniej tygodnia po wyłączeniu zakresu przed jego usunięciem, aby upewnić się, że żaden klient nie nadal z niego korzysta. |

## <a name="register-the-client-app"></a>Rejestrowanie aplikacji klienckiej

1. Przejdź do wybranej lub utworzonej dzierżawy usługi Azure AD.
1. Wybierz **Azure Active Directory** z menu.
1. Wybierz **Rejestracje aplikacji** z menu.
1. Kliknij **pozycję + Nowa rejestracja.**
1. Nadaj aplikacji klienckiej nazwę, na przykład *AMS AAD Content Protection*.
1. Wybierz **pozycję Konta tylko w tym katalogu organizacyjnym ([ twoja nazwa *dzierżawy*] — pojedyncza dzierżawa).** Jeśli chcesz włączyć dostęp do wielu dzierżaw, wybierz jedną z pozostałych opcji wielodostępnych.
1. Parametr **URI przekierowania** jest opcjonalny i można go później zmienić.
1. Kliknij pozycję **Zarejestruj**.
1. Wybierz **pozycję Uprawnienia interfejsu API** z menu.
1. Kliknij pozycję **+ Dodaj uprawnienie**. Zostanie otwarty widok Żądanie uprawnień interfejsu API.
1. Kliknij **kartę Mój interfejs API** i wybierz aplikację *LicenseDeliveryResource2* utworzoną w ostatniej sekcji.
1. Kliknij strzałkę DRM i sprawdź *drm. Uprawnienie License.Delivery.*
1. Kliknij pozycję **Dodaj uprawnienia**. Zostanie zamknięte widok Dodaj uprawnienia.
1. Wybierz **pozycję Manifest** z menu. Zostanie wyświetlony widok manifestu.
1. Znajdź i dodaj następujące pary wartości do `replyUrlsWithType` atrybutu :

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > W tym momencie nie masz jeszcze adresu URL aplikacji odtwarzacza.  Jeśli uruchamiasz aplikację z serwera internetowego hosta lokalnego, możesz użyć tylko pary wartości localhost. Po wdrożeniu aplikacji odtwarzacza możesz dodać tutaj wpis z wdrożonym adresem URL.  Jeśli o tym zapomnisz, zobaczysz komunikat o błędzie podczas logowania w usłudze Azure AD.

1. Kliknij pozycję **Zapisz**.
1. Na koniec, aby sprawdzić, czy konfiguracja jest poprawna, wybierz pozycję **Uwierzytelnianie.**  Zostanie wyświetlony widok Uwierzytelnianie. Aplikacja kliency zostanie wymieniona jako aplikacja jednostronicowa (SPA), zostanie wyświetlone przekierowanie URI, a typ udzielenia to Przepływ kodu autoryzacji z PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Konfigurowanie zasad klucza Media Services kont kont i zasad przesyłania strumieniowego

**W tej sekcji założono, że jesteś deweloperem platformy .NET i wiesz, jak korzystać z interfejsu API usługi AMS w wersji 3.**

> [!NOTE]
> Od tego momentu nie można używać klucza Azure Portal konfiguracji zasad klucza konta usługi Media Services, ponieważ nie obsługuje on używania klucza podpisywania tokenu asymetrycznego z konfiguracją OpenID-Config.  Konfiguracja musi obsługiwać przenokinie kluczy usługi Azure AD, ponieważ token wystawiony przez usługę Azure AD jest podpisany przy użyciu klucza asymetrycznego, a klucz jest przenoszowany co sześć tygodni. W związku z tym w tym samouczku jest używany program .NET i interfejs API usługi AMS w wersji 3.

Mają zastosowanie [konfiguracje zasad klucza zawartości](drm-content-key-policy-concept.md) i przesyłania [strumieniowego](stream-streaming-policy-concept.md) dla szyfrowania DRM i AES-128.  Zmień element `ContentKeyPolicyRestriction` w zasadach klucza zawartości.

Poniżej znajduje się kod .NET do tworzenia ograniczeń zasad klucza zawartości.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Zmień `ida_AADOpenIdDiscoveryDocument` wartości `ida_audience` , i w `ida_issuer` powyższym kodzie. Aby znaleźć wartości tych elementów w Azure Portal:

1. Wybierz używaną wcześniej dzierżawę usługi AAD, kliknij pozycję **Rejestracje aplikacji** w menu, a następnie kliknij link **Punkty** końcowe.
1. Wybierz i skopiuj wartość pola dokumentu metadanych **OpenIdConnect** i wklej ją do kodu jako `ida_AADOpenIdDiscoveryDocument` wartość.
1. Wartość `ida_audience` to identyfikator aplikacji (klienta) zarejestrowanej aplikacji *LicenseDeliveryResource2.*
1. Wartość `ida_issuer` to adres URL `https://login.microsoftonline.com/[tenant_id]/v2.0` . Zastąp *[tenant_id*] identyfikatorem dzierżawy.

## <a name="set-up-the-sample-player-app"></a>Konfigurowanie przykładowej aplikacji odtwarzacza

Jeśli jeszcze tego nie zrobiono, sklonuj lub pobierz aplikację z źródłowego źródła: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Dostępne są dwie opcje konfiguracji aplikacji odtwarzacza:

* Minimalne dostosowanie (zastępowanie tylko niektórych stałych wartości ciągu, takich jak client_id, tenant_id i adres URL przesyłania strumieniowego), ale należy użyć Visual Studio Code i Node.js.
* Jeśli wolisz używać innego środowiska IDE i platformy internetowej, takiej jak ASP.NET Core, możesz umieścić pliki stron internetowych, pliki JavaScript i plik CSS w projekcie, ponieważ sama aplikacja odtwarzacza nie używa żadnego kodu po stronie serwera.

### <a name="option-1"></a>Opcja 1

1. Uruchom program Visual Studio Code.
1. Aby otworzyć projekt, kliknij pozycję Plik -> Otwórz folder -> przejdź do folderu i wybierz folder nadrzędnypackage.js *pliku.*
1. Otwórz plik JavaScript *public/javascript/constants.js*.
1. Zastąp `OAUTH2_CONST.CLIENT_ID` znakiem `client_id` swojej zarejestrowanej aplikacji klienckiej w dzierżawie usługi AAD.  Możesz znaleźć sekcję Przegląd zarejestrowanej aplikacji w `client_id` Azure Portal. Uwaga: jest to identyfikator klienta, a nie identyfikator obiektu.
1. Zastąp `OAUTH2_CONST.TENANT_ID` przez `tenant_id` adres dzierżawy usługi Azure AD. Możesz je `tenant_id` znaleźć, klikając Azure Active Directory menu. Ekran tenant_id zostanie wyświetlony w sekcji Przegląd.
1. Zastąp `OAUTH2_CONST.SCOPE` wartość zakresem dodanym do zarejestrowanej aplikacji klienckiej. Zakres można znaleźć, przechodząc do zarejestrowanej aplikacji klienckiej z menu **Rejestracje aplikacji** a następnie wybierając aplikację kliencyjną:
    1. Wybierz aplikację kliency, kliknij menu **Uprawnienia interfejsu API,** a następnie wybierz zakres *DRM. License.Delivery w* ramach uprawnienia interfejsu API *LicenseDeliveryResource2.* Uprawnienie powinno mieć format taki jak *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM. License.Delivery*. **Ważne:** zachowaj spację przed `offline_access` elementem w . `OAUTH2_CONST.SCOPE`
1. Zastąp dwa stałe ciągi dla , `AMS_CONST` jak pokazano poniżej. Jeden z nich to chroniony adres URL przesyłania strumieniowego zasobu testowego, a drugi to adres URL certyfikatu aplikacji SSL, jeśli chcesz dołączyć przypadek testowy FairPlay. W przeciwnym razie możesz pozostawić ją bez tego dla . `AMS_CONST.APP_CERT_URL` Następnie kliknij pozycję **Zapisz.**

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Aby przetestować lokalnie:

1. W Visual Studio Code (VSC) wybierz pozycję **Widok** z menu głównego, a następnie **pozycję Terminal.**
1. Jeśli jeszcze nie zainstalowano polecenia npm, w wierszu polecenia wprowadź `npm install` polecenie .
1. Wprowadź `npm start` w wierszu polecenia. (Jeśli npm nie uruchamia się, spróbuj zmienić katalog na `npmweb` , wprowadzając polecenie w `cd npmweb` wierszu polecenia).
1. Użyj przeglądarki, aby przejść do witryny `http://localhost:3000` .

W zależności od wybranej przeglądarki wybierz poprawną kombinację protokołów DRM/AES i Streaming Protocol i Container Format do testowania po zalogowaniu `access_token` (pozyskanie). W przypadku testowania w przeglądarce Safari w systemie macOS zaznacz opcję Interfejs API przekierowania, ponieważ przeglądarka Safari nie zezwala na wyskakujące okienka. Większość innych przeglądarek zezwala na opcje wyskakujących okienek i przekierowania.

### <a name="option-2"></a>Opcja 2

Jeśli planujesz używać innej platformy IDE/sieci Web i/lub serwera internetowego, takiego jak usługi IIS uruchomione na komputerze dewelopera, skopiuj następujące pliki do nowego katalogu na lokalnym serwerze sieci Web. Poniższe ścieżki znajdują się w pobranym kodzie.

* *views/index.ejs* (zmień sufiks na .html)
* *views/jwt.ejs* (zmień sufiks na .html)
* *views/info.ejs* (zmień sufiks na html)
* *public/** (pliki JavaScript, CSS, obrazy, jak pokazano poniżej)

1. Skopiuj pliki znalezione w *folderze widoku* do katalogu głównego nowego katalogu.
1. Skopiuj *foldery* znalezione w *folderze publicznym* do katalogu głównego nowego katalogu.
1. Zmień rozszerzenia plików `.ejs` na `.html` . (Nie jest używana żadna zmienna po stronie serwera, więc można ją bezpiecznie zmienić).
1. Otwórz *index.html w* programie VSC (lub innym edytorze kodu) i zmień ścieżki i tak, aby odzwierciedlały, gdzie znajdują się `<script>` `<link>` pliki.  Jeśli poprzednie kroki zostały już opisane, wystarczy usunąć ścieżkę `\` w ścieżce .  Na przykład, `<script type="text/javascript" src="/javascript/constants.js"></script>` staje się `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Dostosuj stałe w pliku *javascript/constants.js* jak w opcji 1.

## <a name="common-customer-scenarios"></a>Typowe scenariusze klientów

Teraz, po ukończeniu samouczka i pracy z podsystemem, możesz spróbować zmodyfikować go dla następujących scenariuszy klienta:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC) do dostarczania licencji za pośrednictwem członkostwa w grupie usługi Azure AD

Do tej pory system umożliwia dowolnemu użytkownikowi, który może się zalogować, uzyskiwanie prawidłowej licencji i odtwarzanie chronionej zawartości.

Częstym wymaganiem klienta jest, aby podzbiór uwierzytelnionych użytkowników mógł oglądać zawartość, a inni nie są na przykład klientem, który oferuje subskrypcje Podstawowa i Premium dla zawartości wideo. Klienci, którzy zapłacili za podstawową subskrypcję, nie powinni mieć możliwości obejrzenia zawartości, która wymaga subskrypcji Premium. Poniżej przedstawiono dodatkowe kroki wymagane do spełnienia tego wymagania:

#### <a name="set-up-the-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

1. Skonfiguruj dwa konta w dzierżawie. Mogą one mieć nazwy *premium_user* i *basic_user*;
1. Utwórz grupę użytkowników i nadaj jej *nazwę PremiumGroup.*
1. Dodaj *premium_user* do grupy *PremiumGroup* jako członka, ale nie dodawaj basic_user *do* grupy.
1. Zanotuj **identyfikator obiektu** grupy *PremiumGroup.*

#### <a name="set-up-the-media-services-account"></a>Konfigurowanie konta Media Services magazynu

Zmodyfikuj (jak pokazano w powyższej sekcji w sekcji Konfiguracja na koncie usługi Media Service), dodając oświadczenie o nazwie groups , gdzie jego wartością jest identyfikator `ContentKeyPolicyRestriction`  `ida_EntitledGroupObjectId` obiektu *PremiumGroup:*

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

Oświadczenie *grup* jest członkiem zestawu roszczeń [z ograniczeniami w](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) usłudze Azure AD.

#### <a name="test"></a>Testowanie

1. Zaloguj się przy użyciu *premium_user* konta. Powinno być możliwe odtwarzanie chronionej zawartości.
1. Zaloguj się przy użyciu *basic_user* konta. Powinien zostać wyświetlany komunikat o błędzie informujący, że wideo jest zaszyfrowane, ale nie ma klucza do jego odszyfrowania. Jeśli wyświetlasz zdarzenia, błędy i pliki do pobrania z menu rozwijanego w dolnej części nakładki diagnostyki odtwarzacza, komunikat o błędzie powinien wskazywać niepowodzenie uzyskania licencji z powodu braku wartości oświadczenia dla oświadczenia grup w JWT wystawionego przez punkt końcowy tokenu usługi Azure AD.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Obsługa wielu kont usługi multimediów (w wielu subskrypcjach)

Klient może mieć wiele kont usługi multimediów w ramach jednej lub wielu subskrypcji platformy Azure. Na przykład klient może mieć jedno konto usługi multimediów jako podstawowe w środowisku produkcyjnym, drugie jako pomocnicze/zapasowe, a drugie na potrzeby tworzenia/testowania.

Musisz tylko upewnić się, że podczas tworzenia we wszystkich kontach usługi multimediów używasz tego samego zestawu parametrów, który został użyty w sekcji (Konfiguracja na koncie usługi Media `ContentKeyPolicyRestriction` Service).

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Obsługa klienta, jego dostawców i/lub podmiotów zależnych w wielu dzierżawach usługi AAD

Jako użytkownicy rozwiązania podmioty zależne klienta, dostawcy/partnerzy mogą znajdować się w różnych dzierżawach usługi AAD, takich jak `mycustomer.com` `mysubsidiary.com` , i `myparther.com` . Chociaż to rozwiązanie jest zbudowane na jednej konkretnej dzierżawie usługi AAD, takiej jak , można ją udostępnić użytkownikom `mycustomer.com` z innych dzierżaw.

W przypadku tego rozwiązania dodaj użytkownika z usługi jako `mycustomer.com` `mypartner.com` użytkownika-gościa do `mycustomer.com` dzierżawy. Upewnij `mypartner.com` się, że użytkownik aktywuje konto gościa. Konto gościa może pochodzić z innej dzierżawy usługi AAD lub `outlook.com` konta.

Zauważ, że użytkownicy-goście z usługi , po aktywowaniu usługi w programie , są nadal uwierzytelniani za pośrednictwem ich własnej/oryginalnej dzierżawy usługi `mypartner.com` `mycustomer.com` AAD, , ale obiekt jest `mypartner.com` `access_token` wystawiony przez . `mycustomer.com`

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Obsługa dzierżawy/subskrypcji klienta przy użyciu konfiguracji w subskrypcji/dzierżawie

Możesz użyć konfiguracji, aby przetestować chronioną zawartość na koncie/subskrypcji usługi multimediów klienta. Należy ją skonfigurować przy użyciu dzierżawy usługi Azure AD i konta usługi multimediów w tej samej subskrypcji. Konto usługi multimediów klienta będzie w jego subskrypcji platformy Azure z własną dzierżawą usługi Azure AD.

1. Dodaj konto klienta do dzierżawy jako konto gościa.
1. We współpracy z klientem przygotuj chronioną zawartość na koncie usługi multimediów klienta, podając trzy parametry wymienione w sekcji Konfiguracja na koncie usługi Media Service.

Klient może następnie przejść do konfiguracji, zalogować się przy użyciu konta gościa i przetestować własną chronioną zawartość. Możesz również zalogować się przy użyciu własnego konta i przetestować zawartość klienta.

Przykładowe rozwiązanie można skonfigurować w dzierżawie firmy Microsoft z subskrypcją firmy Microsoft lub dzierżawie niestandardowej z subskrypcją firmy Microsoft. Wystąpienie usługi Azure Media Service może pochodzić z innej subskrypcji ze swoją dzierżawą.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

> [!WARNING]
> Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń zasoby utworzone podczas pracy z tym samouczkiem. W przeciwnym razie zostanie naliczona opłata za nie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: szyfrowanie zawartości](drm-encrypt-content-how-to.md)
