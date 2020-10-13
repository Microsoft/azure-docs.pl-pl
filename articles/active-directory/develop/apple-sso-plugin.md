---
title: Wtyczka logowania jednokrotnego Microsoft Enterprise dla urządzeń firmy Apple
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej na temat wtyczki Microsoft Azure Active Directory SSO dla urządzeń z systemem iOS i macOS.
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
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530701"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Wtyczka Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Wtyczka Logowanie jednokrotne w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple* zapewnia Logowanie jednokrotne (SSO) dla kont usługi Azure Active Directory (Azure AD) we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego [w przedsiębiorstwie](https://developer.apple.com/documentation/authenticationservices) firmy Apple. Firma Microsoft pracowała ściśle z firmą Apple, aby opracować Tę wtyczkę w celu zwiększenia użyteczności aplikacji przy jednoczesnym zapewnieniu najlepszej ochrony zapewnianej przez firmę Apple i firmę Microsoft.

W tej publicznej wersji zapoznawczej wtyczka logowania jednokrotnego w przedsiębiorstwie jest dostępna tylko dla urządzeń z systemem iOS i jest dystrybuowana w określonych aplikacjach firmy Microsoft.

## <a name="features"></a>Funkcje

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple oferuje następujące korzyści:

- Zapewnia Logowanie jednokrotne dla kont usługi Azure AD we wszystkich aplikacjach, które obsługują funkcję pojedynczej Sign-On w przedsiębiorstwie firmy Apple.
- Dostarczane automatycznie w Microsoft Authenticator i mogą być włączane przez dowolne rozwiązanie do zarządzania urządzeniami przenośnymi (MDM).

## <a name="requirements"></a>Wymagania

Aby skorzystać z wtyczki Microsoft Enterprise SSO dla urządzeń firmy Apple:

- na urządzeniu musi być zainstalowany system iOS w wersji 13,0 lub nowszej.
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej te aplikacje zawierają [Microsoft Authenticator aplikację](../user-help/user-help-auth-app-overview.md).
- Urządzenie musi być zarejestrowane w systemie zarządzania urządzeniami przenośnymi (na przykład z Microsoft Intune).
- Aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie dla urządzeń firmy Microsoft na urządzeniu, należy przeprowadzić konfigurację wypychaną na urządzenie. To ograniczenie zabezpieczeń jest wymagane przez firmę Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Włącz wtyczkę logowania jednokrotnego za pomocą zarządzania urządzeniami przenośnymi (MDM)

Aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple, urządzenia muszą wysyłać sygnały za pomocą usługi MDM. Ponieważ firma Microsoft obejmuje wtyczkę logowania jednokrotnego dla przedsiębiorstw w [aplikacji Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), Użyj urządzenia MDM do skonfigurowania aplikacji w celu włączenia wtyczki Microsoft Enterprise SSO.

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple:

- **Typ**: redirect
- **Identyfikator rozszerzenia**: `com.microsoft.azureauthenticator.ssoextension`
- **Identyfikator zespołu**: (to pole nie jest wymagane w przypadku systemu iOS)
- **Adresy URL**:
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

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Włącz logowanie jednokrotne dla aplikacji, które nie używają MSAL

Wtyczka Logowanie jednokrotne umożliwia każdej aplikacji uczestnictwo w usłudze logowania jednokrotnego, nawet jeśli nie została opracowana przy użyciu zestawu Microsoft SDK, takiego jak biblioteka uwierzytelniania firmy Microsoft (MSAL).

Wtyczka logowania jednokrotnego jest instalowana automatycznie przez urządzenia, które pobrały aplikację Microsoft Authenticator i zarejestrowała swoje urządzenie w organizacji. Twoja organizacja prawdopodobnie korzysta obecnie z aplikacji Authenticator w scenariuszach takich jak uwierzytelnianie wieloskładnikowe, uwierzytelnianie bez hasła i dostęp warunkowy. Można ją włączyć dla aplikacji przy użyciu dowolnego dostawcy MDM, Chociaż firma Microsoft mogła ją skonfigurować w programie Microsoft Endpoint Manager usługi Intune. Lista dozwolonych służy do konfigurowania tych aplikacji do korzystania z wtyczki logowania jednokrotnego zainstalowanej przez aplikację Authenticator.

Obsługiwane są tylko aplikacje korzystające z natywnych technologii sieci Apple lub WebViews. Jeśli aplikacja dostarcza własną implementację warstwy sieciowej, wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft nie jest obsługiwana.  

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla aplikacji, które nie używają MSAL:

- **Klucz**: `AppAllowList`
- **Typ**: `String`
- **Wartość**: rozdzielana przecinkami lista identyfikatorów pakietów aplikacji dla aplikacji, które mogą uczestniczyć w rejestracji jednokrotnej
- **Przykład**: `com.contoso.workapp, com.contoso.travelapp`

Zgodne [aplikacje](./application-consent-experience.md) , które są dozwolone przez administratora zarządzania urządzeniami przenośnymi, mogą w trybie dyskretnym uzyskać token dla użytkownika końcowego. W związku z tym ważne jest, aby dodać tylko zaufane aplikacje do listy dozwolonych. 

Nie musisz dodawać do tej listy aplikacji, które używają MSAL lub ASWebAuthenticationSession. Te aplikacje są domyślnie włączone. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Zezwalaj na tworzenie sesji logowania jednokrotnego z dowolnej aplikacji

Domyślnie wtyczka Microsoft Enterprise SSO umożliwia logowanie jednokrotne do autoryzowanych aplikacji tylko wtedy, gdy wtyczka logowania jednokrotnego ma już udostępnione poświadczenia. Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft może uzyskać udostępnione poświadczenie, gdy jest wywoływana przez inną aplikację ADAL lub MSAL podczas pozyskiwania tokenu. Większość aplikacji firmy Microsoft korzysta z wtyczki Microsoft Authenticator lub logowania jednokrotnego. Oznacza to, że domyślnie Logowanie jednokrotne poza natywnymi przepływami aplikacji jest najlepszym rozwiązaniem.  

Włączenie `browser_sso_interaction_enabled` flagi umożliwia aplikacjom MSAL i przeglądarce Safari wykonywanie początkowego uruchamiania i uzyskiwanie poświadczeń udostępnionych. Jeśli wtyczka Microsoft Enterprise SSO nie ma jeszcze udostępnionego poświadczenia, spróbuje je pobrać za każdym razem, gdy zażądano logowania z adresu URL usługi Azure AD w przeglądarce Safari, ASWebAuthenticationSession, SafariViewController lub innej dozwolonej aplikacji natywnej.  

- **Klucz**: `browser_sso_interaction_enabled`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zalecamy włączenie tej flagi w celu uzyskania bardziej spójnego środowiska dla wszystkich aplikacji. To ustawienie jest domyślnie wyłączone. 

#### <a name="disable-oauth2-application-prompts"></a>Wyłącz OAuth2y aplikacji

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft zapewnia Logowanie jednokrotne, dołączając poświadczenia udostępnione do żądań sieci przychodzących z dozwolonych aplikacji. Niektóre aplikacje OAuth2 mogą wymuszać monit użytkownika końcowego o warstwę protokołu. Dla tych aplikacji zostanie zignorowane udostępnione poświadczenie.  

Włączenie `disable_explicit_app_prompt` flagi ogranicza zdolność aplikacji natywnych i sieci Web w celu wymuszenia monitu użytkownika końcowego o warstwę protokołu i obejścia logowania JEDNOkrotnego.

- **Klucz**: `disable_explicit_app_prompt`
- **Typ**: `Integer`
- **Wartość**: 1 lub 0

Zalecamy włączenie tej flagi w celu uzyskania bardziej spójnego środowiska dla wszystkich aplikacji. To ustawienie jest domyślnie wyłączone. 

#### <a name="use-intune-for-simplified-configuration"></a>Konfiguracja uproszczona przy użyciu usługi Intune

Microsoft Intune jako usługi MDM można użyć, aby ułatwić konfigurację wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [dokumentację konfiguracyjną usługi Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Korzystanie z wtyczki logowania jednokrotnego w aplikacji

[Biblioteka Microsoft Authentication Library (MSAL) dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple.

Jeśli tworzysz aplikację dla scenariuszy roboczych teraźniejszości, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md) w celu uzyskania dodatkowych ustawień funkcji.

## <a name="how-the-sso-plug-in-works"></a>Jak działa wtyczka Logowanie jednokrotne

Wtyczka Microsoft Enterprise SSO jest oparta na [architekturze jednoSign-Onowej dla przedsiębiorstwa firmy Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Dostawcy tożsamości dołączenia do struktury mogą przechwycić ruch sieciowy dla swoich domen i zwiększyć lub zmienić sposób obsługi tych żądań. Na przykład wtyczka logowania jednokrotnego może wyświetlać dodatkowy interfejs użytkownika, aby bezpiecznie zbierać poświadczenia użytkownika końcowego, wymagać uwierzytelniania wieloskładnikowego lub w trybie dyskretnym.

Natywne aplikacje mogą również implementować operacje niestandardowe i komunikować się bezpośrednio z wtyczką logowania jednokrotnego.
Więcej informacji na temat platformy rejestracji jednokrotnej można znaleźć w tym [2019 WWDC wideo od firmy Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Aplikacje korzystające z MSAL

[Biblioteka Microsoft Authentication Library (MSAL) dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple dla kont służbowych. 

Żadna specjalna konfiguracja nie jest wymagana, jeśli wykonano [wszystkie zalecane kroki](./quickstart-v2-ios.md) i użyto domyślnego [formatu identyfikatora URI przekierowania](./redirect-uris-ios.md). W przypadku uruchamiania na urządzeniu, na którym zainstalowano wtyczkę logowania jednokrotnego, usługa MSAL automatycznie wywołaje ją dla wszystkich żądań tokenów interaktywnych i dyskretnych, a także wyliczania kont i usuwania kont. Ponieważ MSAL implementuje natywny protokół typu plug-in logowania jednokrotnego, który opiera się na operacjach niestandardowych, ta konfiguracja zapewnia użytkownikom końcowym bezproblemowe środowisko natywne. 

Jeśli wtyczka logowania jednokrotnego nie jest włączona w usłudze MDM, ale aplikacja Microsoft Authenticator jest obecna na urządzeniu, MSAL będzie używać aplikacji Microsoft Authenticator do dowolnych żądań tokenów interaktywnych. Wtyczka logowania jednokrotnego udostępnia Logowanie jednokrotne za pomocą aplikacji Microsoft Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplikacje, które nie używają MSAL

Aplikacje, które nie używają MSAL, nadal mogą uzyskać Logowanie jednokrotne, jeśli administrator doda je do listy dozwolonych. 

W tych aplikacjach nie trzeba zmieniać kodu, dopóki są spełnione następujące warunki:

- Aplikacja używa Platform firmy Apple do wykonywania żądań sieciowych (na przykład [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Aplikacja korzysta z protokołów standardowych do komunikowania się z usługą Azure AD (na przykład OAuth2, SAML, WS-Federation)
- Aplikacja nie zbiera nazwy użytkownika i hasła w natywnym interfejsie użytkownika

W takim przypadku Logowanie jednokrotne jest udostępniane, gdy aplikacja tworzy żądanie sieciowe i otwiera przeglądarkę internetową w celu podpisania użytkownika w programie. Gdy użytkownik zostanie przekierowany do adresu URL logowania usługi Azure AD, wtyczka logowania jednokrotnego weryfikuje adres URL i sprawdza, czy dla tego adresu URL jest dostępne poświadczenie logowania jednokrotnego. Jeśli istnieje, wtyczka logowania jednokrotnego przekazuje poświadczenia logowania jednokrotnego do usługi Azure AD, która autoryzuje aplikację do ukończenia żądania sieciowego bez monitowania użytkownika o wprowadzenie poświadczeń. Ponadto jeśli urządzenie jest znane w usłudze Azure AD, wtyczka logowania jednokrotnego również przekaże certyfikat urządzenia, aby spełnić sprawdzanie dostępu warunkowego opartego na urządzeniach. 

Aby można było obsługiwać Logowanie jednokrotne dla aplikacji innych niż MSAL, wtyczka Logowanie jednokrotne implementuje protokół podobny do wtyczki przeglądarki systemu Windows opisanej w artykule [co to jest podstawowy token odświeżania?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

W porównaniu z aplikacjami opartymi na MSAL, wtyczka logowania jednokrotnego działa w sposób bardziej niewidoczny dla aplikacji innych niż MSAL przez integrację z istniejącym interfejsem logowania w przeglądarce udostępnianym przez aplikacje. Użytkownik końcowy zobaczy znane środowisko, z korzyścią, że nie trzeba wykonywać dodatkowych logowań w poszczególnych aplikacjach. Na przykład zamiast wyświetlania selektora konta natywnego, wtyczka logowania jednokrotnego dodaje sesje logowania jednokrotnego do środowiska selektora konta opartego na sieci Web. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat trybu udostępnionego urządzenia w systemie iOS, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md).
