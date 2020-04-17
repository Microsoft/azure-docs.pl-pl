---
title: Szybki start platformy tożsamości firmy Microsoft iOS i macOS | Azure
description: Dowiedz się, jak zalogować się do użytkowników i wysyłać zapytania do programu Microsoft Graph w aplikacji dla systemu iOS lub macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 47485d8d9007a6cf6432b7bf401c7c1c34a9863a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536135"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Szybki start: logowanie użytkowników i wywoływanie interfejsu API programu Microsoft Graph z aplikacji dla systemu iOS lub macOS

Ten przewodnik Szybki start zawiera przykładowy kod, który pokazuje, jak natywna aplikacja systemu iOS lub macOS może używać platformy tożsamości firmy Microsoft do logowania kont osobistych, służbowych i szkolnych, pobierania tokenu dostępu i wywoływania interfejsu API programu Microsoft Graph.

Ten przewodnik Szybki start dotyczy zarówno aplikacji dla systemu iOS, jak i macOS. Niektóre kroki są potrzebne tylko dla aplikacji dla systemu iOS. Te kroki wywołują, że są one tylko dla systemu iOS.

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten szybki start](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Wymagania wstępne**
> * Kod XCode 10+
> * iOS 10+
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowe] [Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Ręczne] [Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opcja 1: Zarejestruj się i automatycznie skonfiguruj aplikację, a następnie pobierz przykładowy kod
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby zarejestrować aplikację,
> 1. Przejdź do nowego okienka [Azure portal — rejestracje aplikacji.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://aka.ms/MobileAppReg)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** wprowadź znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji podczas logowania się lub wyrażenia zgody na aplikację.
>      - Pomiń inne konfiguracje na tej stronie.
>      - Wybierz pozycję `Register`.
> 1. W sekcji **Zarządzanie** `Authentication`  >  `Add Platform`  >  `iOS`wybierz pozycję .
>      - Wprowadź ***identyfikator pakietu*** dla aplikacji. Identyfikator pakietu to tylko unikatowy ciąg, który jednoznacznie `com.<yourname>.identitysample.MSALMacOS`identyfikuje aplikację, na przykład . Zanotuj używaną wartość.
>      - Należy zauważyć, że konfiguracja systemu iOS ma również zastosowanie do aplikacji macOS.
> 1. Wybierz `Configure` i zapisz szczegóły ***konfiguracji MSAL*** w dalszej części tego przewodnika Szybki start.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1. Konfigurowanie aplikacji
> Aby przykładowy kod dla tego szybkiego startu do pracy, należy dodać identyfikator URI przekierowania zgodne z brokera Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-ios/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów

#### <a name="step-2-download-the-sample-project"></a>Krok 2: Pobierz przykładowy projekt

- [Pobierz przykład kodu dla systemu iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Pobierz przykład kodu dla systemu macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Krok 3: Instalowanie zależności

W oknie terminala przejdź do folderu z próbką pobranego kodu i uruchom, `pod install` aby zainstalować najnowszą bibliotekę MSAL.

#### <a name="step-4-configure-your-project"></a>Krok 4: Konfigurowanie projektu

> [!div renderon="docs"]
> Jeśli wybrano opcję 1 powyżej, możesz pominąć te kroki.

> [!div renderon="portal" class="sxs-lookup"]
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **ViewController.swift** i zastąp linię zaczynając od 'let kClientID' z następującym fragmentem kodu. Pamiętaj, aby zaktualizować `kClientID` wartość za pomocą identyfikatora klienta, który został zapisany podczas rejestracji aplikacji w portalu wcześniej w przewodniku Szybki start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Edytuj **ViewController.swift** i zastąp linię zaczynając od "let kAuthority" następującym fragmentem kodu:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Edytuj **ViewController.swift** i zastąp linię zaczynając od 'let kGraphEndpoint' z następującym fragmentem kodu:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Otwórz ustawienia projektu. W sekcji **Tożsamość** wprowadź **identyfikator pakietu** wprowadzony w portalu.
> 1. Tylko w przypadku systemu iOS kliknij prawym przyciskiem myszy **info.plist** i wybierz polecenie **Otwórz jako** > **kod źródłowy**.
> 1. Tylko w przypadku systemu iOS w `CFBundleURLSchemes` węźle głównym dict zastąp ***identyfikator pakietu*** wprowadzony w portalu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Zbuduj & uruchom aplikację!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> 1. Wyodrębnij plik zip i otwórz projekt w programie XCode.
> 1. Edytuj **ViewController.swift** i zastąp linię zaczynając od 'let kClientID' z następującym fragmentem kodu. Pamiętaj, aby zaktualizować `kClientID` wartość za pomocą identyfikatora klienta, który został zapisany podczas rejestracji aplikacji w portalu wcześniej w tym przewodniku Szybki start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Jeśli budujesz aplikację dla [chmur krajowych usługi Azure AD,](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)zastąp wiersz zaczynający się od "let kGraphEndpoint" i "let kAuthority" z poprawnymi punktami końcowymi. W przypadku dostępu globalnego użyj wartości domyślnych:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Inne punkty końcowe są udokumentowane [tutaj](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Na przykład, aby uruchomić szybki start za pomocą usługi Azure AD Germany, należy użyć następujących czynności:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Otwórz ustawienia projektu. W sekcji **Tożsamość** wprowadź **identyfikator pakietu** wprowadzony w portalu.
> 1. Tylko w przypadku systemu iOS kliknij prawym przyciskiem myszy **info.plist** i wybierz polecenie **Otwórz jako** > **kod źródłowy**.
> 1. Tylko w przypadku systemu iOS w `Enter_the_bundle_Id_Here` węźle głównym dict zamień ***identyfikator pakietu*** używany w portalu.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Zbuduj & uruchom aplikację!

## <a name="more-information"></a>Więcej informacji

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się więcej na temat tego przewodnika Szybki start.

### <a name="get-msal"></a>Pobierz MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) to biblioteka używana do logowania użytkowników i żądania tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Możesz dodać bibliotekę MSAL do aplikacji w następujący sposób:

```
$ vi Podfile

```
Dodaj do tego podfile(z docelowym plikiem projektu) następujące elementy:

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Uruchom polecenie instalacji CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Inicjowanie usługi MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```swift
import MSAL
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Gdzie: ||
> |---------|---------|
> | `clientId` | Identyfikator aplikacji z aplikacji zarejestrowanej w witrynie *portal.azure.com* |
> | `authority` | Punkt końcowy platformy tożsamości firmy Microsoft. W większości przypadków będzie to adres *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Przekierowanie identyfikatora URI aplikacji. Można przekazać "zero", aby użyć wartości domyślnej lub niestandardowego identyfikatora URI przekierowania. |

### <a name="for-ios-only-additional-app-requirements"></a>Tylko w przypadku systemu iOS dodatkowe wymagania dotyczące aplikacji

Aplikacja musi mieć również następujące `AppDelegate`elementy w pliku . Dzięki temu msal SDK obsługi odpowiedzi tokenu z aplikacji brokera Auth podczas uwierzytelniania.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> W systemie iOS 13+, `UISceneDelegate` jeśli `UIApplicationDelegate`przyjmiesz zamiast `scene:openURLContexts:` , umieść ten kod w wywołaniu zwrotnym (zobacz [dokumentację firmy Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Jeśli obsługujesz zarówno UISceneDelegate i UIApplicationDelegate dla zgodności ze starszymi systemami iOS, wywołania zwrotnego MSAL musi być umieszczony w obu miejscach.

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

Wreszcie, aplikacja musi `LSApplicationQueriesSchemes` mieć wpis w ***Info.plist*** obok `CFBundleURLTypes`. Próbka pochodzi z tym zawarte.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Logowanie użytkowników & tokenów żądania

Biblioteka MSAL oferuje dwie metody uzyskiwania tokenów: `acquireToken` i `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Uzyskaj token interaktywnie

Niektóre sytuacje wymagają od użytkowników interakcji z platformą tożsamości firmy Microsoft. W takich przypadkach użytkownik końcowy może być zobowiązany do wybrania swojego konta, wprowadzenia poświadczeń lub wyrażenia zgody na uprawnienia aplikacji. Na przykład:

* Gdy nowi użytkownicy logują się do aplikacji po raz pierwszy.
* Jeśli użytkownik zresetuje swoje hasło, musi wprowadzić swoje poświadczenia
* Gdy aplikacja żąda dostępu do zasobu po raz pierwszy
* Gdy wymagane są zasady usługi mfa lub inne zasady dostępu warunkowego

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Gdzie:||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla programu Microsoft `[ "<Application ID URL>/scope" ]` Graph lub niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Po cichu uzyskaj token dostępu

Aplikacje nie powinny wymagać od użytkowników logowania się za każdym razem, gdy żądają tokenu. Jeśli użytkownik już się zalogował, ta metoda umożliwia aplikacjom dyskretne żądanie tokenów.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Gdzie: ||
> |---------|---------|
> | `scopes` | Zawiera żądane zakresy (czyli `[ "user.read" ]` dla programu Microsoft `[ "<Application ID URL>/scope" ]` Graph lub niestandardowych interfejsów API sieci Web (`api://<Application ID>/access_as_user`) |
> | `account` | Konto, dla które jest wymagane token. Ten przewodnik Szybki start dotyczy aplikacji z jednym kontem. Jeśli chcesz utworzyć aplikację z wieloma kontami, musisz zdefiniować logikę, aby `accountsFromDeviceForParameters:completionBlock:` zidentyfikować konto, którego chcesz używać dla żądań tokenów przy użyciu i przekazywaniu poprawnych`accountIdentifier` |

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek dla systemu iOS i macOS, aby uzyskać kompletny przewodnik krok po kroku dotyczący tworzenia aplikacji, w tym pełne wyjaśnienie tego przewodnika Szybki start.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Dowiedz się, jak utworzyć aplikację używaną w tym przewodniku Szybki start

> [!div class="nextstepaction"]
> [Wywołanie samouczka interfejsu API wykresu dla systemu iOS i macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
