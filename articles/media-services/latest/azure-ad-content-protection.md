---
title: Kompleksowa ochrona zawartości przy użyciu usługi Azure AD
description: W tym artykule przedstawiono sposób ochrony zawartości przy użyciu Azure Media Services i Azure Active Directory
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
ms.openlocfilehash: 9415d66c49992bc31f773dec908a861f1126e714
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92427201"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Samouczek: Kompleksowa ochrona zawartości przy użyciu usługi Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Korzystając z tego samouczka i dostarczonego przykładu odtwarzacza, można skonfigurować kompleksowy podsystem ochrony zawartości multimedialnej na Azure Media Services (AMS) i Azure Active Directory (AAD) do przesyłania strumieniowego zawartości multimedialnej ze wszystkimi obsługiwanymi protokołami DRM/AES-128, przesyłania strumieniowego, koderem-dekoder i formatami kontenerów. Przykład jest wystarczająco ogólny, aby zabezpieczyć dostęp do dowolnego interfejsu API REST chronionego przez uwierzytelnianie OAuth 2 za pomocą przepływu kodu autoryzacji z kluczem testowym dla wymiany kodu (PKCE). (Usługa dostarczania licencji Azure Media Services jest tylko jedną z nich). Działa również w przypadku interfejsu API Microsoft Graph lub dowolnego niestandardowego interfejsu API REST zabezpieczonego za pomocą przepływu kodu autoryzacji OAuth 2. Jest to dokument towarzyszący do [przykładowego kodu](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Uwzględnij wymagania dotyczące uwierzytelniania
> * Informacje o działaniu aplikacji
> * Rejestrowanie aplikacji zasobów zaplecza
> * Rejestrowanie aplikacji klienckiej
> * Konfigurowanie zasad kluczy zawartości konta usługi Media Services i zasad przesyłania strumieniowego
> * Konfigurowanie aplikacji odtwarzacza

Jeśli nie masz subskrypcji Azure Media Services, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) platformy Azure, a następnie utwórz konto Media Services.

### <a name="duration"></a>Czas trwania
Ukończenie tego samouczka powinno potrwać około dwóch godzin po przygotowaniu wstępnie wymaganej technologii.

## <a name="prerequisites"></a>Wymagania wstępne

Używane są następujące najnowsze wersje i koncepcje technologii. Zalecamy zapoznanie się z nimi przed rozpoczęciem pracy z tym samouczkiem.

### <a name="prerequisite-knowledge"></a>Wymagana wiedza

Jest to opcjonalne, ale zalecane jest zapoznanie się z następującymi pojęciami przed rozpoczęciem pracy z tym samouczkiem:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md) usługi AMS przy użyciu interfejsu API usługi AMS v3, Azure Portal lub [Azure Media Services Explorer (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Punkty końcowe usługi Azure AD V2 na [platformie tożsamości firmy Microsoft](../../active-directory/develop/index.yml)
* Nowoczesne uwierzytelnianie w chmurze, takie jak [OAuth 2,0 i OpenID Connect Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Przepływ kodu autoryzacji w OAuth 2,0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) i dlaczego PKCE jest wymagany
  * [Uprawnienie do delegowania uprawnień aplikacji](../../active-directory/develop/developer-glossary.md#permissions)
* [Token JWT](../../active-directory/develop/access-tokens.md), jego oświadczenia i Przerzucanie klucza podpisywania (uwzględnione w przykładzie).

### <a name="prerequisite-code-and-installations"></a>Wstępnie wymagany kod i instalacje

* Przykładowy kod. Pobierz [przykładowy kod](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Instalacja Visual Studio Code. Pobierz Visual Studio Code tym miejscu [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* Instalacja Node.js. Pobierz Node.js tym miejscu [https://nodejs.org](https://nodejs.org) . NPM jest dostarczany z instalacją.
* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
* Konto Azure Media Services (AMS).
* @azure/msal-browser Wersja 2.0 — jeden z członków rodziny SDK [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) dla różnych platform klienckich
* Najnowsza wersja [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(uwzględnionych w przykładach).
* Poświadczenia FPS od firmy Apple, jeśli chcesz dołączyć FairPlay DRM i certyfikat aplikacji hostowanej za pomocą mechanizmu CORS, który jest dostępny za pośrednictwem JavaScript po stronie klienta.

> [!IMPORTANT]
> Ten samouczek używa platformy .NET do tworzenia ograniczenia zasad klucza zawartości.  Jeśli nie jesteś deweloperem platformy .NET, ale chcesz wypróbować Node.js, aby nawiązać połączenie z Azure Media Services, Przeczytaj [link Connect to Media Services v3 API-Node.js](configure-connect-nodejs-howto.md). Dostępny jest również moduł Node.js, który umożliwia automatyczne obsłudze przerzucania kluczy, zobacz Node.js [module paszport-AD](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Weź pod uwagę wymagania dotyczące uwierzytelniania i autoryzacji

Podczas projektowania podsystemu prezentowane są kilka wyzwań. Ma wiele przenoszonych części, istnieją ograniczenia aplikacji klienta i przerzucanie kluczy usługi Azure AD, które występuje co sześć tygodni.

Aplikacja Single-Page (SPA) użyta w tym samouczku uwzględnia wyzwania związane z wymaganiami dotyczącymi uwierzytelniania i poniższymi ograniczeniami. Procesor zdarzeń korzysta z następujących elementów:

* Punkty końcowe usługi Azure AD v2 jako platforma dewelopera usługi Azure AD (punkty końcowe v1) zmieniają się na platformę tożsamości firmy Microsoft (punkty końcowe w wersji 2).
* Przepływ kodu autoryzacji, ponieważ niejawny przepływ uwierzytelniania OAuth 2 został uznany za przestarzały.
* Aplikacja, która podlega następującym ograniczeniom:
    * Klient publiczny nie może ukryć klucza tajnego klienta.  Przepływ kodu autoryzacji wymaga ukrycia klucza tajnego klienta, więc jest używany przepływ kodu autoryzacji z PKCE.
    * Aplikacja oparta na przeglądarce, która jest objęta piaskownicą zabezpieczeń przeglądarki (ograniczeniem CORS/Inspekcja wstępna).
    * Aplikacja oparta na przeglądarce korzystająca z nowoczesnego języka JavaScript, która podlega ograniczeniom zabezpieczeń języka JavaScript (niestandardowa dostępność nagłówka, identyfikator korelacji).

## <a name="understand-the-subsystem-design"></a>Opis projektowania podsystemu

Projekt podsystemu jest przedstawiony na poniższym diagramie.  Ma trzy warstwy:

* Warstwa zaplecza (w czerni) służąca do konfigurowania zasad kluczy zawartości i publikowania zawartości na potrzeby przesyłania strumieniowego
* Publiczne punkty końcowe (niebieskie), które są punktami końcowymi w odtwarzaczu i kliencie, zapewniające uwierzytelnianie, autoryzację, licencję DRM i zaszyfrowaną zawartość
* Aplikacja odtwarzacza (w jasnym kolorze niebieskim), która integruje wszystkie składniki i
    * obsługuje uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD.
    * obsługuje access_token pozyskiwania z usługi Azure AD.
    * odbiera manifest i zaszyfrowaną zawartość z usługi AMS/CDN.
    * uzyskuje licencję DRM z Azure Media Services.
    * obsługuje odszyfrowywanie, dekodowanie i wyświetlanie zawartości.

![ekran służący do analizowania tokenów JWT](media/aad-ams-content-protection/subsystem.svg)

Zapoznaj się z artykułem [Projektowanie systemu ochrony zawartości z wieloma drmmi przy użyciu funkcji kontroli dostępu](./design-multi-drm-system-with-access-control.md) , aby uzyskać więcej szczegółowych informacji o podsystemie.

## <a name="understand-the-single-page-app"></a>Zrozumienie aplikacji jednostronicowej

Aplikacja odtwarzacza jest aplikacją jednostronicową (SPA) opracowaną w Visual Studio Code przy użyciu:

* Node.js przy użyciu programu ES 6 JavaScript
* @azure/msal-browser 2,0 beta
* Zestaw SDK Azure Media Player
* Przepływ OAuth 2 do punktów końcowych usługi Azure AD v2 (platforma tożsamości firmy Microsoft)

Aplikacja odtwarzacz SPA wykonuje następujące czynności:

* Uwierzytelnianie użytkowników w trybie macierzystym dla dzierżawy i użytkowników-Gości z innych dzierżawców usługi AAD lub kont MSA. Użytkownicy mogą zdecydować się na zalogowanie się za pomocą okna podręcznego lub przekierowania przeglądarki (w przypadku przeglądarek, które nie umożliwiają wyskakujących okienek, takich jak Safari)
* Uzyskiwanie `access_token` przez przepływ kodu autoryzacji z PKCE.
* Odnowienie `access_token` (tokeny wystawione przez usługi AAD) jest ważne przez 1 godzinę, dla którego `refresh_token` jest również nabyte.
* Analizowanie tokenów JWT (zarówno `access_token` i `id_token` ) do testowania/inspekcji.
* Pozyskiwanie licencji DRM dla wszystkich trzech kluczy zawartości protokołów DRM lub AES-128.
* Przesyłanie strumieniowe zawartości pod różnymi kombinacjami protokołu DRM vs Streaming format. Dla każdej kombinacji jest generowany prawidłowy ciąg formatu.
* Odszyfrowywanie, dekodowanie i wyświetlanie.
* Microsoft Graph wywołań interfejsu API w celu rozwiązywania problemów. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Ekran logowania, pozyskiwania tokenów, odnawiania tokenów i wyświetlania tokenów:

 ![Ekran logowania, pozyskiwania tokenów, odnawiania tokenów i wyświetlania tokenów](media/aad-ams-content-protection/token-acquisition.png)

Ekran służący do analizowania tokenów JWT (access_token lub id_token):

![Zrzut ekranu przedstawiający analizowanie tokenów J W T.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Ekran służący do testowania zawartości chronionej przy użyciu różnych kombinacji protokołów DRM/AES vs streaming i format kontenera:

![Zrzut ekranu, który pokazuje Testowanie zawartości chronionej przy użyciu różnych kombinacji D R M lub E S zamiast protokołów przesyłania strumieniowego w formacie kontenera](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Wybierz dzierżawę usługi Azure AD

> [!NOTE]
> Z tego miejsca zakłada się, że użytkownik zalogował się do Azure Portal i ma co najmniej jedną dzierżawę usługi Azure AD.

Wybierz dzierżawę usługi Azure AD, która ma być używana na potrzeby kompleksowego przykładu. Dostępne są dwie opcje:

* Istniejąca dzierżawa usługi Azure AD. Każda subskrypcja platformy Azure musi mieć jedną dzierżawę usługi Azure AD, ale dzierżawę usługi Azure AD można używać przez wiele subskrypcji platformy Azure.
* Nowa dzierżawa usługi Azure AD, która *nie* jest używana przez żadną subskrypcję platformy Azure. Jeśli wybierzesz opcję Nowy dzierżawca, konto usługi multimediów i Przykładowa aplikacja odtwarzacza muszą znajdować się w subskrypcji platformy Azure korzystającej z oddzielnej dzierżawy usługi Azure AD. Zapewnia to elastyczność. Na przykład możesz użyć własnej dzierżawy usługi Azure AD, ale również konta usługi multimedialnej klienta w ramach subskrypcji platformy Azure klienta.

## <a name="register-the-backend-resource-app"></a>Rejestrowanie aplikacji zasobów zaplecza

1. Przejdź do wybranej lub utworzonej dzierżawy usługi Azure AD.
1. Wybierz **Azure Active Directory** z menu.
1. Wybierz **rejestracje aplikacji** z menu.
1. Kliknij pozycję **+ Nowa rejestracja**.
1. Nazwij aplikację *LicenseDeliveryResource2* (gdzie 2 wskazuje punkty końcowe usługi AAD v2).
1. Wybierz **tylko konta w tym katalogu organizacji ([*Nazwa dzierżawy*) — tylko jeden dzierżawca)**. Jeśli chcesz włączyć dostęp do wielu dzierżawców, wybierz jedną z innych opcji wielodostępnych.
1. **Identyfikator URI przekierowania** jest opcjonalny i można go później zmienić.
1. Kliknij pozycję **Zarejestruj**. Zostanie wyświetlony widok Rejestracje aplikacji.
1. Z menu wybierz pozycję **manifest** . Zostanie wyświetlony widok manifestu.
1. Zmień wartość na `accessTokenAcceptedVersion` *2* (brak cudzysłowów).
1. Zmień wartość `groupMembershipClaims` na *"Security* " (z cudzysłowami).
1. Kliknij pozycję **Zapisz**.
1. Wybierz opcję **Uwidacznianie interfejsu API** z menu. Zostanie wyświetlony widok Dodaj zakres. (Platforma Azure udostępnia identyfikator URI aplikacji, ale jeśli chcesz ją zmienić, możesz edytować w polu Identyfikator URI aplikacji).
1. Kliknij przycisk **Zapisz i Kontynuuj**. Widok zostanie zmieniony. Dla każdego ustawienia w kolumnie ustawienia w poniższej tabeli wprowadź wartość w kolumnie wartość, a następnie kliknij pozycję **Dodaj zakres**.

| Ustawienie | Wartość | Opis |
| ------- | ----- | ----------- |
| Nazwa zakresu | *Zastosowanie. License. Delivery* | Sposób wyświetlania zakresu podczas żądania dostępu do tego interfejsu API i w tokenach dostępu, gdy zakres został przyznany do aplikacji klienckiej. Ta aplikacja musi być unikatowa w ramach tej aplikacji. Najlepszym rozwiązaniem jest użycie elementu "Resource. Operation. Constraint" jako wzorca w celu wygenerowania nazwy. |
| Kto może wyrazić zgodę? | *Administratorzy i użytkownicy* | Określa, czy użytkownicy mogą wyrazić zgodę na ten zakres w katalogach, w których jest włączona zgoda użytkownika. |
| Nazwa wyświetlana zgody administratora | *Dostarczanie licencji DRM* | Zakres, który zostanie wywołany na ekranie wyrażania zgody, gdy Administratorzy wyrażają zgodę na ten zakres. |
| Opis zgody administratora * * | *Zakres zasobów zaplecza dostarczania licencji DRM* | Szczegółowy opis zakresu, który jest wyświetlany, gdy administratorzy dzierżawy rozszerzają zakres na ekranie wyrażania zgody. |
| Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika | *Zastosowanie. License. Delivery* | Zakres, który zostanie wywołany na ekranie wyrażania zgody, gdy użytkownicy wyrażają zgodę na ten zakres. |
| Opis na potrzeby wyrażenia zgody przez użytkownika | *Zakres zasobów zaplecza dostarczania licencji DRM* | Jest to szczegółowy opis zakresu, który jest wyświetlany, gdy użytkownicy rozszerzają zakres na ekranie wyrażania zgody. |
| Stan | *Włączono* | Określa, czy ten zakres jest dostępny dla klientów do żądania. Ustaw na wartość "wyłączone" dla zakresów, które nie mają być widoczne dla klientów. Można usuwać tylko wyłączone zakresy, a firma Microsoft zaleca oczekiwanie co najmniej tygodnia od momentu wyłączenia zakresu przed jego usunięciem, aby upewnić się, że żaden klient nadal go używa. |

## <a name="register-the-client-app"></a>Rejestrowanie aplikacji klienckiej

1. Przejdź do wybranej lub utworzonej dzierżawy usługi Azure AD.
1. Wybierz **Azure Active Directory** z menu.
1. Wybierz **rejestracje aplikacji** z menu.
1. Kliknij pozycję **+ Nowa rejestracja**.
1. Nadaj aplikacji klienckiej nazwę, na przykład usługi *AMS w usłudze AAD Content Protection*.
1. Wybierz **tylko konta w tym katalogu organizacji ([*Nazwa dzierżawy*) — tylko jeden dzierżawca)**. Jeśli chcesz włączyć dostęp do wielu dzierżawców, wybierz jedną z innych opcji wielodostępnych.
1. **Identyfikator URI przekierowania** jest opcjonalny i można go później zmienić.
1. Kliknij pozycję **Zarejestruj**.
1. Wybierz pozycję **uprawnienia interfejsu API** z menu.
1. Kliknij pozycję **+ Dodaj uprawnienie**. Zostanie otwarty widok uprawnienia interfejsu API żądania.
1. Kliknij kartę **My API** i wybierz aplikację *LicenseDeliveryResource2* utworzoną w ostatniej sekcji.
1. Kliknij strzałkę DRM i sprawdź *technologię DRM. Uprawnienie License. Delivery* .
1. Kliknij pozycję **Dodaj uprawnienia**. Widok Dodaj uprawnienia zostanie zamknięty.
1. Z menu wybierz pozycję **manifest** . Zostanie wyświetlony widok manifestu.
1. Znajdź i Dodaj następujące pary wartości do `replyUrlsWithType` atrybutu:

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
    > W tym momencie nie masz jeszcze adresu URL aplikacji odtwarzacza.  Jeśli aplikacja jest uruchamiana z poziomu serwera sieci weblocalhost, można użyć tylko pary wartość localhost. Po wdrożeniu aplikacji odtwarzacza możesz dodać tutaj wpis przy użyciu wdrożonego adresu URL.  Jeśli zapomnisz to zrobić, zobaczysz komunikat o błędzie w temacie Logowanie do usługi Azure AD.

1. Kliknij pozycję **Zapisz**.
1. Na koniec upewnij się, że konfiguracja jest poprawna, a następnie wybierz pozycję **uwierzytelnianie**.  Zostanie wyświetlony widok uwierzytelnianie. Aplikacja kliencka zostanie wyświetlona jako aplikacja jednostronicowa (SPA), zostanie wyświetlony identyfikator URI przekierowania, a typ dotacji będzie przepływem kodu autoryzacji z PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Konfigurowanie zasad klucza zawartości konta Media Services i zasad przesyłania strumieniowego

**W tej sekcji założono, że jesteś deweloperem platformy .NET i znasz interfejs API usługi AMS v3.**

> [!NOTE]
> Zgodnie z tym pisaniem nie można użyć Azure Portal dla Instalatora zasad klucza konta usługi Media Services, ponieważ nie obsługuje on używania klucza podpisywania tokenu asymetrycznego z OpenID Connect-config.  Instalator musi obsługiwać Przerzucanie kluczy usługi Azure AD, ponieważ wystawiony token usługi Azure AD jest podpisany przez klucz asymetryczny, a klucze są przenoszone co sześć tygodni. Dlatego w tym samouczku jest stosowany interfejs API platformy .NET i usługi AMS v3.

Konfiguracja zasad dotyczących [kluczy zawartości](content-key-policy-concept.md) i [zasad przesyłania STRUMIENIOWEGO](streaming-policy-concept.md) dla technologii DRM i AES-128.  Zmień wartość `ContentKeyPolicyRestriction` w obszarze zasady klucza zawartości.

Poniżej znajduje się kod .NET służący do tworzenia ograniczenia zasad klucza zawartości.

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

Zmień `ida_AADOpenIdDiscoveryDocument` wartości, `ida_audience` i `ida_issuer` w powyższym kodzie. Aby znaleźć wartości tych elementów w Azure Portal:

1. Wybierz wcześniej używaną dzierżawę usługi AAD, kliknij pozycję **rejestracje aplikacji** w menu, a następnie kliknij łącze **punkty końcowe** .
1. Wybierz i skopiuj wartość pola **dokumentu metadanych OpenIdConnect** i wklej je do kodu jako `ida_AADOpenIdDiscoveryDocument` wartość.
1. `ida_audience`Wartość jest identyfikatorem aplikacji (klienta) zarejestrowanej aplikacji *LicenseDeliveryResource2*.
1. `ida_issuer`Wartość jest adresem URL `https://login.microsoftonline.com/[tenant_id]/v2.0` . Zastąp ciąg [*tenant_id*] identyfikatorem dzierżawy.

## <a name="set-up-the-sample-player-app"></a>Konfigurowanie przykładowej aplikacji odtwarzacza

Jeśli jeszcze tego nie zrobiono, Sklonuj lub Pobierz aplikację z repozytorium źródłowego: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Dostępne są dwie opcje konfigurowania aplikacji odtwarzacza:

* Minimalne dostosowanie (zastępowanie tylko niektórych wartości ciągów stałych, takich jak client_id, tenant_id i adres URL przesyłania strumieniowego), ale należy użyć Visual Studio Code i Node.js.
* Jeśli wolisz używać innej platformy IDE i sieci Web, takiej jak ASP.NET Core, możesz umieścić pliki strony sieci Web, pliki JavaScript i plik CSS w projekcie, ponieważ sama aplikacja odtwarzacza nie używa kodu po stronie serwera.

### <a name="option-1"></a>Opcja 1

1. Uruchom program Visual Studio Code.
1. Aby otworzyć projekt, kliknij pozycję plik-> Otwórz folder — > Przeglądaj i wybierz folder nadrzędny *package.jsna* pliku.
1. Otwórz plik języka JavaScript *Public/JavaScript/constants.js*.
1. Zastąp `OAUTH2_CONST.CLIENT_ID` wartość `client_id` zarejestrowanej aplikacji klienckiej w dzierżawie usługi AAD.  Możesz znaleźć w `client_id` sekcji Omówienie zarejestrowanej aplikacji w Azure Portal. Uwaga: to jest identyfikator klienta, a nie identyfikator obiektu.
1. Zastąp `OAUTH2_CONST.TENANT_ID` wartość przez `tenant_id` dzierżawę usługi Azure AD. Możesz znaleźć `tenant_id` , klikając Azure Active Directory menu. Tenant_id pojawi się w sekcji Przegląd.
1. Zamień na `OAUTH2_CONST.SCOPE` zakres, który został dodany do zarejestrowanej aplikacji klienckiej. Zakres można znaleźć, przechodząc do zarejestrowanej aplikacji klienckiej z menu **rejestracje aplikacji** a następnie wybierając aplikację kliencką:
    1. Wybierz aplikację kliencką, kliknij menu **uprawnień interfejsu API** , a następnie wybierz zakres *DRM. License. Delivery* w ramach uprawnień interfejsu API *LicenseDeliveryResource2*. Uprawnienie powinno mieć format podobny do *API://df4ed433-dbf0-4da6-B328-e1fe05786db5/DRM. License. Delivery*. **Ważne**: Zachowaj miejsce przed `offline_access` w `OAUTH2_CONST.SCOPE` .
1. Zastąp dwa ciągi stałe `AMS_CONST` , tak jak pokazano poniżej. Jeden z nich to chroniony adres URL przesyłania strumieniowego zasobu testowego, a drugi to adres URL certyfikatu aplikacji FPS, jeśli chcesz uwzględnić przypadek testowy FairPlay. W przeciwnym razie można pozostawić ją w stanie od `AMS_CONST.APP_CERT_URL` . Następnie kliknij przycisk **Zapisz**.

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

1. W Visual Studio Code (VSC) wybierz opcję **Widok** z menu głównego, a następnie pozycję **Terminal**.
1. Jeśli jeszcze nie zainstalowano npm, wpisz w wierszu polecenia `npm install` .
1. Wprowadź `npm start` w wierszu polecenia. (Jeśli npm nie jest uruchomiona, spróbuj zmienić katalog na `npmweb` , wprowadzając `cd npmweb` polecenie w wierszu polecenia).
1. Użyj przeglądarki, aby przejść do `http://localhost:3000` .

W zależności od używanej przeglądarki Wybierz poprawną kombinację protokołu DRM/AES vs Streaming format, który ma zostać przetestowany po zalogowaniu się `access_token` . Jeśli testujesz się w przeglądarce Safari w witrynie macOS, sprawdź opcję przekierowania API, ponieważ przeglądarka Safari nie zezwala na wyskakujące okienka. Większość innych przeglądarek zezwala na okna podręczne i opcje przekierowania.

### <a name="option-2"></a>Opcja 2

Jeśli planujesz użyć innej platformy IDE/sieci Web i/lub serwera Web, takiego jak usługi IIS działające na komputerze deweloperskim, Skopiuj następujące pliki do nowego katalogu na lokalnym serwerze WebServer. Poniższe ścieżki znajdują się tam, gdzie znajdziesz je w pobranym kodzie.

* *widoki/index. EJS* (Zmień sufiks na. html)
* *widoki/JWT. EJS* (Zmień sufiks na. html)
* *widoki/info. EJS* (Zmień sufiks na HTML)
* *Public/** (pliki JavaScript, CSS, obrazy, jak pokazano poniżej)

1. Skopiuj pliki znajdujące się w folderze *widoku* do katalogu głównego nowego katalogu.
1. Skopiuj *foldery* znajdujące się w folderze *publicznym* do katalogu głównego nowego katalogu.
1. Zmień rozszerzenia `.ejs` plików na `.html` . (Nie jest używana żadna zmienna po stronie serwera, aby można było ją bezpiecznie zmienić).
1. Otwórz *index.html* w VSC (lub innym edytorze kodu) i Zmień `<script>` ścieżki i `<link>` tak, aby odzwierciedlały miejsce, w którym znajdują się pliki.  Jeśli wykonano poprzednie kroki, wystarczy usunąć tylko `\` w ścieżce.  Na przykład, `<script type="text/javascript" src="/javascript/constants.js"></script>` staje się `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Dostosuj stałe w pliku *JavaScript/constants.js* jak w opcji 1.

## <a name="common-customer-scenarios"></a>Typowe scenariusze klientów

Po ukończeniu samouczka i podsystemu działającego można spróbować zmodyfikować go w następujących scenariuszach klientów:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Kontrola dostępu oparta na rolach (Azure RBAC) na potrzeby dostarczania licencji za pośrednictwem członkostwa w grupie usługi Azure AD

Do tej pory system zezwoli każdemu użytkownikowi, który może się zalogować, aby uzyskać prawidłową licencję i odtworzyć chronioną zawartość.

Jest to typowy wymóg klienta, którego podzbiór uwierzytelnionych użytkowników może oglądać zawartość, a na przykład klient, który oferuje subskrypcje Basic i Premium dla swojej zawartości wideo. Klienci, którzy zapłacili za subskrypcję podstawową, nie powinni być w stanie obserwować zawartości, która wymaga subskrypcji Premium. Poniżej znajdują się dodatkowe czynności wymagane do spełnienia tego wymagania:

#### <a name="set-up-the-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

1. Skonfiguruj dwa konta w dzierżawie. Mogą one mieć nazwę *premium_user* i *basic_user*;
1. Utwórz grupę użytkowników i Wywołaj ją w *warstwie Premium*.
1. Dodaj *premium_user* do grupy *Premium* jako element członkowski, ale nie dodawaj do niej *basic_user* .
1. Zanotuj **Identyfikator obiektu** w usłudze *Premium*.

#### <a name="set-up-the-media-services-account"></a>Skonfiguruj konto Media Services

Zmodyfikuj `ContentKeyPolicyRestriction` (jak pokazano w powyższej sekcji na koncie usługi Media Service), dodając roszczeń o nazwie *grupy*, gdzie `ida_EntitledGroupObjectId` ma identyfikator obiektu w grupie *Premium* jako jego wartość:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

Deklaracja *grup* jest członkiem [ograniczonego zestawu roszczeń](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) w usłudze Azure AD.

#### <a name="test"></a>Testowanie

1. Zaloguj się przy użyciu konta *premium_user* . Powinno być możliwe odtwarzanie chronionej zawartości.
1. Zaloguj się przy użyciu konta *basic_user* . Powinien zostać wyświetlony komunikat o błędzie informujący, że wideo jest zaszyfrowane, ale nie ma klucza do odszyfrowania. Jeśli wyświetlasz zdarzenia, błędy i pliki do pobrania przy użyciu listy rozwijanej u dołu nakładki diagnostyki odtwarzacza, komunikat o błędzie powinien wskazywać niepowodzenie pozyskiwania licencji ze względu na brakującą wartość żądania dla grup w tokenie JWT wystawionym przez punkt końcowy tokenu usługi Azure AD.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Obsługa wielu kont usługi Media Service (między wieloma subskrypcjami)

Klient może mieć wiele kont usługi multimediów w ramach jednej lub wielu subskrypcji platformy Azure. Na przykład klient może mieć jedno konto usługi Media Service jako podstawowe, inne jako pomocnicze/zapasowe, a drugie dla tworzenia i testowania.

Wystarczy upewnić się, że używasz tego samego zestawu parametrów, które zostały użyte w sekcji (Instalator na koncie usługi Media Service) w celu utworzenia `ContentKeyPolicyRestriction` konta usługi na wszystkich nośnikach.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Obsługa klienta, jego dostawców i/lub podmiotów zależnych w wielu dzierżawcach usługi AAD

Użytkownicy rozwiązania, zależni od dostawców, dostawcy lub partnerzy mogą znajdować się w różnych dzierżawach usługi AAD, takich jak `mycustomer.com` , `mysubsidiary.com` i `myparther.com` . Chociaż to rozwiązanie jest oparte na pojedynczej dzierżawie usługi AAD, takiej jak `mycustomer.com` , można ją zapewnić użytkownikom z innych dzierżawców.

Przy użyciu `mycustomer.com` dla tego rozwiązania Dodaj użytkownika z `mypartner.com` roli użytkownika-gościa do `mycustomer.com` dzierżawy. Upewnij się, że `mypartner.com` użytkownik aktywuje konto gościa. Konto gościa może należeć do innej dzierżawy usługi AAD lub `outlook.com` konta.

Należy zauważyć, że użytkownicy-Goście z `mypartner.com` , po aktywowaniu w `mycustomer.com` usłudze, są nadal uwierzytelniani za pośrednictwem własnej/oryginalnej dzierżawy usługi AAD, `mypartner.com` ale `access_token` jest wystawiony przez `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Obsługa dzierżawy/subskrypcji klienta z konfiguracją w ramach subskrypcji/dzierżawy

Możesz użyć swojej instalacji do testowania zawartości chronionej na koncie/subskrypcji klienta usługi multimediów. Skonfigurujesz go przy użyciu dzierżawy usługi Azure AD i konta usługi Media w tej samej subskrypcji. Konto usługi multimedialnej klienta ma swoją subskrypcję platformy Azure z własną dzierżawą usługi Azure AD.

1. Dodaj konto klienta do swojej dzierżawy jako konto gościa.
1. Skontaktuj się z klientem w celu przygotowania chronionej zawartości na koncie usługi multimedialnej klienta, podając trzy parametry wymienione w sekcji Instalator w ramach konta usługi Media Service.

Klient może następnie przejść do konfiguracji, zalogować się przy użyciu konta gościa i przetestować własną zawartość chronioną. Możesz również zalogować się przy użyciu własnego konta i przetestować zawartość klienta.

Twoje przykładowe rozwiązanie można skonfigurować w dzierżawie firmy Microsoft z subskrypcją firmy Microsoft lub niestandardową dzierżawą z subskrypcją firmy Microsoft. Wystąpienie usługi Azure Media Service może należeć do innej subskrypcji z dzierżawcą.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

> [!WARNING]
> Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby utworzone w ramach tego samouczka. W przeciwnym razie zostanie naliczona opłata za te opłaty.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: szyfrowanie zawartości](encrypt-content-quickstart.md)
