---
title: Wtyczka logowania jednokrotnego Microsoft Enterprise dla urządzeń firmy Apple
titleSuffix: Microsoft identity platform | Azure
description: Zapoznaj się z dodatkiem SSO firmy Microsoft Azure Active Directory dla urządzeń z systemem iOS, iPadOS i macOS.
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
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427100"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Wtyczka Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)

>[!IMPORTANT]
> Ta funkcja [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Wtyczka Logowanie jednokrotne w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple* zapewnia Logowanie jednokrotne (SSO) dla kont usługi Azure Active Directory (Azure AD) w systemach MacOS, iOS i iPadOS we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego [w przedsiębiorstwie](https://developer.apple.com/documentation/authenticationservices) firmy Apple. Obejmuje to starsze aplikacje, od których może zależeć firma, ale które nie obsługują jeszcze najnowszych bibliotek lub protokołów tożsamości. Firma Microsoft pracowała ściśle z firmą Apple, aby opracować Tę wtyczkę w celu zwiększenia użyteczności aplikacji przy jednoczesnym zapewnieniu najlepszej ochrony zapewnianej przez firmę Apple i firmę Microsoft.

Wtyczka Logowanie jednokrotne w przedsiębiorstwie jest obecnie dostępna jako Wbudowana funkcja następujących aplikacji:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) -iOS, iPadOS
* Microsoft Intune [Portal firmy](/mem/intune/apps/apps-company-portal-macos) — macOS

## <a name="features"></a>Funkcje

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple oferuje następujące korzyści:

- Zapewnia Logowanie jednokrotne dla kont usługi Azure AD we wszystkich aplikacjach, które obsługują funkcję pojedynczej Sign-On w przedsiębiorstwie firmy Apple.
- Program może być włączony przez dowolne rozwiązanie do zarządzania urządzeniami przenośnymi (MDM).
- Rozszerza Logowanie jednokrotne do aplikacji, które nie używają jeszcze bibliotek Microsoft Identity platform.
- Rozszerza Logowanie jednokrotne do aplikacji korzystających z OAuth2, OpenID Connect Connect i SAML.

## <a name="requirements"></a>Wymagania

Aby skorzystać z wtyczki Microsoft Enterprise SSO dla urządzeń firmy Apple:

- Urządzenie musi **obsługiwać** i mieć aplikację obejmującą wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla **urządzeń firmy** Apple:
  - iOS 13.0 +: [aplikacja Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 +: [aplikacja Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [aplikacja Intune — portal firmy](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Urządzenie musi być **zarejestrowane w systemie zarządzania urządzeniami przenośnymi** (na przykład z Microsoft Intune).
- Aby włączyć wtyczkę logowania jednokrotnego dla przedsiębiorstwa na urządzeniu, należy przeprowadzić konfigurację **wypychaną do urządzenia** . To ograniczenie zabezpieczeń jest wymagane przez firmę Apple.

### <a name="ios-requirements"></a>wymagania dotyczące systemu iOS:
- na urządzeniu musi być zainstalowany system iOS w wersji 13,0 lub nowszej.
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej te aplikacje są [Microsoft Authenticator aplikacji](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>wymagania macOS:
- na urządzeniu musi być zainstalowany macOS 10,15 lub nowszy. 
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej te aplikacje zawierają [Intune — portal firmy aplikację](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Włącz wtyczkę logowania jednokrotnego za pomocą zarządzania urządzeniami przenośnymi (MDM)

### <a name="microsoft-intune-configuration"></a>Konfiguracja Microsoft Intune

Jeśli używasz Microsoft Intune jako usługi MDM, możesz użyć wbudowanych ustawień profilu konfiguracji, aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft.

Najpierw skonfiguruj ustawienia [rozszerzenia aplikacji Logowanie](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) jednokrotne w profilu konfiguracji i [Przypisz profil do grupy użytkowników lub urządzeń](/mem/intune/configuration/device-profile-assign) (jeśli nie został jeszcze przypisany).

Ustawienia profilu, które włączają wtyczkę logowania jednokrotnego, są automatycznie stosowane do urządzeń grupy podczas następnego zaewidencjonowania urządzenia w usłudze Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Konfiguracja ręczna dla innych usług MDM

Jeśli nie używasz Microsoft Intune do zarządzania urządzeniami przenośnymi, użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple.

#### <a name="ios-settings"></a>Ustawienia systemu iOS:

- **Identyfikator rozszerzenia**: `com.microsoft.azureauthenticator.ssoextension`
- **Identyfikator zespołu**: (to pole nie jest wymagane w przypadku systemu iOS)

#### <a name="macos-settings"></a>Ustawienia systemu macOS:

- **Identyfikator rozszerzenia**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Identyfikator zespołu**: `UBF8T346G9`

#### <a name="common-settings"></a>Typowe ustawienia:

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
  
### <a name="additional-configuration-options"></a>Dodatkowe opcje konfiguracji
Dodatkowe opcje konfiguracji można dodać, aby zwiększyć możliwości rejestracji jednokrotnej w dodatkowych aplikacjach.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Włącz logowanie jednokrotne dla aplikacji, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft

Wtyczka Logowanie jednokrotne umożliwia każdej aplikacji uczestnictwo w usłudze logowania jednokrotnego, nawet jeśli nie została opracowana przy użyciu zestawu Microsoft SDK, takiego jak biblioteka uwierzytelniania firmy Microsoft (MSAL).

Wtyczka logowania jednokrotnego jest instalowana automatycznie przez urządzenia, które pobrały aplikację Microsoft Authenticator w systemach iOS i iPadOS lub Intune — Portal firmy App w macOS i zarejestrowali swoje urządzenie w organizacji. Twoja organizacja prawdopodobnie korzysta obecnie z aplikacji Authenticator w scenariuszach takich jak uwierzytelnianie wieloskładnikowe, uwierzytelnianie bez hasła i dostęp warunkowy. Można ją włączyć dla aplikacji przy użyciu dowolnego dostawcy MDM, Chociaż firma Microsoft mogła ją skonfigurować w programie Microsoft Endpoint Manager usługi Intune. Lista dozwolonych służy do konfigurowania tych aplikacji do korzystania z wtyczki logowania jednokrotnego.

>[!IMPORTANT]
> Obsługiwane są tylko aplikacje korzystające z natywnych technologii sieci Apple lub WebViews. Jeśli aplikacja dostarcza własną implementację warstwy sieciowej, wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft nie jest obsługiwana.  

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla aplikacji, które nie korzystają z biblioteki Microsoft Identity platform:

Jeśli chcesz podać listę określonych aplikacji:

- **Klucz**: `AppAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej
- **Przykład**: `com.contoso.workapp, com.contoso.travelapp`

Lub jeśli chcesz podać listę prefiksów:
- **Klucz**: `AppPrefixAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista prefiksów identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej. Należy pamiętać, że spowoduje to włączenie wszystkich aplikacji zaczynających się od określonego prefiksu do uczestnictwa w rejestracji jednokrotnej
- **Przykład**: `com.contoso., com.fabrikam.`

Zgodne [aplikacje](./application-consent-experience.md) , które są dozwolone przez administratora zarządzania urządzeniami przenośnymi, mogą w trybie dyskretnym uzyskać token dla użytkownika końcowego. W związku z tym ważne jest, aby dodać tylko zaufane aplikacje do listy dozwolonych. 

>[!NOTE]
> Nie musisz dodawać do tej listy aplikacji, które używają MSAL lub ASWebAuthenticationSession. Te aplikacje są domyślnie włączone. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Jak wykryć identyfikatory zbioru aplikacji na urządzeniach z systemem iOS

Firma Apple nie zapewnia łatwego sposobu odnajdywania identyfikatorów pakietów ze sklepu z aplikacjami. Najprostszym sposobem odnajdywania identyfikatorów pakietów aplikacji, które chcą używać na potrzeby logowania jednokrotnego, jest poproszenie swojego dostawcy lub dewelopera aplikacji. Jeśli ta opcja jest niedostępna, można użyć konfiguracji MDM do odnajdywania identyfikatorów pakietów. 

Tymczasowo Włącz następującą flagę w konfiguracji MDM:

- **Klucz**: `admin_debug_mode_enabled`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Gdy ta flaga jest włączona, zaloguj się do aplikacji systemu iOS na urządzeniu, dla którego chcesz znać identyfikator pakietu. Następnie otwórz Microsoft Authenticator App-> help-> wysyłanie dzienników — > Wyświetl dzienniki. 

W pliku dziennika poszukaj następującego wiersza:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Powinno to przechwycić wszystkie identyfikatory pakietów aplikacji widoczne dla rozszerzenia rejestracji jednokrotnej. Następnie można użyć tych identyfikatorów, aby skonfigurować Logowanie jednokrotne dla tych aplikacji. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Zezwalaj użytkownikowi na logowanie się z nieznanych aplikacji i przeglądarki Safari.

Domyślnie wtyczka Microsoft Enterprise SSO umożliwia logowanie jednokrotne do autoryzowanych aplikacji tylko wtedy, gdy użytkownik zalogował się z aplikacji korzystającej z biblioteki platformy tożsamości firmy Microsoft, takiej jak ADAL lub MSAL. Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft może również uzyskać udostępnione poświadczenie, gdy zostanie wywołane przez inną aplikację, która korzysta z biblioteki platformy tożsamości firmy Microsoft podczas uzyskiwania nowego tokenu.

Włączenie `browser_sso_interaction_enabled` flagi umożliwia aplikacji, która nie korzysta z biblioteki platformy tożsamości firmy Microsoft do wykonywania początkowego ładowania i uzyskiwania poświadczeń udostępnionych. Umożliwia także przeglądarce Safari wykonywanie początkowego uruchamiania i uzyskiwanie poświadczeń udostępnionych. Jeśli wtyczka Microsoft Enterprise SSO nie ma jeszcze udostępnionego poświadczenia, spróbuje je pobrać za każdym razem, gdy zażądano logowania z adresu URL usługi Azure AD w przeglądarce Safari, ASWebAuthenticationSession, SafariViewController lub innej dozwolonej aplikacji natywnej.  

- **Klucz**: `browser_sso_interaction_enabled`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Dla macOS to ustawienie jest wymagane, aby zapewnić bardziej spójne środowisko dla wszystkich aplikacji. Dla systemu iOS i iPadOS to ustawienie nie jest wymagane, ponieważ większość aplikacji używa aplikacji Microsoft Authenticator do logowania. Jeśli jednak masz pewne aplikacje, które nie używają Microsoft Authenticator w systemie iOS lub iPadOS, ta flaga poprawi środowisko, dlatego zalecamy włączenie tego ustawienia. To ustawienie jest domyślnie wyłączone.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Wyłącz monitowanie o uruchomienie usługi MFA podczas początkowego ładowania

Domyślnie wtyczka Microsoft Enterprise SSO jest zawsze monitowany o użytkownika w celu uwierzytelniania wieloskładnikowego (MFA) podczas wykonywania początkowego ładowania i uzyskiwania poświadczeń udostępnionych, nawet jeśli nie jest to wymagane dla bieżącej aplikacji, którą uruchomił użytkownik. Jest tak dlatego, że udostępnione poświadczenia mogą być łatwo używane we wszystkich dodatkowych aplikacjach bez monitowania użytkownika, jeśli uwierzytelnianie wieloskładnikowe stanie się później wymagane. Pozwala to skrócić czas, przez jaki użytkownik musi być monitowany na urządzeniu i jest generalnie dobrą decyzją.

Włączenie `browser_sso_disable_mfa` tej opcji powoduje wyłączenie usługi i wyświetlenie monitu o podanie tylko użytkownika, gdy uwierzytelnianie wieloskładnikowe jest wymagane przez aplikację lub zasób. 

- **Klucz**: `browser_sso_disable_mfa`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zaleca się pozostawienie tej flagi jako wyłączonej, ponieważ zmniejsza to czas, w którym użytkownik musi być monitowany na urządzeniu. Jeśli Twoja organizacja rzadko używa usługi MFA, możesz włączyć flagę, ale zamiast tego zalecamy użycie usługi MFA częściej. Z tego powodu jest domyślnie wyłączony.

#### <a name="disable-oauth2-application-prompts"></a>Wyłącz OAuth2y aplikacji

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft zapewnia Logowanie jednokrotne, dołączając poświadczenia udostępnione do żądań sieci przychodzących z dozwolonych aplikacji. Niektóre aplikacje OAuth2 mogą jednak niepoprawnie wymusić wyświetlanie przez użytkownika końcowego pożądanych w warstwie protokołu. W takim przypadku zobaczysz, że udostępnione poświadczenia są ignorowane dla tych aplikacji, a użytkownik zostanie poproszony o zalogowanie się, nawet jeśli wtyczka logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft działa dla innych aplikacji.  

Włączenie `disable_explicit_app_prompt` flagi ogranicza zdolność aplikacji natywnych i sieci Web w celu wymuszenia monitu użytkownika końcowego o warstwę protokołu i obejścia logowania JEDNOkrotnego.

- **Klucz**: `disable_explicit_app_prompt`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zalecamy włączenie tej flagi w celu uzyskania bardziej spójnego środowiska dla wszystkich aplikacji. To ustawienie jest domyślnie wyłączone. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Włącz logowanie jednokrotne za pomocą plików cookie dla określonej aplikacji

Niewielka liczba aplikacji może być niezgodna z rozszerzeniem rejestracji jednokrotnej. W przypadku aplikacji, które mają zaawansowane ustawienia sieciowe mogą wystąpić nieoczekiwane problemy, gdy są one włączone dla logowania jednokrotnego (np. może zostać wyświetlony komunikat o błędzie, że żądanie sieci zostało anulowane lub przerwane). 

Jeśli występują problemy z logowaniem przy użyciu metody opisanej w `Enable SSO for apps that don't use MSAL` sekcji, można wypróbować alternatywną konfigurację dla tych aplikacji. 

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla tych konkretnych aplikacji:

- **Klucz**: `AppCookieSSOAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista prefiksów identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej. Należy pamiętać, że spowoduje to włączenie wszystkich aplikacji zaczynających się od określonego prefiksu do uczestnictwa w rejestracji jednokrotnej
- **Przykład**: `com.contoso.myapp1, com.fabrikam.myapp2`

Należy pamiętać, że aplikacje włączone dla logowania jednokrotnego przy użyciu tego mechanizmu należy dodać zarówno do programu `AppCookieSSOAllowList` , jak i `AppPrefixAllowList` .

Zalecamy próbę wykonania tej opcji tylko w przypadku aplikacji, które mają nieoczekiwane błędy logowania. 

#### <a name="use-intune-for-simplified-configuration"></a>Konfiguracja uproszczona przy użyciu usługi Intune

Jak wspomniano wcześniej, możesz użyć Microsoft Intune jako usługi MDM, aby ułatwić konfigurację wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft, w tym włączyć wtyczkę i dodać starsze aplikacje do listy dozwolonych, aby umożliwić im Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [dokumentację konfiguracyjną usługi Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Korzystanie z wtyczki logowania jednokrotnego w aplikacji

[Biblioteka Microsoft Authentication Library (MSAL) dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple. Jest to zalecany sposób dodawania obsługi wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft i zapewnia pełną możliwość korzystania z platformy tożsamości firmy Microsoft.

Jeśli tworzysz aplikację dla scenariuszy roboczych teraźniejszości, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md) w celu uzyskania dodatkowych ustawień funkcji.

## <a name="how-the-sso-plug-in-works"></a>Jak działa wtyczka Logowanie jednokrotne

Wtyczka Microsoft Enterprise SSO jest oparta na [architekturze jednoSign-Onowej dla przedsiębiorstwa firmy Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Dostawcy tożsamości dołączenia do struktury mogą przechwycić ruch sieciowy dla swoich domen i zwiększyć lub zmienić sposób obsługi tych żądań. Na przykład wtyczka logowania jednokrotnego może wyświetlać dodatkowy interfejs użytkownika, aby bezpiecznie zbierać poświadczenia użytkownika końcowego, wymagać uwierzytelniania wieloskładnikowego lub w trybie dyskretnym.

Natywne aplikacje mogą również implementować operacje niestandardowe i komunikować się bezpośrednio z wtyczką logowania jednokrotnego.
Więcej informacji na temat platformy rejestracji jednokrotnej można znaleźć w tym [2019 WWDC wideo od firmy Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Aplikacje korzystające z biblioteki platformy tożsamości firmy Microsoft

[Biblioteka Microsoft Authentication Library (MSAL) dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple dla kont służbowych. 

Żadna specjalna konfiguracja nie jest wymagana, jeśli wykonano [wszystkie zalecane kroki](./quickstart-v2-ios.md) i użyto domyślnego [formatu identyfikatora URI przekierowania](./redirect-uris-ios.md). W przypadku uruchamiania na urządzeniu, na którym zainstalowano wtyczkę logowania jednokrotnego, usługa MSAL automatycznie wywołaje ją dla wszystkich żądań tokenów interaktywnych i dyskretnych, a także wyliczania kont i usuwania kont. Ponieważ MSAL implementuje natywny protokół typu plug-in logowania jednokrotnego, który opiera się na operacjach niestandardowych, ta konfiguracja zapewnia użytkownikom końcowym bezproblemowe środowisko natywne. 

Jeśli wtyczka logowania jednokrotnego nie jest włączona w usłudze MDM, ale aplikacja Microsoft Authenticator jest obecna na urządzeniu, MSAL będzie używać aplikacji Microsoft Authenticator do dowolnych żądań tokenów interaktywnych. Wtyczka logowania jednokrotnego udostępnia Logowanie jednokrotne za pomocą aplikacji Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Aplikacje, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft

Aplikacje, które nie korzystają z biblioteki platformy tożsamości firmy Microsoft, takiej jak MSAL, nadal mogą uzyskać Logowanie jednokrotne, jeśli administrator doda je do listy dozwolonych. 

W tych aplikacjach nie trzeba zmieniać kodu, dopóki są spełnione następujące warunki:

- Aplikacja używa Platform firmy Apple do wykonywania żądań sieciowych (na przykład [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Aplikacja korzysta z protokołów standardowych do komunikowania się z usługą Azure AD (na przykład OAuth2, SAML, WS-Federation)
- Aplikacja nie zbiera nazwy użytkownika i hasła w natywnym interfejsie użytkownika

W takim przypadku Logowanie jednokrotne jest udostępniane, gdy aplikacja tworzy żądanie sieciowe i otwiera przeglądarkę internetową w celu podpisania użytkownika w programie. Gdy użytkownik zostanie przekierowany do adresu URL logowania usługi Azure AD, wtyczka logowania jednokrotnego weryfikuje adres URL i sprawdza, czy dla tego adresu URL jest dostępne poświadczenie logowania jednokrotnego. Jeśli istnieje, wtyczka logowania jednokrotnego przekazuje poświadczenia logowania jednokrotnego do usługi Azure AD, która autoryzuje aplikację do ukończenia żądania sieciowego bez monitowania użytkownika o wprowadzenie poświadczeń. Ponadto jeśli urządzenie jest znane w usłudze Azure AD, wtyczka logowania jednokrotnego również przekaże certyfikat urządzenia, aby spełnić sprawdzanie dostępu warunkowego opartego na urządzeniach. 

Aby można było obsługiwać Logowanie jednokrotne dla aplikacji innych niż MSAL, wtyczka Logowanie jednokrotne implementuje protokół podobny do wtyczki przeglądarki systemu Windows opisanej w artykule [co to jest podstawowy token odświeżania?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

W porównaniu z aplikacjami opartymi na MSAL, wtyczka logowania jednokrotnego działa w sposób bardziej niewidoczny dla aplikacji innych niż MSAL przez integrację z istniejącym interfejsem logowania w przeglądarce udostępnianym przez aplikacje. Użytkownik końcowy zobaczy znane środowisko, z korzyścią, że nie trzeba wykonywać dodatkowych logowań w poszczególnych aplikacjach. Na przykład zamiast wyświetlania selektora konta natywnego, wtyczka logowania jednokrotnego dodaje sesje logowania jednokrotnego do środowiska selektora konta opartego na sieci Web. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat trybu udostępnionego urządzenia w systemie iOS, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md).
