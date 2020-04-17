---
title: MsAL dla systemu iOS & macOS tutorial - Platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak aplikacje dla systemu iOS i macOS (Swift) mogą wywoływać interfejs API, który wymaga tokenów dostępu przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1251049add8c9d3c71b6ba13aff24e086613e84b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450959"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Logowanie użytkowników i wywoływanie programu Microsoft Graph z aplikacji na iOS lub macOS

W tym samouczku dowiesz się, jak zintegrować aplikację systemu iOS lub macOS z platformą tożsamości firmy Microsoft. Aplikacja zaloguje się do użytkownika, pobierze token dostępu, aby wywołać interfejs API programu Microsoft Graph i zażąda interfejsu API programu Microsoft Graph.  

Po zakończeniu przewodnika aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych lub szkolnych od dowolnej firmy lub organizacji korzystającej z usługi Azure Active Directory.

>[!NOTE]
> Jeśli jesteś nowy na platformie tożsamości firmy Microsoft, zalecamy rozpoczęcie od [logowania użytkowników i wywołanie interfejsu API programu Microsoft Graph z aplikacji dla systemu iOS lub macOS](quickstart-v2-ios.md).

## <a name="how-this-tutorial-works"></a>Jak działa ten samouczek

![Pokazuje, jak działa przykładowa aplikacja generowana przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikacja w tym samouczku zaloguje się na użytkowników i pobierze dane w ich imieniu.  Te dane będą dostępne za pośrednictwem chronionego interfejsu API (microsoft graph API w tym przypadku), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Aplikacja zaloguje się do użytkownika za pośrednictwem przeglądarki lub programu Microsoft Authenticator.
* Użytkownik końcowy zaakceptuje uprawnienia żądane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API programu Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź programu Microsoft Graph.

W tym przykładzie użyto biblioteki uwierzytelniania firmy Microsoft (MSAL) do zaimplementowania uwierzytelniania. Usługa MSAL automatycznie odnawia tokeny, dostarcza logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

Ten samouczek ma zastosowanie zarówno do aplikacji dla systemu iOS, jak i macOS. Należy zauważyć, że niektóre kroki różnią się między tymi dwiema platformami. 

## <a name="prerequisites"></a>Wymagania wstępne

- XCode w wersji 11.x lub wyższej jest wymagane do utworzenia aplikacji w tym przewodniku. XCode można pobrać ze [strony internetowej iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Adres URL pobierania XCode").
- Biblioteka uwierzytelniania firmy Microsoft[(MSAL.framework).](https://github.com/AzureAD/microsoft-authentication-library-for-objc) Można użyć menedżera zależności lub dodać bibliotekę ręcznie. Poniższe instrukcje pokazują, jak to zrobić.

W tym samouczku utworzy się nowy projekt. Jeśli chcesz pobrać ukończony samouczek zamiast tego, pobierz kod:
- [Przykładowy kod systemu iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Przykładowy kod systemu macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz xcode i wybierz **pozycję Utwórz nowy projekt Xcode**.
2. W przypadku aplikacji dla systemu iOS wybierz pozycję**Pojedyncza aplikacja do widoku** **systemu iOS** > i wybierz pozycję **Dalej**.
3. W przypadku aplikacji dla systemu macOS wybierz pozycję **aplikacja macOS** > **Cocoa i** wybierz pozycję **Dalej**.
4. Podaj nazwę produktu.
5. Ustaw **język** na **Szybki** i wybierz pozycję **Dalej**.
6. Wybierz folder do utworzenia aplikacji, a następnie kliknij przycisk **Utwórz**.

## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Przejdź do witryny [Azure Portal](https://aka.ms/MobileAppReg).
2. Otwórz blok Rejestracje aplikacji i kliknij **+Nowa rejestracja**.
3. Wprowadź **nazwę** aplikacji, a następnie bez ustawiania identyfikatora URI przekierowania.
4. Wybieranie **kont w dowolnym katalogu organizacji (dowolny katalog usługi Azure AD — wielodostępne) i osobistych kont Microsoft (np.** **Supported account types**
5. Kliknij **pozycję Zarejestruj się**
6. W sekcji **Zarządzanie** wyświetlonym okienkiem wybierz pozycję **Uwierzytelnianie**.

7. Kliknij **pozycję Wypróbuj nowe środowisko** w górnej części ekranu, aby otworzyć nowe środowisko rejestracji aplikacji, a następnie kliknij **+Nowa rejestracja** > **+ Dodaj platformę** > **iOS/macOS**.
    - Wprowadź identyfikator pakietu projektu. Jeśli kod został pobrany, `com.microsoft.identitysample.MSALiOS`jest to . Jeśli tworzysz własny projekt, wybierz projekt w xcode i otwórz kartę **Ogólne.** Identyfikator pakietu pojawi się w sekcji **Tożsamość.**
8. Kliknij `Configure` i zapisz **konfigurację MSAL,** która pojawia się na stronie **konfiguracji MSAL,** aby można było ją wprowadzić podczas późniejszej konfiguracji aplikacji.  Kliknij przycisk **Gotowe**.

## <a name="add-msal"></a>Dodawanie usługi MSAL

Wybierz jeden z następujących sposobów instalowania biblioteki MSAL w aplikacji:

### <a name="cocoapods"></a>CocoaPods

1. Jeśli używasz [CocoaPods,](https://cocoapods.org/) `MSAL` zainstaluj najpierw, tworząc pusty `podfile` plik o nazwie w `.xcodeproj` tym samym folderze co plik projektu. Dodaj następujące `podfile`elementy do:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Zamień `<your-target-here>` na nazwę projektu.
3. W oknie terminala przejdź do folderu `podfile` zawierającego `pod install` utworzone i uruchomione dane, aby zainstalować bibliotekę MSAL.
4. Zamknij xcode `<your project name>.xcworkspace` i otwórz, aby ponownie załadować projekt w xcode.

### <a name="carthage"></a>Kartagina

Jeśli używasz [Kartathage](https://github.com/Carthage/Carthage), `MSAL` zainstaluj, dodając `Cartfile`go do:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

W oknie terminala w tym samym `Cartfile`katalogu co zaktualizowany uruchom następujące polecenie, aby kartathage zaktualizować zależności w projekcie.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Ręczne

Można również użyć Git Submodule lub sprawdź najnowszą wersję do użycia jako framework w aplikacji.

## <a name="add-your-app-registration"></a>Dodawanie rejestracji aplikacji

Następnie dodamy rejestrację aplikacji do Twojego kodu. 

Najpierw dodaj następującą instrukcję importu `ViewController.swift`do górnej `AppDelegate.swift` `SceneDelegate.swift` części , a także lub plików:

```swift
import MSAL
```

Następnie dodaj następujący `ViewController.swift` kod `viewDidLoad()`przed:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Jedyną wartością, którą należy zmodyfikować `kClientID`powyżej, jest wartość przypisana do [identyfikatora aplikacji.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id) Ta wartość jest częścią danych konfiguracji MSAL, które zostały zapisane podczas kroku na początku tego samouczka, aby zarejestrować aplikację w witrynie Azure portal.

## <a name="for-ios-only-configure-url-schemes"></a>Tylko w systemie iOS konfigurowanie schematów adresów URL

W tym kroku zarejestrujesz się, `CFBundleURLSchemes` aby użytkownik mógł zostać przekierowany z powrotem do aplikacji po zalogowaniu. Przy okazji, `LSApplicationQueriesSchemes` umożliwia również aplikacji, aby korzystać z Microsoft Authenticator.

W xcode `Info.plist` otwórz jako plik kodu źródłowego i `<dict>` dodaj następujące wnętrze sekcji. Zamień `[BUNDLE_ID]` na wartość używaną w witrynie Azure portal, `com.microsoft.identitysample.MSALiOS`która po pobraniu kodu jest . Jeśli tworzysz własny projekt, wybierz projekt w xcode i otwórz kartę **Ogólne.** Identyfikator pakietu pojawi się w sekcji **Tożsamość.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Tylko w systemie macOS skonfiguruj piaskownicę aplikacji

1. Przejdź do **karty** > Ustawienia projektu Xcode > Możliwości**Piaskownica aplikacji**
2. Zaznacz pole wyboru **Połączenia wychodzące (klient).** 

## <a name="create-your-apps-ui"></a>Tworzenie interfejsu użytkownika aplikacji

Teraz utwórz interfejs użytkownika, który zawiera przycisk, aby wywołać interfejs API programu Microsoft Graph, inny do `ViewController`wylogowania i widok tekstu, aby wyświetlić pewne dane wyjściowe, dodając następujący kod do klasy:

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

### <a name="macos-ui"></a>Interfejs użytkownika systemu macOS

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

Następnie, również `ViewController` wewnątrz klasy, `viewDidLoad()` zastąp metodę na:

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

## <a name="use-msal"></a>Korzystanie z usługi MSAL

### <a name="initialize-msal"></a>Inicjowanie usługi MSAL

Dodaj następującą `initMSAL` metodę `ViewController` do klasy:

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

Dodaj następującą `initMSAL` metodę `ViewController` after do klasy.

### <a name="ios-code"></a>Kod systemu iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>Kod systemu macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Tylko w przypadku systemu iOS obsłużyć wywołanie zwrotne logowania

Otwórz plik `AppDelegate.swift`. Aby obsłużyć wywołanie `MSALPublicClientApplication.handleMSALResponse` zwrotne `appDelegate` po zalogowaniu, dodaj do klasy w ten sposób:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Jeśli używasz Xcode 11**, należy umieścić wywołania zwrotnego `SceneDelegate.swift` MSAL zamiast.
Jeśli obsługujesz zarówno UISceneDelegate i UIApplicationDelegate dla zgodności ze starszymi systemami iOS, wywołania zwrotnego MSAL należy umieścić w obu plikach.

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

#### <a name="acquire-tokens"></a>Nabywanie tokenów

Teraz możemy zaimplementować logikę przetwarzania interfejsu użytkownika aplikacji i uzyskać tokeny interaktywnie za pośrednictwem usługi MSAL.

MSAL udostępnia dwie podstawowe metody uzyskiwania tokenów: `acquireTokenSilently()` i: `acquireTokenInteractively()` 

- `acquireTokenSilently()`próbuje zalogować się do użytkownika i uzyskać tokeny bez interakcji z użytkownikiem, o ile konto jest obecne. `acquireTokenSilently()`wymaga podania `MSALAccount` prawidłowości, które można pobrać przy użyciu jednego z interfejsów API wyliczania konta MSAL. W tym `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` przykładzie użyto do pobrania rachunku bieżącego. 

- `acquireTokenInteractively()`zawsze pokazuje interfejs użytkownika podczas próby zalogowania się użytkownika. Może używać sesyjnych plików cookie w przeglądarce lub konta w autycyjnie firmy Microsoft w celu zapewnienia interaktywnego środowiska jednokrotnego.

Dodaj następujący kod `ViewController` do klasy:

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

#### <a name="get-a-token-interactively"></a>Interaktywne uzyskanie tokenu

Poniższy kod pobiera token po raz pierwszy, tworząc obiekt `MSALInteractiveTokenParameters` i wywołując `acquireToken`. Następnie dodasz kod, który:

1. Tworzy `MSALInteractiveTokenParameters` z zakresami.
2. Wywołania `acquireToken()` z utworzonymi parametrami.
3. Obsługuje błędy. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem obsługi błędów w systemie MSAL dla systemu iOS i macOS](msal-handling-exceptions.md).
4. Obsługuje udane sprawy.

Dodaj następujący kod `ViewController` do klasy.

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


#### <a name="get-a-token-silently"></a>Uzyskaj token po cichu

Aby uzyskać zaktualizowany token dyskretnie, `ViewController` dodaj następujący kod do klasy. Tworzy obiekt `MSALSilentTokenParameters` i `acquireTokenSilent()`wywołuje:

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

### <a name="call-the-microsoft-graph-api"></a>Wywoływanie interfejsu API programu Microsoft Graph 

Po uzyskaniu tokenu aplikacja może go użyć w nagłówku HTTP, aby złożyć autoryzowane żądanie do programu Microsoft Graph:

| klawisz nagłówka    | value                 |
| ------------- | --------------------- |
| Autoryzacja | > \<tokenu dostępu na okaziciela |

Dodaj następujący kod `ViewController` do klasy:

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

Zobacz [Interfejs API programu Microsoft Graph,](https://graph.microsoft.com) aby dowiedzieć się więcej o interfejsie API programu Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Używanie funkcji MSAL do wylogowywania się

Następnie dodaj obsługę wylogowywania się.

> [!Important]
> Wylogowanie się z usługi MSAL powoduje usunięcie wszystkich znanych informacji o użytkowniku z aplikacji, a także usunięcie aktywnej sesji na urządzeniu, jeśli jest to dozwolone przez konfigurację urządzenia. Opcjonalnie można również wylogować użytkownika z przeglądarki.

Aby dodać możliwość wylogowania, dodaj `ViewController` następujący kod wewnątrz klasy. 

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

Domyślnie usługa MSAL buforuje tokeny aplikacji w pęku kluczy systemu iOS lub macOS. 

Aby włączyć buforowanie tokenów:
1. Upewnij się, że aplikacja jest prawidłowo podpisana
2. Przejdź do **karty** > Ustawienia projektu Xcode >**Włącz udostępnianie pęku kluczy**
3. Kliknij **+** i wprowadź następujący wpis **Grupy pęku kluczy:** `com.microsoft.adalcache` 3.a Dla systemu iOS, wprowadź 3.b Dla macOS enter`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Dodawanie metod pomocniczych
Dodaj następujące metody pomocnika `ViewController` do klasy, aby zakończyć próbkę.

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

### <a name="macos-ui"></a>Interfejs użytkownika systemu macOS:

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

Użyj następującego kodu, aby odczytać bieżącą konfigurację urządzenia, w tym czy urządzenie jest skonfigurowane jako udostępnione:

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

### <a name="multi-account-applications"></a>Aplikacje z wieloma kontami

Ta aplikacja jest zbudowana dla scenariusza pojedynczego konta. Usługa MSAL obsługuje również scenariusze z wieloma kontami, ale wymaga dodatkowej pracy z aplikacji. Należy utworzyć interfejs użytkownika, aby pomóc użytkownikom wybrać konto, którego mają używać dla każdej akcji, która wymaga tokenów. Alternatywnie aplikacja może zaimplementować heurystykę, aby wybrać konto, którego chcesz użyć, odpytując wszystkie konta z usługi MSAL. Na przykład `accountsFromDeviceForParameters:completionBlock:` zobacz [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Tworzenie i wdrażanie aplikacji na urządzeniu testowym lub symulatorze. Powinieneś mieć możliwość logowania się i uzyskania tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, zostanie poproszony przez firmę Microsoft identity o wyrażenie zgody na żądane uprawnienia.  Podczas gdy większość użytkowników jest w stanie wyrazić zgodę, niektórzy dzierżawcy usługi Azure AD wyłączyli zgodę użytkownika, co wymaga od administratorów zgody w imieniu wszystkich użytkowników. Aby obsługiwać ten scenariusz, zarejestruj zakresy aplikacji w witrynie Azure portal.

Po zalogowaniu aplikacja wyświetli dane zwrócone z punktu `/me` końcowego programu Microsoft Graph.

## <a name="get-help"></a>Uzyskiwanie pomocy

Jeśli masz problemy z tym samouczkiem lub platformą tożsamości firmy Microsoft, odwiedź [stronę Pomocy i pomocy technicznej.](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
