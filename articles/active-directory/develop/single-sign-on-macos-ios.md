---
title: Konfigurowanie logowania jednokrotnego w systemach macOS i iOS
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne (SSO) w systemach macOS i iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80881252"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Instrukcje: Konfigurowanie logowania jednokrotnego w systemach macOS i iOS

Biblioteka Microsoft Authentication Library (MSAL) dla macOS i iOS obsługuje logowanie jednokrotne (SSO) między aplikacjami macOS/iOS i przeglądarkami. W tym artykule opisano następujące scenariusze logowania jednokrotnego:

- [Dyskretne Logowanie jednokrotne między wieloma aplikacjami](#silent-sso-between-apps)

Ten typ logowania jednokrotnego działa w wielu aplikacjach dystrybuowanych przez tego samego deweloperów firmy Apple. Zapewnia to dyskretne Logowanie jednokrotne (oznacza to, że użytkownik nie jest monitowany o poświadczenia) poprzez odczytywanie tokenów odświeżania pisanych przez inne aplikacje z łańcucha kluczy i wymianę ich na potrzeby tokenów dostępu dyskretnie.  

- [Logowanie jednokrotne za pośrednictwem brokera uwierzytelniania](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Ten przepływ nie jest dostępny w witrynie macOS.

Firma Microsoft udostępnia aplikacje, nazywane brokerami, które umożliwiają logowanie jednokrotne między aplikacjami od różnych dostawców, o ile urządzenie przenośne jest zarejestrowane w usłudze Azure Active Directory (AAD). Ten typ logowania jednokrotnego wymaga zainstalowania na urządzeniu użytkownika aplikacji brokera.

- **Logowanie jednokrotne między MSAL a Safari**

Logowanie jednokrotne jest realizowane za pomocą klasy [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) . Używa on istniejącego stanu logowania z innych aplikacji i przeglądarki Safari. Nie jest to ograniczone do aplikacji dystrybuowanych przez tego samego deweloperów firmy Apple, ale wymaga to pewnej interakcji z użytkownikiem.

Jeśli używasz domyślnego widoku sieci Web w aplikacji do logowania użytkowników, uzyskasz automatyczne logowanie jednokrotne między aplikacjami opartymi na MSAL i Safari. Aby dowiedzieć się więcej o widokach sieci Web obsługiwanych przez program MSAL, odwiedź stronę [Dostosowywanie przeglądarek i WebViews](customize-webviews.md).

> [!IMPORTANT]
> Ten typ logowania jednokrotnego nie jest obecnie dostępny w witrynie macOS. MSAL on macOS obsługuje tylko WKWebView, które nie obsługują logowania jednokrotnego za pomocą przeglądarki Safari. 

- **Dyskretne Logowanie jednokrotne między bibliotekami ADAL i MSAL macOS/iOS**

MSAL cel-C obsługuje migrację i logowanie jednokrotne za pomocą aplikacji opartych na języku C. Aplikacje muszą być dystrybuowane przez tego samego deweloperów firmy Apple.

Zobacz [Logowanie jednokrotne między aplikacjami ADAL i MSAL w systemie macOS i iOS,](sso-between-adal-msal-apps-macos-ios.md) Aby uzyskać instrukcje dotyczące rejestracji jednokrotnej w aplikacjach opartych na domenach ADAL i MSAL.

## <a name="silent-sso-between-apps"></a>Dyskretna Logowanie jednokrotne między aplikacjami

Usługa MSAL obsługuje udostępnianie Logowanie jednokrotne za pomocą grup dostępu łańcucha kluczy systemu iOS.

Aby włączyć logowanie jednokrotne w aplikacjach, należy wykonać następujące kroki, które opisano szczegółowo poniżej:

1. Upewnij się, że wszystkie aplikacje używają tego samego identyfikatora klienta lub identyfikatora aplikacji.
1. Upewnij się, że wszystkie aplikacje mają ten sam certyfikat podpisywania od firmy Apple, aby można było udostępnić łańcuchy kluczy.
1. Zażądaj tego samego uprawnienia łańcucha kluczy dla każdej aplikacji.
1. Poinformuj zestaw SDK MSAL o udostępnionym pęku kluczy, którego chcesz użyć, jeśli jest inny niż domyślny.

### <a name="use-the-same-client-id-and-application-id"></a>Użyj tego samego identyfikatora klienta i identyfikatora aplikacji

Aby platforma tożsamości firmy Microsoft mogła dowiedzieć się, które aplikacje mogą udostępniać tokeny, te aplikacje muszą mieć ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został dostarczony podczas rejestracji pierwszej aplikacji w portalu.

Sposób, w jaki platforma tożsamości firmy Microsoft informuje aplikacje, które używają tego samego identyfikatora aplikacji, jest przez ich **identyfikatory URI przekierowania**. Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w portalu dołączania. Każda aplikacja w Twoim pakiecie będzie miała inny identyfikator URI przekierowania. Na przykład:

Identyfikator URI przekierowania APP1: `msauth.com.contoso.mytestapp1://auth`  
Identyfikator URI przekierowania APP2: `msauth.com.contoso.mytestapp2://auth`  
Identyfikator URI przekierowania APP3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Format identyfikatorów URI przekierowania musi być zgodny z formatem MSAL obsługuje, który jest udokumentowany w [wymaganiach dotyczących formatu identyfikatora URI przekierowania MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Konfigurowanie udostępniania łańcucha kluczy między aplikacjami

Zapoznaj się z artykułem [Dodawanie możliwości](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) firmy Apple, aby włączyć udostępnianie łańcucha kluczy. Ważne jest, aby zdecydować, jak ma być wywoływana łańcucha kluczy, i dodać tę możliwość do wszystkich aplikacji, które będą objęte logowaniem jednokrotnym.

Po poprawnym skonfigurowaniu uprawnień zobaczysz `entitlements.plist` plik w katalogu projektu zawierający coś podobne do tego przykładu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Dodaj nową grupę pęku kluczy

Dodaj nową grupę pęku kluczy do **możliwości** projektu. Grupa łańcucha kluczy powinna:
* `com.microsoft.adalcache` w systemie iOS 
* `com.microsoft.identity.universalstorage` w witrynie macOS.

![przykład łańcucha kluczy](media/single-sign-on-macos-ios/keychain-example.png)

Aby uzyskać więcej informacji, zobacz [grupy pęku kluczy](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurowanie obiektu aplikacji

Po włączeniu uprawnień łańcucha kluczy w każdej aplikacji i przygotowaniu do korzystania z logowania jednokrotnego można skonfigurować `MSALPublicClientApplication` za pomocą grupy dostępu łańcucha kluczy, tak jak w poniższym przykładzie:

Cel-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Adres

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Po udostępnieniu łańcucha kluczy w aplikacjach każda aplikacja może usuwać użytkowników, a nawet wszystkie tokeny w aplikacji.
> Jest to szczególnie zagrożone w przypadku aplikacji korzystających z tokenów do wykonywania zadań w tle.
> Udostępnianie łańcucha kluczy oznacza, że należy zachować ostrożność, gdy aplikacja korzysta z operacji usuwania Microsoft Identity SDK.

Gotowe. Zestaw SDK tożsamości firmy Microsoft będzie teraz udostępniać poświadczenia dla wszystkich aplikacji. Lista kont również będzie współużytkowana przez wystąpienia aplikacji.

## <a name="sso-through-authentication-broker-on-ios"></a>Logowanie jednokrotne za pośrednictwem brokera uwierzytelniania w systemie iOS

MSAL zapewnia obsługę uwierzytelniania obsługiwanego przez brokera Microsoft Authenticator. Microsoft Authenticator zapewnia Logowanie jednokrotne dla zarejestrowanych urządzeń usługi AAD, a także pomaga aplikacji stosować zasady dostępu warunkowego.

Poniższe kroki umożliwiają włączenie logowania jednokrotnego przy użyciu brokera uwierzytelniania dla aplikacji:

1. Zarejestruj format URI przekierowania zgodnego z brokerem dla aplikacji w informacji. plist aplikacji. Format identyfikatora URI przekierowania zgodnego z brokerem to `msauth.<app.bundle.id>://auth` . Zastąp element "<app.bundle.id>" "IDENTYFIKATORem pakietu aplikacji. Na przykład:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Dodaj następujące schematy do informacji o aplikacji. plist w obszarze `LSApplicationQueriesSchemes` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Dodaj następujący `AppDelegate.m` plik do pliku, aby obsłużyć wywołania zwrotne:

    Cel-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Adres
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Jeśli używasz Xcode 11**, zamiast tego należy umieścić w pliku wywołanie zwrotne MSAL `SceneDelegate` .
W przypadku obsługi UISceneDelegate i UIApplicationDelegate w celu zapewnienia zgodności ze starszymi wersjami systemu iOS należy umieścić wywołanie zwrotne w obu plikach.

Cel-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Adres

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)