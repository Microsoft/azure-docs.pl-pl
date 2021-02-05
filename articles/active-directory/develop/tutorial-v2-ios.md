---
title: 'Samouczek: Tworzenie aplikacji dla systemu iOS lub macOS używającej platformy tożsamości firmy Microsoft do uwierzytelniania | Azure'
titleSuffix: Microsoft identity platform
description: W tym samouczku utworzysz aplikację dla systemu iOS lub macOS, która korzysta z platformy tożsamości firmy Microsoft do logowania użytkowników i uzyskiwania tokenu dostępu w celu wywołania interfejsu API Microsoft Graph w ich imieniu.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5734844d91e95fe7d4081e5986095934ea02b4fd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584284"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Samouczek: Logowanie użytkowników i wywoływanie Microsoft Graph z aplikacji dla systemu iOS lub macOS

W tym samouczku utworzysz aplikację dla systemu iOS lub macOS, która integruje się z platformą tożsamości firmy Microsoft w celu podpisywania użytkownikom i uzyskiwania tokenu dostępu do wywoływania interfejsu API Microsoft Graph.

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory. Ten samouczek dotyczy zarówno aplikacji dla systemu iOS, jak i macOS. Niektóre kroki są różne dla obu platform.

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji dla systemu iOS lub macOS w usłudze *Xcode*
> * Zarejestruj aplikację w Azure Portal
> * Dodawanie kodu do obsługi logowania i wylogowywania użytkowników
> * Dodawanie kodu do wywoływania interfejsu API Microsoft Graph
> * Testowanie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- [Xcode 11. x +](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Jak działa aplikacja samouczków

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikacja w tym samouczku może zalogować użytkowników i pobrać dane z Microsoft Graph w ich imieniu. Dostęp do tych danych będzie uzyskiwany za pośrednictwem chronionego interfejsu API (w tym przypadku Microsoft Graph API), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Twoja aplikacja zaloguje użytkownika za pomocą przeglądarki lub Microsoft Authenticator.
* Użytkownik końcowy zaakceptuje uprawnienia wymagane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź Microsoft Graph.

Ten przykład używa biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu zaimplementowania uwierzytelniania. Usługa MSAL automatycznie odnawia tokeny, dostarcza Logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

Jeśli chcesz pobrać kompletną wersję aplikacji skompilowanej w ramach tego samouczka, możesz znaleźć obie wersje w witrynie GitHub:

- [przykład kodu dla systemu iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [przykład kodu macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz Xcode i wybierz pozycję **Utwórz nowy projekt Xcode**.
2. W przypadku aplikacji dla systemu iOS wybierz opcję  >  **aplikacja z pojedynczym widokiem** systemu iOS i wybierz pozycję **dalej**.
3. W przypadku aplikacji macOS wybierz pozycję  >  **aplikacja kakao** macOS i wybierz pozycję **dalej**.
4. Podaj nazwę produktu.
5. Ustaw **Język** na **SWIFT** i wybierz pozycję **dalej**.
6. Wybierz folder, w którym chcesz utworzyć aplikację, a następnie wybierz pozycję **Utwórz**.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. Wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)** w obszarze **obsługiwane typy kont**.
1. Wybierz pozycję **Zarejestruj**.
1. W obszarze **Zarządzanie** wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**  >  **iOS/macOS**.
1. Wprowadź identyfikator pakietu projektu. Jeśli pobrano kod, jest to `com.microsoft.identitysample.MSALiOS` . Jeśli tworzysz własny projekt, wybierz projekt w Xcode i Otwórz kartę **Ogólne** . Identyfikator pakietu zostanie wyświetlony w sekcji **tożsamość** .
1. Wybierz pozycję **Konfiguruj** i Zapisz **konfigurację MSAL** , która jest wyświetlana na stronie **Konfiguracja MSAL** , tak aby można ją było wprowadzić podczas późniejszej konfiguracji aplikacji. 
1. Kliknij **Gotowe**.

## <a name="add-msal"></a>Dodaj MSAL

Wybierz jedną z następujących metod instalacji biblioteki MSAL w aplikacji:

### <a name="cocoapods"></a>CocoaPods

1. Jeśli używasz [CocoaPods](https://cocoapods.org/), zainstaluj program, `MSAL` tworząc najpierw pusty plik o nazwie `podfile` w tym samym folderze, w którym znajduje się `.xcodeproj` plik projektu. Dodaj następujące elementy `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Zamień `<your-target-here>` na nazwę projektu.
3. W oknie terminalu przejdź do folderu, który zawiera `podfile` utworzone i uruchomione polecenie, `pod install` Aby zainstalować bibliotekę MSAL.
4. Zamknij Xcode i Otwórz, `<your project name>.xcworkspace` Aby ponownie załadować projekt w Xcode.

### <a name="carthage"></a>Carthage

Jeśli używasz [Carthage](https://github.com/Carthage/Carthage), zainstaluj go, `MSAL` dodając go do `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

W oknie terminalu w tym samym katalogu, który `Cartfile` został zaktualizowany, uruchom następujące polecenie, aby Carthage zaktualizować zależności w projekcie.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Ręczne

Możesz również użyć modułu podrzędnego Git lub zapoznaj się z najnowszą wersją, aby użyć jej jako platformy w aplikacji.

## <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

Następnie dodamy Twoją rejestrację aplikacji do Twojego kodu.

Najpierw Dodaj następującą instrukcję importu na początku `ViewController.swift` , a także `AppDelegate.swift` `SceneDelegate.swift` pliki:

```swift
import MSAL
```

Następnie Dodaj następujący kod do programu `ViewController.swift` przed `viewDidLoad()` :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Jedyna wartość, którą modyfikujesz, to wartość przypisana do `kClientID` tego [identyfikatora aplikacji](./developer-glossary.md#application-id-client-id). Ta wartość jest częścią danych konfiguracji MSAL, które zostały zapisane w ramach kroku na początku tego samouczka, aby zarejestrować aplikację w Azure Portal.

## <a name="configure-xcode-project-settings"></a>Konfigurowanie ustawień projektu Xcode

Dodaj nową grupę łańcucha kluczy do **możliwości & podpisywania** projektu. Grupa łańcucha kluczy powinna znajdować się `com.microsoft.adalcache` w systemach iOS i `com.microsoft.identity.universalstorage` macOS.

![Interfejs użytkownika Xcode przedstawiający sposób konfigurowania grupy łańcucha kluczy](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Tylko w przypadku systemu iOS Skonfiguruj schematy adresów URL

W tym kroku zostanie zarejestrowane, `CFBundleURLSchemes` Aby użytkownik mógł zostać przekierowany z powrotem do aplikacji po zalogowaniu się. Dzięki temu `LSApplicationQueriesSchemes` aplikacja może również używać Microsoft Authenticator.

W Xcode Otwórz `Info.plist` jako plik kodu źródłowego i Dodaj następujący element w `<dict>` sekcji. Zamień na `[BUNDLE_ID]` wartość użytą w Azure Portal. Jeśli pobrano kod, identyfikator pakietu to `com.microsoft.identitysample.MSALiOS` . Jeśli tworzysz własny projekt, wybierz projekt w Xcode i Otwórz kartę **Ogólne** . Identyfikator pakietu zostanie wyświetlony w sekcji **tożsamość** .

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
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Tylko dla macOS, Konfigurowanie piaskownicy aplikacji

1. Przejdź do > ustawień projektu Xcode   >  **w obszarze piaskownica aplikacji** na karcie możliwości
2. Zaznacz pole wyboru **połączenia wychodzące (klient)** .

## <a name="create-your-apps-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Teraz Utwórz interfejs użytkownika, który zawiera przycisk do wywoływania interfejsu API Microsoft Graph, drugi do wylogowania, i widok tekstu, aby wyświetlić niektóre dane wyjściowe, dodając następujący kod do `ViewController` klasy:

### <a name="ios-ui"></a>Interfejs użytkownika systemu iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>Interfejs użytkownika macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Następnie w `ViewController` klasie należy zamienić `viewDidLoad()` metodę na:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>Użyj MSAL

### <a name="initialize-msal"></a>Zainicjuj MSAL

Dodaj następującą `initMSAL` metodę do `ViewController` klasy:

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Dodaj następującą metodę po `initMSAL` metodzie do `ViewController` klasy.

### <a name="ios-code"></a>kod systemu iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>kod macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Tylko dla systemu iOS obsługa wywołania zwrotnego logowania

Otwórz plik `AppDelegate.swift`. Aby obsłużyć wywołanie zwrotne po zalogowaniu, Dodaj `MSALPublicClientApplication.handleMSALResponse` do `appDelegate` klasy podobnej do tej:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Jeśli używasz Xcode 11**, zamiast tego należy umieścić wywołanie zwrotne MSAL `SceneDelegate.swift` .
W przypadku obsługi UISceneDelegate i UIApplicationDelegate w celu zapewnienia zgodności ze starszymi wersjami systemu iOS należy umieścić wywołanie zwrotne w obu plikach.

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

#### <a name="acquire-tokens"></a>Uzyskaj tokeny

Teraz możemy zaimplementować logikę przetwarzania interfejsu użytkownika aplikacji oraz interaktywnie uzyskać tokeny za pomocą MSAL.

MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilently()` i `acquireTokenInteractively()` :

- `acquireTokenSilently()` próbuje zalogować użytkownika i uzyskać tokeny bez interakcji użytkownika, o ile konto jest obecne. `acquireTokenSilently()` wymaga podania prawidłowego, `MSALAccount` który można pobrać za pomocą jednego z interfejsów API wyliczenia kont MSAL. Ten przykład używa `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` do pobierania bieżącego konta.

- `acquireTokenInteractively()` zawsze pokazuje interfejs użytkownika podczas próby zalogowania użytkownika. Może ona używać plików cookie sesji w przeglądarce lub konta w usłudze Microsoft Authenticator w celu zapewnienia interaktywnego logowania jednokrotnego.

Dodaj następujący kod do `ViewController` klasy:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Interaktywnie Uzyskaj token

Poniższy fragment kodu pobiera token po raz pierwszy przez utworzenie `MSALInteractiveTokenParameters` obiektu i wywołanie `acquireToken` . Następnie dodasz kod, który:

1. Tworzy `MSALInteractiveTokenParameters` z zakresami.
2. Wywołania `acquireToken()` z utworzonymi parametrami.
3. Obsługuje błędy. Więcej szczegółów można znaleźć w [Przewodniku obsługi błędów MSAL dla systemów iOS i macOS](msal-error-handling-ios.md).
4. Obsługuje pomyślne przypadki.

Dodaj poniższy kod do klasy `ViewController`.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

`promptType`Właściwość służy do `MSALInteractiveTokenParameters` konfigurowania zachowania monitu o uwierzytelnienie i zgodę. Obsługiwane są następujące wartości:

- `.promptIfNecessary` (ustawienie domyślne) — użytkownik jest monitowany tylko w razie potrzeby. Obsługa logowania jednokrotnego zależy od obecności plików cookie w widoku WebView, a typ konta. Jeśli wielu użytkowników jest zalogowanych, zostanie wyświetlone środowisko wyboru konta. *Jest to zachowanie domyślne*.
- `.selectAccount` -Jeśli żaden użytkownik nie jest określony, w widoku WebView zostanie wyświetlona lista aktualnie zalogowanych kont, z których użytkownik chce wybrać.
- `.login` -Wymaga uwierzytelnienia użytkownika w widoku WebView. W przypadku określenia tej wartości w danym momencie może być zalogowane tylko jedno konto.
- `.consent` -Wymaga, aby użytkownik wyraził zgodę na bieżący zestaw zakresów dla żądania.

#### <a name="get-a-token-silently"></a>Uzyskaj token dyskretnie

Aby uzyskać zaktualizowany token w trybie dyskretnym, Dodaj następujący kod do `ViewController` klasy. Tworzy `MSALSilentTokenParameters` obiekt i wywołuje `acquireTokenSilent()` :

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Wywoływanie interfejsu API Microsoft Graph

Gdy masz token, aplikacja może używać go w nagłówku HTTP w celu uzyskania autoryzowanego żądania do Microsoft Graph:

| klucz nagłówka    | wartość                 |
| ------------- | --------------------- |
| Autoryzacja | Elementu nośnego \<access-token> |

Dodaj następujący kod do `ViewController` klasy:

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Aby dowiedzieć się więcej o interfejsie API Microsoft Graph, zobacz [Microsoft Graph interfejsu API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Użyj MSAL do wylogowania

Następnie Dodaj obsługę wylogowywania.

> [!Important]
> Wylogowanie za pomocą usługi MSAL powoduje usunięcie wszystkich znanych informacji o użytkowniku z aplikacji, a także usunięcie aktywnej sesji na ich urządzeniu, gdy jest to dozwolone przez konfigurację urządzenia. Opcjonalnie możesz również wylogować użytkownika z przeglądarki.

Aby dodać możliwość wylogowania, Dodaj następujący kod do `ViewController` klasy.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Włącz buforowanie tokenów

Domyślnie MSAL pamięci podręcznej tokeny aplikacji w pęku kluczy systemu iOS lub macOS.

Aby włączyć buforowanie tokenów:

1. Upewnij się, że aplikacja jest prawidłowo podpisana
1. Przejdź do pozycji Ustawienia projektu Xcode > **karcie możliwości**  >  **Włącz udostępnianie łańcucha kluczy**
1. Wybierz **+** i wprowadź jedną z następujących **grup łańcucha kluczy**:
    - Wykonane `com.microsoft.adalcache`
    - MacOS `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Dodawanie metod pomocnika
Dodaj następujące metody pomocnika do `ViewController` klasy, aby ukończyć próbkę.

### <a name="ios-ui"></a>Interfejs użytkownika systemu iOS:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>Interfejs użytkownika macOS:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Tylko w przypadku systemu iOS uzyskaj dodatkowe informacje o urządzeniu

Użyj poniższego kodu, aby odczytać bieżącą konfigurację urządzenia, w tym informacje o tym, czy urządzenie jest skonfigurowane jako udostępnione:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Aplikacje wielokonta

Ta aplikacja została skompilowana dla scenariusza jednego konta. MSAL obsługuje również scenariusze obejmujące wiele kont, ale wymagają pewnej dodatkowej pracy z poziomu aplikacji. Należy utworzyć interfejs użytkownika, aby ułatwić użytkownikom wybranie konta, które ma być używane dla każdej akcji wymagającej tokenów. Alternatywnie aplikacja może zaimplementować heurystykę, aby wybrać konto, które będzie używane przez wysyłanie zapytań do wszystkich kont z MSAL. Na przykład zobacz `accountsFromDeviceForParameters:completionBlock:` [interfejs API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testowanie aplikacji

Kompiluj i Wdróż aplikację na urządzeniu testowym lub symulatorze. Powinno być możliwe zalogowanie się i uzyskanie tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Gdy użytkownik loguje się po raz pierwszy, zostanie poproszony o tożsamość firmy Microsoft, aby wyrazić zgodę na wymagane uprawnienia. Chociaż większość użytkowników ma możliwość wyrażania zgody, niektórzy dzierżawy usługi Azure AD mają wyłączoną zgodę użytkownika, która wymaga od administratorów zgody w imieniu wszystkich użytkowników. Aby obsłużyć ten scenariusz, należy zarejestrować zakresy aplikacji w Azure Portal.

Po zalogowaniu aplikacja będzie wyświetlać dane zwrócone z `/me` punktu końcowego Microsoft Graph.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tworzeniu aplikacji mobilnych, które wywołują chronione interfejsy API sieci Web w naszej wieloczęściowej serii scenariuszy.

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja mobilna, która wywołuje interfejsy API sieci Web](scenario-mobile-overview.md)
