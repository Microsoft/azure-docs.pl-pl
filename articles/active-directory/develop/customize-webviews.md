---
title: Dostosowywanie przeglądarek & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować środowisko przeglądarki MSAL iOS/macOS, aby zalogować się do użytkowników.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 3b4362e4c5e69efddfbc99ef0f98ad3c5966165c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450882"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Jak: Dostosowywanie przeglądarek i webviews dla systemu iOS/macOS

Do uwierzytelniania interaktywnego wymagana jest przeglądarka internetowa. W systemach iOS i macOS 10.15+Biblioteka uwierzytelniania Firmy Microsoft (MSAL) domyślnie używa systemowej przeglądarki sieci Web (która może być wyświetlana na górze aplikacji) do uwierzytelniania interaktywnego w celu zalogowania się użytkowników. Korzystanie z przeglądarki systemowej ma tę zaletę, że współużytkuje stan logowania jednokrotnego (SSO) innym aplikacjom i aplikacjom internetowym.

Środowisko można zmienić, dostosowując konfigurację do innych opcji wyświetlania zawartości sieci Web, takich jak:

Tylko w przypadku systemu iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [Kontroler SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

W przypadku systemu iOS i macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MsAL dla systemu `WKWebView` macOS obsługuje tylko starsze wersje systemu operacyjnego. `ASWebAuthenticationSession`jest obsługiwany tylko w systemie macOS 10.15 i nowszym. 

## <a name="system-browsers"></a>Przeglądarki systemowe

W przypadku `ASWebAuthenticationSession`systemu `SFAuthenticationSession`iOS , i `SFSafariViewController` są uważane za przeglądarki systemowe. W systemie macOS jest dostępna tylko `ASWebAuthenticationSession` opcja. Ogólnie rzecz biorąc, przeglądarki systemowe udostępniają pliki cookie i inne dane witryny z aplikacją przeglądarki Safari.

Domyślnie usługa MSAL będzie dynamicznie wykrywać wersję systemu iOS i wybierać zalecaną przeglądarkę systemową dostępną w tej wersji. W iOS 12+ `ASWebAuthenticationSession`będzie . 

### <a name="default-configuration-for-ios"></a>Domyślna konfiguracja systemu iOS

| Wersja | Przeglądarka sieci Web |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | Kontroler SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Domyślna konfiguracja systemu macOS

| Wersja | Przeglądarka sieci Web |
|:-------------:|:-------------:|
| macOS 10.15+ | ASWebAuthenticationSession |
| inne wersje | WKWebView (Widok na WKWeb) |

Deweloperzy mogą również wybrać inną przeglądarkę systemową dla aplikacji MSAL:

- `SFAuthenticationSession`jest wersją systemu iOS `ASWebAuthenticationSession`11 .
- `SFSafariViewController`jest bardziej ogólnym celem i zapewnia interfejs do przeglądania stron internetowych i może być używany do celów logowania, jak również. W iOS 9 i 10 pliki cookie i inne dane witryny są udostępniane safari — ale nie w iOS 11 i nowszych.

## <a name="in-app-browser"></a>Przeglądarka w aplikacji

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) to przeglądarka w aplikacji, która wyświetla zawartość sieci Web. Nie udostępnia plików cookie ani danych witryny internetowej innym instancjom **WKWebView** ani przeglądarce Safari. WKWebView to wieloplatformowa przeglądarka, która jest dostępna zarówno dla systemu iOS, jak i macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Implikacje udostępniania plików cookie i logowania jednokrotnego (Logowanie jednokrotne)

Używana przeglądarka wpływa na środowisko użytkownika Ś/ko ze względu na sposób udostępniania plików cookie. W poniższych tabelach podsumowano środowiska samego użytkownika na przeglądarkę.

| Technologia    | Typ przeglądarki  | Dostępność systemu iOS | Dostępność systemu macOS | Udostępnia pliki cookie i inne dane  | Dostępność msal | Logowanie jednokrotne |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 i do góry | macOS 10.15 i do góry | Tak | systemy iOS i macOS 10.15+ | w/ Wystąpienia safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 i do góry | Nie dotyczy | Tak | Tylko system iOS |  w/ Wystąpienia safari
| [Kontroler SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 i do góry | Nie dotyczy | Nie | Tylko system iOS | Nie**
| **Kontroler SFSafariViewController** | System | iOS10 | Nie dotyczy | Tak | Tylko system iOS |  w/ Wystąpienia safari
| **WKWebView (Widok na WKWeb)**  | W aplikacji | iOS8 i do góry | macOS 10.10 i do góry | Nie | iOS i macOS | Nie**

** Aby identyfikator Y SSO działał, tokeny muszą być współdzielone między aplikacjami. Wymaga to pamięci podręcznej tokenu lub aplikacji brokera, takich jak Microsoft Authenticator dla systemu iOS.

## <a name="change-the-default-browser-for-the-request"></a>Zmienianie domyślnej przeglądarki żądania

Możesz użyć przeglądarki w aplikacji lub określonej przeglądarki systemowej w zależności od wymagań `MSALWebviewParameters`środowiska użytkownika, zmieniając następującą właściwość w:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Zmiana na żądanie interaktywne

Każde żądanie można skonfigurować tak, aby zastąpić `MSALInteractiveTokenParameters.webviewParameters.webviewType` domyślną przeglądarkę, `acquireTokenWithParameters:completionBlock:` zmieniając właściwość przed przekazaniem jej do interfejsu API.

Ponadto MSAL obsługuje przekazywanie `WKWebView` w niestandardowe, ustawiając `MSALInteractiveTokenParameters.webviewParameters.customWebView` właściwość.

Przykład:

Obiektowy C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Jeśli używasz niestandardowego widoku internetowego, powiadomienia są używane do wskazywania stanu wyświetlanej zawartości sieci Web, na przykład:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opcje

Wszystkie typy obsługiwanych przez msal przeglądarki sieci web są zadeklarowane w [wyliczeniach MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływach uwierzytelniania i scenariuszach aplikacji](authentication-flows-app-scenarios.md)
