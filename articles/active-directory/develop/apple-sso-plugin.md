---
title: Wtyczka logowania jednokrotnego Microsoft Enterprise dla urządzeń firmy Apple
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej na temat Azure Active Directory wtyczki logowania jednokrotnego dla urządzeń z systemem iOS, iPadOS i macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 24a538686e101d40daba008f30a72ffc5078047a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674537"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Wtyczka Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)

>[!IMPORTANT]
> Ta funkcja [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Wtyczka Logowanie jednokrotne w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple* zapewnia Logowanie jednokrotne (SSO) dla kont usługi Azure Active Directory (Azure AD) w systemach MacOS, iOS i iPadOS we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego [w przedsiębiorstwie](https://developer.apple.com/documentation/authenticationservices) firmy Apple. Wtyczka zapewnia Logowanie jednokrotne w przypadku starych aplikacji, od których może zależeć firma, ale które jeszcze nie obsługują najnowszych bibliotek lub protokołów tożsamości. Firma Microsoft pracowała ściśle z firmą Apple, aby opracować Tę wtyczkę w celu zwiększenia użyteczności aplikacji przy jednoczesnym udostępnieniu najlepszej ochrony.

Wtyczka Logowanie jednokrotne w przedsiębiorstwie jest obecnie wbudowaną funkcją następujących aplikacji:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [Portal firmy](/mem/intune/apps/apps-company-portal-macos): macOS

## <a name="features"></a>Funkcje

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple oferuje następujące korzyści:

- Zapewnia Logowanie jednokrotne dla kont usługi Azure AD we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego dla przedsiębiorstwa firmy Apple.
- Można ją włączyć za pomocą dowolnego rozwiązania do zarządzania urządzeniami przenośnymi (MDM).
- Rozszerza Logowanie jednokrotne do aplikacji, które nie używają jeszcze bibliotek Microsoft Identity platform.
- Rozszerza Logowanie jednokrotne do aplikacji, które używają protokołu OAuth 2, OpenID Connect Connect i protokołu SAML.

## <a name="requirements"></a>Wymagania

Aby użyć wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple:

- Urządzenie musi *obsługiwać* i mieć zainstalowaną aplikację, która ma wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple:
  - iOS 13,0 i nowsze: [aplikacja Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13,0 i nowsze: [aplikacja Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 i nowsze: [aplikacja Intune — portal firmy](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Urządzenie musi być *zarejestrowane w usłudze MDM*, na przykład za pomocą Microsoft Intune.
- Aby włączyć wtyczkę logowania jednokrotnego dla przedsiębiorstwa, należy przeprowadzić konfigurację *wypychaną do urządzenia* . Firma Apple wymaga tego ograniczenia zabezpieczeń.

wymagania dotyczące systemu iOS:
- na urządzeniu musi być zainstalowany system iOS w wersji 13,0 lub nowszej.
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej ta aplikacja jest aplikacją [Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


wymagania macOS:
- na urządzeniu musi być zainstalowany macOS 10,15 lub nowszy. 
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej ta aplikacja jest aplikacją [Intune — portal firmy](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in"></a>Włącz wtyczkę logowania jednokrotnego

Skorzystaj z poniższych informacji, aby włączyć wtyczkę logowania jednokrotnego za pomocą zarządzania urządzeniami przenośnymi.
### <a name="microsoft-intune-configuration"></a>Konfiguracja Microsoft Intune

Jeśli używasz Microsoft Intune jako usługi MDM, możesz użyć wbudowanych ustawień profilu konfiguracji, aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft:

1. Skonfiguruj ustawienia [rozszerzenia aplikacji SSO](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) dla profilu konfiguracji. 
1. Jeśli profil nie został już przypisany, [Przypisz profil do grupy użytkowników lub urządzeń](/mem/intune/configuration/device-profile-assign).

Ustawienia profilu, które włączają wtyczkę logowania jednokrotnego, są automatycznie stosowane do urządzeń grupy podczas następnego zaewidencjonowania urządzenia w usłudze Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Konfiguracja ręczna dla innych usług MDM

Jeśli nie korzystasz z usługi Intune do zarządzania urządzeniami przenośnymi, użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple.

Ustawienia systemu iOS:

- **Identyfikator rozszerzenia**: `com.microsoft.azureauthenticator.ssoextension`
- **Identyfikator zespołu**: to pole nie jest wymagane w przypadku systemu iOS.

Ustawienia systemu macOS:

- **Identyfikator rozszerzenia**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Identyfikator zespołu**: `UBF8T346G9`

Typowe ustawienia:

- **Typ**: redirect
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>Więcej opcji konfiguracji
Możesz dodać więcej opcji konfiguracji, aby zwiększyć możliwości rejestracji jednokrotnej w innych aplikacjach.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Włącz logowanie jednokrotne dla aplikacji, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft

Wtyczka Logowanie jednokrotne umożliwia każdej aplikacji uczestnictwo w usłudze logowania jednokrotnego, nawet jeśli nie została opracowana za pomocą zestawu Microsoft SDK, takiego jak Microsoft Authentication Library (MSAL).

Wtyczka logowania jednokrotnego jest instalowana automatycznie przez urządzenia z:
* Pobrano aplikację Authenticator w systemie iOS lub iPadOS lub pobrano aplikację Intune — Portal firmy na macOS.
* Zarejestrowano swoje urządzenie w organizacji. 

Twoja organizacja prawdopodobnie używa aplikacji Authenticator do scenariuszy, takich jak uwierzytelnianie wieloskładnikowe (MFA), uwierzytelnianie bezhaseł i dostęp warunkowy. Za pomocą dostawcy MDM można włączyć wtyczkę logowania jednokrotnego dla aplikacji. Firma Microsoft ułatwia konfigurowanie wtyczki wewnątrz programu Microsoft Endpoint Manager w usłudze Intune. Dozwolonych służy do konfigurowania tych aplikacji do korzystania z wtyczki logowania jednokrotnego.

>[!IMPORTANT]
> Wtyczka Microsoft Enterprise SSO jest obsługiwana tylko w przypadku aplikacji korzystających z natywnych technologii sieci firmy Apple lub widoków WebViews. Nie obsługuje aplikacji, które dostarczają własne implementacje warstwy sieciowej.  

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla aplikacji, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft.

Aby udostępnić listę konkretnych aplikacji, użyj następujących parametrów:

- **Klucz**: `AppAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej.
- **Przykład**: `com.contoso.workapp, com.contoso.travelapp`

Aby podać listę prefiksów, użyj następujących parametrów:
- **Klucz**: `AppPrefixAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista prefiksów identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej. Ten parametr umożliwia wszystkim aplikacjom, które zaczynają się od określonego prefiksu, uczestnictwo w rejestracji jednokrotnej.
- **Przykład**: `com.contoso., com.fabrikam.`

[Wysłane aplikacje](./application-consent-experience.md) , które administrator zarządzania urządzeniami przenośnymi mogą uczestniczyć w rejestracji jednokrotnej, można w trybie dyskretnym uzyskać token dla użytkownika końcowego. Dodaj tylko zaufane aplikacje do dozwolonych. 

>[!NOTE]
> Nie musisz dodawać aplikacji, które używają MSAL lub ASWebAuthenticationSession, do listy aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej. Te aplikacje są domyślnie włączone. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Znajdowanie identyfikatorów pakietów aplikacji na urządzeniach z systemem iOS

Firma Apple nie zapewnia łatwego sposobu uzyskiwania identyfikatorów pakietów ze sklepu z aplikacjami. Najprostszym sposobem uzyskania identyfikatorów pakietów aplikacji, które mają być używane na potrzeby logowania jednokrotnego, jest poproszenie swojego dostawcy lub dewelopera aplikacji. Jeśli ta opcja jest niedostępna, można użyć konfiguracji MDM, aby znaleźć identyfikatory pakietów: 

1. Tymczasowo Włącz następującą flagę w konfiguracji MDM:

    - **Klucz**: `admin_debug_mode_enabled`
    - **Typ**: `Integer`
    - **Wartość**: 1 lub 0
1. Gdy ta flaga jest włączona, zaloguj się do aplikacji systemu iOS na urządzeniu, dla którego chcesz znać identyfikator pakietu. 
1. W aplikacji Authenticator wybierz pozycję **Pomoc**  >  **Wyślij dzienniki**  >  **Wyświetl dzienniki**. 
1. W pliku dziennika znajdź następujący wiersz: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Ten wiersz powinien przechwycić wszystkie identyfikatory pakietów aplikacji, które są widoczne dla rozszerzenia rejestracji jednokrotnej. 

Użyj identyfikatorów pakietów, aby skonfigurować Logowanie jednokrotne dla aplikacji. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Zezwalaj użytkownikom na logowanie się z nieznanych aplikacji i przeglądarki Safari

Domyślnie wtyczka Microsoft Enterprise SSO umożliwia logowanie jednokrotne do autoryzowanych aplikacji tylko wtedy, gdy użytkownik zalogował się z aplikacji korzystającej z biblioteki platformy tożsamości firmy Microsoft, takiej jak MSAL lub biblioteka uwierzytelniania Azure Active Directory (ADAL). Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft może również uzyskać udostępnione poświadczenie, gdy jest wywoływana przez inną aplikację, która korzysta z biblioteki platformy tożsamości firmy Microsoft podczas uzyskiwania nowego tokenu.

Po włączeniu `browser_sso_interaction_enabled` flagi aplikacje, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft, mogą wykonać początkowe uruchamianie i uzyskać udostępnione poświadczenia. Przeglądarka Safari może również wykonać początkowe uruchamianie i uzyskać udostępnione poświadczenie. 

Jeśli wtyczka Microsoft Enterprise SSO nie ma jeszcze udostępnionego poświadczenia, spróbuje je pobrać za każdym razem, gdy zażądano logowania z adresu URL usługi Azure AD w przeglądarce Safari, ASWebAuthenticationSession, SafariViewController lub innej dozwolonej aplikacji natywnej. 

Użyj tych parametrów, aby włączyć flagę: 

- **Klucz**: `browser_sso_interaction_enabled`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

macOS wymaga tego ustawienia, aby zapewnić spójne środowisko dla wszystkich aplikacji. systemy iOS i iPadOS nie wymagają tego ustawienia, ponieważ większość aplikacji korzysta z aplikacji Authenticator do logowania. Jednak zalecamy włączenie tego ustawienia, ponieważ jeśli niektóre aplikacje nie używają aplikacji Authenticator w systemie iOS lub iPadOS, ta flaga poprawi środowisko pracy. Ustawienie jest domyślnie wyłączone.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Wyłącz monitowanie o usługę MFA podczas początkowego ładowania

Domyślnie wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft zawsze wyświetla użytkownikowi informacje o usłudze MFA podczas początkowego ładowania i podczas pobierania poświadczeń udostępnionych. Użytkownik jest monitowany o uwierzytelnianie MFA, nawet jeśli nie jest to wymagane dla aplikacji, która została otwarta przez użytkownika. Takie zachowanie umożliwia łatwe korzystanie z udostępnionego poświadczenia między wszystkimi innymi aplikacjami bez konieczności monitowania użytkownika o to, czy uwierzytelnianie wieloskładnikowe jest wymagane później. Ze względu na to, że użytkownik otrzymuje mniej pełnych wskazówek, ta konfiguracja jest ogólnie dobra.

Włączenie `browser_sso_disable_mfa` powoduje wyłączenie usługi MFA podczas początkowego uruchamiania oraz pobranie poświadczeń udostępnionych. W takim przypadku użytkownik jest monitowany tylko wtedy, gdy aplikacja lub zasób wymaga uwierzytelniania MFA. 

Aby włączyć flagę, użyj następujących parametrów:

- **Klucz**: `browser_sso_disable_mfa`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zalecamy pozostawienie tej flagi jako wyłączonej, ponieważ skraca liczbę monitów o zalogowanie się użytkownika. Jeśli Twoja organizacja rzadko używa usługi MFA, możesz włączyć flagę. Jednak zalecamy użycie usługi MFA częściej. Z tego powodu flaga jest domyślnie wyłączona.

#### <a name="disable-oauth-2-application-prompts"></a>Wyłącz wyświetlanie wierszy aplikacji OAuth 2

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft zapewnia Logowanie jednokrotne, dołączając poświadczenia udostępnione do żądań sieci, które pochodzą z dozwolonych aplikacji. Niektóre aplikacje OAuth 2 mogą jednak nieprawidłowo wymuszać wyświetlanie przez użytkownika końcowego pożądanych w warstwie protokołu. Jeśli zobaczysz ten problem, zobaczysz również, że udostępnione poświadczenia są ignorowane dla tych aplikacji. Użytkownik zostanie poproszony o zalogowanie się, nawet jeśli wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft działa dla innych aplikacji.  

Włączenie `disable_explicit_app_prompt` flagi ogranicza możliwość działania natywnych aplikacji i aplikacji sieci Web w celu wymuszenia monitu użytkownika końcowego o warstwę protokołu i obejścia logowania JEDNOkrotnego. Aby włączyć flagę, użyj następujących parametrów:

- **Klucz**: `disable_explicit_app_prompt`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zalecamy włączenie tej flagi, aby zapewnić spójne środowisko dla wszystkich aplikacji. Jest on domyślnie wyłączony. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Włącz logowanie jednokrotne za pomocą plików cookie dla określonej aplikacji

Niektóre aplikacje mogą być niezgodne z rozszerzeniem rejestracji jednokrotnej. W przypadku aplikacji, które mają zaawansowane ustawienia sieciowe, mogą wystąpić nieoczekiwane problemy podczas włączania logowania jednokrotnego. Na przykład może zostać wyświetlony komunikat o błędzie informujący o tym, że żądanie sieci zostało anulowane lub przerwane. 

Jeśli masz problemy z logowaniem się przy użyciu metody opisanej w sekcji [aplikacje, które nie używają MSAL](#applications-that-dont-use-msal) , wypróbuj alternatywną konfigurację. Użyj tych parametrów, aby skonfigurować wtyczkę:

- **Klucz**: `AppCookieSSOAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista prefiksów identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej. Wszystkie aplikacje, które zaczynają się od prefiksów na liście, będą mogły uczestniczyć w rejestracji jednokrotnej.
- **Przykład**: `com.contoso.myapp1, com.fabrikam.myapp2`

Aplikacje włączone dla logowania jednokrotnego przy użyciu tej konfiguracji należy dodać do obu `AppCookieSSOAllowList` i `AppPrefixAllowList` .

Wypróbuj tę konfigurację tylko dla aplikacji, które mają nieoczekiwane błędy logowania. 

#### <a name="use-intune-for-simplified-configuration"></a>Konfiguracja uproszczona przy użyciu usługi Intune

Usługa Intune może być używana w ramach usługi MDM, aby ułatwić konfigurację wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft. Na przykład możesz użyć usługi Intune, aby włączyć wtyczkę i dodać stare aplikacje do dozwolonych, aby umożliwić im Logowanie jednokrotne. 

Aby uzyskać więcej informacji, zobacz [dokumentację konfiguracyjną usługi Intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Korzystanie z wtyczki logowania jednokrotnego w aplikacji

Usługa [MSAL dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple. Jest to zalecany sposób dodawania obsługi wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft. Zapewnia to pełną możliwość korzystania z platformy tożsamości firmy Microsoft.

Jeśli tworzysz aplikację dla scenariuszy teraźniejszości-Worker, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md) w celu uzyskania informacji o instalacji.

## <a name="understand-how-the-sso-plug-in-works"></a>Informacje o tym, jak działa wtyczka logowania jednokrotnego

Wtyczka Microsoft Enterprise SSO jest zależna od [platformy Apple Enterprise SSO](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Dostawcy tożsamości dołączenia do struktury mogą przechwycić ruch sieciowy dla swoich domen i zwiększyć lub zmienić sposób obsługi tych żądań. Na przykład wtyczka logowania jednokrotnego może pokazywać więcej interfejsów użytkownika, aby bezpiecznie zbierać poświadczenia użytkownika końcowego, wymagać uwierzytelniania wieloskładnikowego lub dyskretnie dostarczać tokeny do aplikacji.

Natywne aplikacje mogą również implementować operacje niestandardowe i komunikować się bezpośrednio z wtyczką logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [konferencji dla deweloperów na całym świecie 2019 od firmy Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Aplikacje korzystające z MSAL

Usługa [MSAL dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 i nowszych obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple dla kont służbowych. 

Nie jest potrzebna jakakolwiek konfiguracja specjalna, jeśli wykonano [wszystkie zalecane kroki](./quickstart-v2-ios.md) i użyto domyślnego [formatu identyfikatora URI przekierowania](./redirect-uris-ios.md). Na urządzeniach z wtyczką logowania jednokrotnego program MSAL automatycznie wywoła ją dla wszystkich żądań tokenów interaktywnych i dyskretnych. Wywołuje to również na potrzeby operacji wyliczania kont i usuwania kont. Ponieważ MSAL implementuje natywny protokół typu plug-in logowania jednokrotnego, który opiera się na operacjach niestandardowych, ta konfiguracja zapewnia użytkownikom końcowym bezproblemowe środowisko natywne. 

Jeśli wtyczka logowania jednokrotnego nie jest włączona przez rozwiązanie MDM, ale aplikacja Microsoft Authenticator jest obecna na urządzeniu, MSAL zamiast tego używa aplikacji Authenticator dla wszystkich żądań tokenów interaktywnych. Wtyczka logowania jednokrotnego udostępnia Logowanie jednokrotne za pomocą aplikacji Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplikacje, które nie używają MSAL

Aplikacje, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft, takiej jak MSAL, mogą nadal mieć rejestrację jednokrotną, jeśli administrator doda te aplikacje do dozwolonych. 

Nie trzeba zmieniać kodu w tych aplikacjach tak długo, jak są spełnione następujące warunki:

- Aplikacja używa Platform firmy Apple do uruchamiania żądań sieciowych. Te struktury obejmują na przykład [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) i [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- Aplikacja używa protokołów standardowych do komunikowania się z usługą Azure AD. Protokoły te obejmują na przykład uwierzytelnianie OAuth 2, SAML i WS-Federation.
- Aplikacja nie zbiera nazw użytkowników w postaci zwykłego tekstu i haseł w natywnym interfejsie użytkownika.

W takim przypadku Logowanie jednokrotne jest udostępniane, gdy aplikacja tworzy żądanie sieciowe i otwiera przeglądarkę internetową w celu podpisania użytkownika w programie. Gdy użytkownik zostanie przekierowany do adresu URL logowania usługi Azure AD, wtyczka logowania jednokrotnego weryfikuje adres URL i sprawdza poświadczenia logowania jednokrotnego dla tego adresu URL. Jeśli odnajdzie poświadczenia, wtyczka SSO przekaże ją do usługi Azure AD, która autoryzuje aplikację do ukończenia żądania sieci bez monitowania użytkownika o wprowadzenie poświadczeń. Ponadto jeśli urządzenie jest znane w usłudze Azure AD, wtyczka logowania jednokrotnego przekazuje certyfikat urządzenia, aby spełniał kontrolę dostępu warunkowego opartego na urządzeniach. 

Aby można było obsługiwać Logowanie jednokrotne dla aplikacji innych niż MSAL, wtyczka Logowanie jednokrotne implementuje protokół podobny do wtyczki przeglądarki systemu Windows opisanej w artykule [co to jest podstawowy token odświeżania?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

W porównaniu z aplikacjami opartymi na MSAL, wtyczka logowania jednokrotnego działa w sposób bardziej niewidoczny dla aplikacji innych niż MSAL. Integruje się z istniejącym interfejsem logowania w przeglądarce udostępnianym przez aplikacje. 

Użytkownik końcowy widzi znane środowisko i nie musi zalogować się ponownie w każdej aplikacji. Na przykład zamiast wyświetlania selektora konta natywnego, wtyczka logowania jednokrotnego dodaje sesje logowania jednokrotnego do środowiska selektora konta opartego na sieci Web. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat trybu udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md).
