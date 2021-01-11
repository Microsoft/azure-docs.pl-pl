---
title: Korzystanie z identyfikatorów URI przekierowania z MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla usługi ObjectiveC (MSAL for iOS i macOS) oraz biblioteką uwierzytelniania usługi Azure AD dla ObjectiveC (ADAL. ObjC) i sposób migracji między nimi.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064202"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Używanie identyfikatorów URI przekierowania z biblioteką uwierzytelniania firmy Microsoft dla systemów iOS i macOS

Po uwierzytelnieniu użytkownika usługa Azure Active Directory (Azure AD) wysyła token do aplikacji przy użyciu identyfikatora URI przekierowania zarejestrowanego w aplikacji usługi Azure AD.

Biblioteka Microsoft Authentication Library (MSAL) wymaga, aby identyfikator URI przekierowania był zarejestrowany w aplikacji usługi Azure AD w określonym formacie. MSAL używa domyślnego identyfikatora URI przekierowania, jeśli nie zostanie on określony. Format to `msauth.[Your_Bundle_Id]://auth` .

Domyślny format URI przekierowania działa w przypadku większości aplikacji i scenariuszy, w tym uwierzytelniania obsługiwanego przez brokera i widoku sieci Web systemu. Użyj domyślnego formatu, jeśli jest to możliwe.

Jednak może zajść potrzeba zmiany identyfikatora URI przekierowania dla zaawansowanych scenariuszy, zgodnie z poniższym opisem.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenariusze, które wymagają innego identyfikatora URI przekierowania

### <a name="cross-app-single-sign-on-sso"></a>Logowanie jednokrotne (SSO) między aplikacjami

Aby platforma tożsamości firmy Microsoft mogła udostępniać tokeny w aplikacjach, każda aplikacja musi mieć ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator podany podczas rejestrowania aplikacji w portalu (nie identyfikator pakietu aplikacji, który rejestruje się na aplikację przy użyciu firmy Apple).

Identyfikatory URI przekierowania muszą być różne dla każdej aplikacji systemu iOS. Dzięki temu usługa tożsamości firmy Microsoft może jednoznacznie identyfikować różne aplikacje, które współużytkują identyfikator aplikacji. Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w Azure Portal. Każda aplikacja w Twoim pakiecie będzie miała inny identyfikator URI przekierowania. Na przykład:

Uwzględniając następujące zarejestrowane aplikacje w Azure Portal:

* Identyfikator klienta: `ABCDE-12345` (jest to pojedynczy identyfikator klienta)
* RedirectUris: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

APP1 używa przekierowania `msauth.com.contoso.app1://auth` . \
App2 używa `msauth.com.contoso.app2://auth` . \
APP3 używa `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>Migrowanie z biblioteki ADAL do MSAL

Podczas migrowania kodu, który używał biblioteki Azure AD Authentication Library (ADAL) do MSAL, być może masz już skonfigurowany identyfikator URI przekierowania dla aplikacji. Można nadal używać tego samego identyfikatora URI przekierowania, o ile aplikacja ADAL została skonfigurowana do obsługi scenariuszy obsługiwanych przez brokera, a identyfikator URI przekierowania spełnia wymagania dotyczące formatu identyfikatora URI przekierowania MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Wymagania formatu identyfikatora URI przekierowania MSAL

* Identyfikator URI przekierowania MSAL musi mieć postać `<scheme>://host`

    Gdzie `<scheme>` jest unikatowym ciągiem identyfikującym aplikację. Jest on głównie oparty na identyfikatorze pakietu aplikacji w celu zagwarantowania unikalności. Jeśli na przykład identyfikator pakietu aplikacji to `com.contoso.myapp` , identyfikator URI przekierowania będzie miał postać: `msauth.com.contoso.myapp://auth` .

    Jeśli przeprowadzasz migrację z biblioteki ADAL, identyfikator URI przekierowania prawdopodobnie będzie miał następujący format: `<scheme>://[Your_Bundle_Id]` , gdzie `scheme` jest unikatowym ciągiem. Ten format będzie nadal działał w przypadku korzystania z MSAL.

* `<scheme>` musi być zarejestrowany w info. plist `CFBundleURLTypes > CFBundleURLSchemes` aplikacji.  W tym przykładzie został otwarty program info. plist jako kod źródłowy:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```

MSAL sprawdzi, czy identyfikator URI przekierowania prawidłowo rejestruje i zwróci błąd, jeśli nie jest.
    
* Jeśli chcesz używać linków uniwersalnych jako identyfikatora URI przekierowania, `<scheme>` musi być `https` i nie musi być zadeklarowany w `CFBundleURLSchemes` . Zamiast tego należy skonfigurować aplikacje i domenę na podstawie instrukcji firmy Apple w obszarze [uniwersalne linki dla deweloperów](https://developer.apple.com/ios/universal-links/) i wywołać `handleMSALResponse:sourceApplication:` metodę, `MSALPublicClientApplication` gdy aplikacja zostanie otwarta przy użyciu uniwersalnego łącza.

## <a name="use-a-custom-redirect-uri"></a>Użyj niestandardowego identyfikatora URI przekierowania

Aby użyć niestandardowego identyfikatora URI przekierowania, Przekaż `redirectUri` parametr do `MSALPublicClientApplicationConfig` i przekaż ten obiekt do `MSALPublicClientApplication` momentu zainicjowania obiektu. Jeśli identyfikator URI przekierowania jest nieprawidłowy, inicjator zwróci `nil` i ustawi `redirectURIError` dodatkowe informacje.  Na przykład:

Cel-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Adres

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Obsługuj zdarzenie otwarcia adresu URL

Aplikacja powinna wywoływać MSAL, gdy odbierze odpowiedź za poorednictwem schematów adresów URL lub łączy uniwersalnych. Wywołaj `handleMSALResponse:sourceApplication:` metodę, `MSALPublicClientApplication` gdy aplikacja zostanie otwarta. Oto przykład dla schematów niestandardowych:

Cel-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Adres

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)
