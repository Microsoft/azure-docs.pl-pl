---
title: Wysyłaj powiadomienia wypychane do aplikacji dla systemu iOS, które korzystają z usługi Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu iOS, które korzystają z usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336638"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji dla systemu iOS przy użyciu interfejsu API REST Notification Hubs

> [!div class="op_single_selector"]
> * [Obiektowy C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

W tym samouczku użyjesz platformy Azure Notification Hubs do wypychania powiadomień do aplikacji dla systemu iOS opartej na SWIFT przy użyciu [interfejsu API REST](/rest/api/notificationhubs/). Możesz również utworzyć pustą aplikację dla systemu iOS, która odbiera powiadomienia wypychane przy użyciu [usługi Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Ten samouczek przeprowadzi Cię przez następujące kroki:

> [!div class="checklist"]
> * Wygeneruj plik żądania podpisania certyfikatu.
> * Zażądaj aplikacji powiadomień wypychanych.
> * Utwórz profil aprowizacji dla aplikacji.
> * Tworzenie centrum powiadomień.
> * Skonfiguruj centrum powiadomień przy użyciu informacji dotyczących usługi APNs.
> * Połącz aplikację systemu iOS z centrum powiadomień.
> * Przetestuj rozwiązanie.

Pełny kod dla tego samouczka można znaleźć [w witrynie GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności, potrzebne są:

- Aby zapoznać się z [omówieniem Notification Hubs platformy Azure](notification-hubs-push-notification-overview.md) , jeśli nie znasz usługi.
- Aby dowiedzieć się więcej o [rejestracjach i instalacji](notification-hubs-push-notification-registration-management.md).
- Aktywne [konto dewelopera firmy Apple](https://developer.apple.com).
- Na komputerze Mac z systemem Xcode wraz z prawidłowym certyfikatem dewelopera zainstalowanym w łańcuchu kluczy.
- Fizyczne urządzenie iPhone, które można uruchomić i debugować za pomocą programu, ponieważ nie można testować powiadomień wypychanych za pomocą symulatora.
- Fizyczne urządzenie iPhone zarejestrowane w [portalu firmy Apple](https://developer.apple.com) i skojarzone z Twoim certyfikatem.
- [Subskrypcja platformy Azure](https://portal.azure.com) , w której można tworzyć zasoby i zarządzać nimi.

Nawet jeśli nie masz doświadczenia w pracy z programowaniem w systemie iOS, powinno być możliwe wykonanie kroków związanych z tworzeniem tego przykładu pierwszej zasady. Można jednak skorzystać ze znajomości następujących pojęć:

- Tworzenie aplikacji dla systemu iOS za pomocą Xcode i Swift.
- Konfigurowanie [centrum powiadomień platformy Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) dla systemu iOS.
- [Portal dla deweloperów firmy Apple](https://developer.apple.com) i [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Centrum powiadomień zostanie skonfigurowane do używania tylko trybu uwierzytelniania **piaskownicy** . Tego trybu uwierzytelniania nie należy używać w przypadku obciążeń produkcyjnych.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Łączenie aplikacji systemu iOS z centrum powiadomień

W tej sekcji utworzysz aplikację dla systemu iOS, która będzie łączyć się z centrum powiadomień.  

### <a name="create-an-ios-project"></a>Tworzenie projektu dla systemu iOS

1. W środowisku Xcode utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem).

1. Podczas ustawiania opcji dla nowego projektu:

   1. Określ **nazwę produktu** (PushDemo) i **identyfikator organizacji** (`com.<organization>`), który został użyty podczas ustawiania **identyfikatora pakietu** w portalu dla deweloperów firmy Apple.

   1. Wybierz **zespół** , dla którego został skonfigurowany **Identyfikator aplikacji** .

   1. Ustaw **Język** na **SWIFT**.

   1. Wybierz pozycję **Dalej**.

1. Utwórz nowy folder o nazwie **SupportingFiles**.

1. Utwórz nowy plik listy p o nazwie **devsettings. plist** w folderze **SupportingFiles** Pamiętaj, aby dodać ten folder do pliku **GITIGNORE** , aby nie był on zatwierdzany podczas pracy z repozytorium git. W aplikacji produkcyjnej prawdopodobnie będzie warunkowo ustawić te klucze tajne jako część zautomatyzowanego procesu kompilacji. Takie ustawienia nie zostały omówione w tym instruktażu.

1. Zaktualizuj **devsettings. plist** , aby uwzględnić następujące wpisy konfiguracji przy użyciu własnych wartości z centrum powiadomień, które zostało zainicjowane:

   | Key                            | Typ                     | Wartość                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | Ustaw            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Wymagane wartości można znaleźć, przechodząc do zasobu centrum powiadomień w Azure Portal. W szczególności wartości **Ustaw** i **notificationHubNamespace** znajdują się w prawym górnym rogu podsumowania **Essentials** na stronie **Przegląd** .

   ![Podsumowanie Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Możesz również znaleźć wartości **notificationHubKeyName** i **notificationHubKey** , przechodząc do **zasad dostępu** i wybierając odpowiednie **zasady dostępu**, na przykład `DefaultFullSharedAccessSignature`. Następnie skopiuj z **podstawowego ciągu połączenia** wartość, która jest poprzedzona `SharedAccessKeyName=` wartością dla `notificationHubKeyName` i wartość prefiksu `SharedAccessKey=` dla. `notificationHubKey`

   Parametry połączenia powinny mieć następujący format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Aby zachować prostotę, należy `DefaultFullSharedAccessSignature` określić, aby można było wysyłać powiadomienia za pomocą tokenu. W ramach tej `DefaultListenSharedAccessSignature` metody lepszym wyborem jest sytuacja, w której użytkownik chce otrzymywać tylko powiadomienia.

1. W obszarze **projekt nawigatora**wybierz **nazwę projektu** , a następnie wybierz kartę **Ogólne** .

1. Znajdź **tożsamość** , a następnie ustaw wartość **identyfikatora pakietu** tak, aby była `com.<organization>.PushDemo`zgodna, która jest wartością użytą dla **identyfikatora aplikacji** z poprzedniego kroku.

1. Znajdź **możliwości & podpisywania**, a następnie wybierz odpowiedni **zespół** dla **konta dewelopera firmy Apple**. Wartość **zespołu** powinna być zgodna z tą, w której zostały utworzone certyfikaty i profile.

1. Xcode powinien automatycznie pobrać odpowiednią wartość **profilu aprowizacji** na podstawie **identyfikatora pakietu**. Jeśli nie widzisz nowego **profilu aprowizacji** , spróbuj odświeżyć profile dla **tożsamości podpisującej** , wybierając pozycję **Xcode** > **Preferences** > **, a następnie** wybierz przycisk **Pobierz profile ręczne** , aby pobrać profile.

1. Na karcie **możliwości & podpisywania** kliknij przycisk **+ możliwość** i dwukrotnie naciśnij pozycję **powiadomienia wypychane** na liście, aby upewnić się, że **powiadomienia wypychane** są włączone.

1. Otwórz plik **AppDelegate. Swift** w celu zaimplementowania protokołu **UNUserNotificationCenterDelegate** i Dodaj następujący kod na początku klasy:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Będziesz używać tych członków później. W tym celu użyjesz elementu członkowskiego **tagów** jako części rejestracji przy użyciu **szablonu niestandardowego**. Aby uzyskać więcej informacji na temat tagów, zobacz [Tagi rejestracji](notification-hubs-tags-segment-push-message.md) i [rejestracji szablonów](notification-hubs-templates-cross-platform-push-messages.md).

1. W tym samym pliku Dodaj następujący kod do funkcji **didFinishLaunchingWithOptions** :

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Ten kod pobiera wartości ustawień z **devsettings. plist**, ustawia klasę **AppDelegate** jako delegata **UNUserNotificationCenter** , żąda autoryzacji powiadomień wypychanych, a następnie wywołuje **registerForRemoteNotifications**.

    Aby zachować prostotę, kod obsługuje *tylko system iOS 10 i nowsze*. Obsługę wcześniejszych wersji systemu operacyjnego można dodać za pomocą odpowiednich interfejsów API i podejścia, jak zwykle.

1. W tym samym pliku Dodaj następujące funkcje:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kod używa wartości **Identyfikator InstallationID** i **pushChannel** do zarejestrowania się w centrum powiadomień. W takim przypadku używasz **UIDevice. Current. identifierForVendor** , aby podać unikatową wartość, aby zidentyfikować urządzenie, a następnie sformatować **deviceToken** w celu zapewnienia odpowiedniej wartości **pushChannel** . Funkcja **showAlert** istnieje po prostu, aby wyświetlić tekst komunikatu dla celów demonstracyjnych.

1. W pliku **AppDelegate. Swift** Dodaj funkcje **willPresent** i **didReceive** do **UNUserNotificationCenterDelegate**. Te funkcje wyświetlają alert, gdy zostanie powiadomiony o tym, że aplikacja działa odpowiednio na pierwszym planie lub w tle.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Dodaj instrukcje Print na dole funkcji **didRegisterForRemoteNotificationsWithDeviceToken** , aby sprawdzić, czy są przypisane wartości **Identyfikator InstallationID** i **pushChannel** .

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Utwórz foldery **modele**, **usługi**i **Narzędzia** dla podstawowych składników, które zostaną później dodane do projektu.

1. Sprawdź, czy projekt jest kompilowany i uruchamiany na urządzeniu fizycznym. Powiadomień wypychanych nie można przetestować za pomocą symulatora.

### <a name="create-models"></a>Tworzenie modeli

W tym kroku utworzysz zestaw modeli reprezentujący ładunki [interfejsu API REST Notification Hubs](/rest/api/notificationhubs/) i przechowywania wymaganych danych tokenów sygnatury dostępu współdzielonego (SAS).

1. Dodaj nowy plik SWIFT o nazwie **PushTemplate. Swift** do folderu **models** . Ten model udostępnia strukturę reprezentującą **treść** pojedynczego szablonu jako część ładunku **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Dodaj nowy plik SWIFT o nazwie **DeviceInstallation. Swift** do folderu **models** . Ten plik definiuje strukturę reprezentującą ładunek do tworzenia lub aktualizowania **instalacji urządzenia**. Dodaj następujący kod do pliku:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Dodaj nowy plik SWIFT o nazwie **TokenData. Swift** do folderu **models** . Ten model będzie używany do przechowywania tokenu sygnatury dostępu współdzielonego wraz z jego wygaśnięciem.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Generowanie tokenu SAS

Notification Hubs używać tej samej infrastruktury zabezpieczeń co Azure Service Bus. Aby wywołać interfejs API REST, należy [programowo wygenerować token sygnatury dostępu współdzielonego](/rest/api/eventhub/generate-sas-token) , który może być używany w nagłówku **autoryzacji** żądania.  

Token otrzymany będzie w następującym formacie:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Sam proces obejmuje te same sześć kluczowych kroków:  

1. Obliczanie daty wygaśnięcia w formacie [czasu epoki systemu UNIX](https://en.wikipedia.org/wiki/Unix_time) , co oznacza liczbę sekund, które upłynęły od północy czasu uniwersalnego skoordynowanego, 1 stycznia 1970.
1. Formatowanie **ResourceUrl** , który reprezentuje zasób, do którego próbujesz uzyskać dostęp, tak aby był zakodowany za pomocą wartości procentowej i małych liter. **ResourceUrl** ma formularz `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Przygotowywanie **StringToSign**, który jest sformatowany jako `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Obliczanie i kodowanie w formacie base64 **sygnatury** przy użyciu skrótu HMAC-SHA256 wartości **StringToSign** . Wartość skrótu jest używana z **kluczową** częścią **parametrów połączenia** dla odpowiedniej **reguły autoryzacji**.
1. Formatowanie **sygnatury** zakodowanej algorytmem Base64 w celu zakodowania procentowo.
1. Konstruowanie tokenu w oczekiwanym formacie przy użyciu wartości **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**i **UrlEncodedResourceUrl** .

Zapoznaj się z [dokumentacją Azure Service Bus](../service-bus-messaging/service-bus-sas.md) , aby uzyskać bardziej szczegółowe omówienie sygnatury dostępu współdzielonego i sposobu ich używania Azure Service Bus i Notification Hubs.

Na potrzeby tego przykładu można użyć biblioteki **CommonCrypto** typu open source firmy Apple w celu ułatwienia mieszania sygnatury. Ponieważ jest to biblioteka języka C, nie jest dostępna w usłudze SWIFT z poziomu pola. Bibliotekę można udostępnić przy użyciu nagłówka mostkowania.

Aby dodać i skonfigurować nagłówek mostkowania:

1. W Xcode wybierz pozycję **plik** > **Nowy** > **File** > plik**nagłówkowy**. Nazwij plik nagłówka **BridgingHeader. h**.

1. Edytuj plik, aby zaimportować **CommonHMAC. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Zaktualizuj **Ustawienia kompilacji** elementu docelowego, aby odwoływać się do nagłówka mostkowania:

   1. Naciśnij projekt **PushDemo** i przewiń w dół do sekcji **SWIFT kompilatora** .

   1. Upewnij się, że opcja **Zainstaluj w nagłówku zgodności** dla języka C ma ustawioną **wartość tak**.

   1. Wprowadź ścieżkę `'<ProjectName>/BridgingHeader.h'` pliku do opcji **nagłówka mostkowania "cel-C"** . To jest ścieżka do pliku z naszym nagłówkiem łączącym.

   Jeśli nie możesz znaleźć tych opcji, upewnij się, że masz wybrany widok **wszystkie** , a nie **podstawowy** lub **dostosowany**.

   Istnieje wiele dostępnych bibliotek otoki typu open-source innych firm, które mogą ułatwić korzystanie z **CommonCrypto** . Jednak Omówienie takich bibliotek wykracza poza zakres tego artykułu.

1. Dodaj nowy plik SWIFT o nazwie **TokenUtility. Swift** w folderze **Utilities** i Dodaj następujący kod:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   To narzędzie hermetyzuje logikę odpowiedzialną za generowanie tokenu SAS.

   Jak wspomniano wcześniej, funkcja **getSasToken** organizuje czynności wysokiego poziomu wymagane do przygotowania tokenu. Funkcja zostanie wywołana przez usługę instalacji w dalszej części tego samouczka.

   Pozostałe dwie funkcje są wywoływane przez funkcję **getSasToken** : **sha256HMac** do przetwarzania sygnatur i **urlEncodedString** na potrzeby kodowania skojarzonego z nim ciągu adresu URL. Funkcja **urlEncodedString** jest wymagana, ponieważ nie jest możliwe uzyskanie wymaganych danych wyjściowych przy użyciu wbudowanej funkcji **addingPercentEncoding** .

   [Zestaw SDK usługi Azure Storage dla systemu iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) to doskonały przykład sposobu podejścia do tych operacji w celu osiągnięcia tego celu. Więcej informacji o tokenach SAS Azure Service Bus można znaleźć w [dokumentacji Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

1. W **AppDelegate. Swift**Dodaj następujący kod do funkcji *didRegisterForRemoteNotificationsWithDeviceToken* , aby sprawdzić, czy **TokenUtility. getSasToken** generuje prawidłowy token
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Pamiętaj, aby zastąpić wartości symboli zastępczych w ciągu **BaseAddress** własnym

### <a name="verify-the-sas-token"></a>Weryfikowanie tokenu SAS

Przed zaimplementowaniem usługi instalacji na kliencie Sprawdź, czy nasza aplikacja prawidłowo generuje token SYGNATURy dostępu współdzielonego za pomocą wybranego narzędzia HTTP. Na potrzeby tego samouczka wybrane **Narzędzie zostanie wyszukane.**

Zanotuj wartości **Identyfikator InstallationID** i **tokenów** , które są generowane przez aplikację.

Wykonaj następujące kroki, aby wywołać interfejs API **instalacji** :

1. W programie **Poster**Otwórz nową kartę.

1. Ustaw żądanie, aby **uzyskać** i określić następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądania w następujący sposób:

   | Key           | Wartość            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autoryzacja | \<sasToken>       |
   | x-MS-Version  | 2015-01          |

1. Wybierz przycisk **kod** , który pojawia się w prawym górnym rogu przycisku **Zapisz** . Żądanie powinno wyglądać podobnie do poniższego przykładu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Kliknij przycisk **Wyślij**.

W tym momencie nie istnieje Rejestracja dla określonego **Identyfikator InstallationID** . Weryfikacja powinna spowodować, że odpowiedź "404 nie zostanie znaleziona" zamiast odpowiedzi "401 Brak autoryzacji". Ten wynik powinien potwierdzić, że token sygnatury dostępu współdzielonego został zaakceptowany.

### <a name="implement-the-installation-service-class"></a>Implementowanie klasy usługi instalacji

Następnie zaimplementujemy podstawową otokę wokół [interfejsu API REST instalacji](/rest/api/notificationhubs/create-overwrite-installation).  

Dodaj nowy plik SWIFT o nazwie **NotificationRegistrationService. Swift** w folderze **usługi** , a następnie Dodaj następujący kod do tego pliku:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Szczegóły wymagania są udostępniane w ramach inicjalizacji. Tagi i szablony są opcjonalnie przesyłane do funkcji **register** w celu utworzenia części ładunku JSON **instalacji urządzenia** .  

Funkcja **register** wywołuje inne funkcje prywatne w celu przygotowania żądania. Po odebraniu odpowiedzi zostaje wywołana i wskazuje, czy rejestracja zakończyła się pomyślnie.  

Punkt końcowy żądania jest konstruowany przez funkcję **GetBaseAddress —** . Konstrukcja używa *przestrzeni nazw* i *nazwy* centrum powiadomień, które zostały podane podczas inicjalizacji.  

Funkcja **getSasToken** sprawdza, czy aktualnie przechowywany token jest prawidłowy. Jeśli token jest nieprawidłowy, funkcja wywołuje **TokenUtility** w celu wygenerowania nowego tokenu, a następnie zapisuje go przed zwróceniem wartości.

Na koniec **encodeToJson** konwertuje odpowiednie obiekty modelu na kod JSON do użycia jako część treści żądania.

### <a name="invoke-the-notification-hubs-rest-api"></a>Wywoływanie interfejsu API REST Notification Hubs

Ostatnim krokiem jest aktualizowanie **AppDelegate** , aby użyć **NotificationRegistrationService** do zarejestrowania się w naszym **NotificationHub**.

1. Otwórz **AppDelegate. Swift** i Dodaj zmienne na poziomie klasy, aby zapisać odwołanie do **NoficiationRegistrationService** i rodzajowego **PushTemplate**:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. W tym samym pliku zaktualizuj funkcję **didRegisterForRemoteNotificationsWithDeviceToken** , aby zainicjować **NotificationRegistrationService** z parametrami wymaganymi, a następnie wywołaj funkcję **register** .

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Aby zachować prostotę, należy użyć kilku instrukcji Print, aby zaktualizować okno dane wyjściowe z wynikiem operacji **rejestracji** .

1. Teraz Kompiluj i uruchamiaj aplikację na urządzeniu fizycznym. Powinna zostać wyświetlona wartość "zarejestrowane" w oknie danych wyjściowych.

## <a name="test-the-solution"></a>Testowanie rozwiązania

Nasza aplikacja na tym etapie jest zarejestrowana w usłudze **NotificationHub** i może odbierać powiadomienia wypychane. W programie Xcode Zatrzymaj debuger i Zamknij aplikację, jeśli jest aktualnie uruchomiona. Następnie sprawdź, czy szczegóły **instalacji urządzenia** są zgodnie z oczekiwaniami, i czy nasza aplikacja może teraz odbierać powiadomienia wypychane.  

### <a name="verify-the-device-installation"></a>Weryfikowanie instalacji urządzenia

Teraz można wykonać to samo żądanie, jak wcześniej **przy użyciu programu** do [weryfikacji tokenu SAS](#verify-the-sas-token). Przy założeniu, że token sygnatury dostępu współdzielonego nie wygasł, odpowiedź powinna teraz zawierać podane wcześniej informacje o instalacji, takie jak szablony i Tagi.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Jeśli poprzedni **token sygnatury dostępu współdzielonego** utracił ważność, można dodać **punkt przerwania** do **wiersza 24** klasy **TokenUtility** , aby uzyskać nowy **token sygnatury dostępu współdzielonego** i zaktualizować nagłówek **autoryzacji** za pomocą tej nowej wartości.

### <a name="send-a-test-notification-azure-portal"></a>Wyślij testowe powiadomienie (Azure Portal)

Najszybszym sposobem na przetestowanie, że teraz możesz otrzymywać powiadomienia, jest przechodzenie do centrum powiadomień w Azure Portal:

1. W Azure Portal przejdź do karty **Przegląd** w centrum powiadomień.

1. Wybierz pozycję **Testuj test**, która znajduje się powyżej podsumowania **Essentials** w lewym górnym rogu okna portalu:

    ![Przycisk wysyłania testu podsumowania podstawy Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Wybierz **szablon niestandardowy** z listy **platformy** .

1. Wprowadź **12345** dla **wyrażenia tagu Wyślij do**. Ten tag został poprzednio określony w instalacji.

1. Opcjonalnie można edytować **komunikat** w ładunku JSON:

    ![Notification Hubs wysyłanie testowe](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Wybierz pozycję **Wyślij**. Portal powinien wskazać, czy powiadomienie zostało pomyślnie wysłane do urządzenia:

    ![Wyniki wysyłania testu Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Przy założeniu, że aplikacja nie jest uruchomiona na pierwszym planie, należy również sprawdzić powiadomienie w **centrum powiadomień** na urządzeniu. Naciśnięcie powiadomienia powinno otworzyć aplikację i wyświetlić alert.

    ![Przykład odebranego powiadomienia](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Wyślij powiadomienie testowe (przewoźnik poczty)

Powiadomienia można wysyłać za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) przy użyciu programu **Poster**, który może być wygodniejszym sposobem przetestowania.

1. Otwórz nową kartę w programie **Poster**.

1. Ustaw żądanie na **wpis**, a następnie wprowadź następujący adres:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Skonfiguruj nagłówki żądania w następujący sposób:

   | Key                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | Application/JSON; charset = utf-8 |
   | Autoryzacja                  | \<sasToken>                     |
   | ServiceBusNotification — format  | szablon                       |
   | Tagi                           | "12345"                        |

1. Skonfiguruj **treść** żądania w taki sposób, aby korzystała z **surowego pliku JSON (Application. JSON)** z następującym ładunkiem JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Wybierz przycisk **kod** , który znajduje się poniżej przycisku **Zapisz** w prawym górnym rogu okna. Żądanie powinno wyglądać podobnie do poniższego przykładu:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Kliknij przycisk **Wyślij**.

Należy uzyskać kod stanu powodzenia i otrzymać powiadomienie **201** na urządzeniu klienckim.

## <a name="next-steps"></a>Następne kroki
Masz teraz podstawową aplikację SWIFT dla systemu iOS połączoną z centrum powiadomień za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/) i można wysyłać i odbierać powiadomienia. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie usługi Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Interfejsy API REST Notification Hubs](/rest/api/notificationhubs/)
- [Notification Hubs SDK dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK w witrynie GitHub](https://github.com/Azure/azure-notificationhubs)
- [Rejestrowanie przy użyciu zaplecza aplikacji](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Praca ze znacznikami](notification-hubs-tags-segment-push-message.md) 
- [Praca z szablonami niestandardowymi](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus kontroli dostępu z sygnaturami dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)
- [Programowe generowanie tokenów SAS](/rest/api/eventhub/generate-sas-token)
- [Zabezpieczenia firmy Apple: typowe Kryptografia](https://developer.apple.com/security/)
- [Czas epoki systemu UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [FUNKCJE](https://en.wikipedia.org/wiki/HMAC)
