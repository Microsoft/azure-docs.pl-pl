---
title: Wysyłanie powiadomień wypychanych do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs i interfejsów API REST
description: W ramach tego samouczka nauczysz się używać usługi Azure Notification Hubs i interfejsów API REST do wysyłania powiadomień wypychanych do urządzeń z systemem iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126439"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Samouczek: wysyłanie powiadomień wypychanych do aplikacji dla systemu iOS przy użyciu Notification Hubs interfejsów API REST

W tym samouczku opisano, jak za pomocą usługi Azure Notification Hubs wysyłać powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu interfejsów API REST.

Ten samouczek obejmuje następujące kroki:

- Utwórz przykładową aplikację dla systemu iOS.
- Połącz aplikację systemu iOS z usługą Azure Notification Hubs.
- Wysyłanie testowych powiadomień wypychanych.
- Sprawdź, czy aplikacja otrzymuje powiadomienia.

Pełny kod dla tego samouczka można pobrać z witryny [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Na komputerze Mac z systemem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), wraz z prawidłowym certyfikatem dewelopera zainstalowanym w łańcuchu kluczy.

- IPhone lub iPad z systemem iOS w wersji 10 lub nowszej.

- Urządzenie fizyczne zarejestrowane w [portalu firmy Apple](https://developer.apple.com/)   i skojarzone z Twoim certyfikatem.

Przed kontynuowaniem przejdź do poprzedniego samouczka na temat rozpoczynania pracy z [usługą Azure Notification Hubs dla aplikacji systemu iOS](https://go.microsoft.com/fwlink/?linkid=2129801) , aby skonfigurować i skonfigurować poświadczenia wypychania w centrum powiadomień. Nawet jeśli nie masz doświadczenia w tworzeniu systemu iOS, należy wykonać następujące czynności.

> [!NOTE]
> Ze względu na wymagania dotyczące konfiguracji powiadomień wypychanych należy wdrożyć i przetestować powiadomienia wypychane na fizycznym urządzeniu z systemem iOS (telefonie iPhone lub tablecie iPad), a nie emulatorze systemu iOS.

W poniższych sekcjach utworzysz aplikację dla systemu iOS, która nawiązuje połączenie z centrum powiadomień.

## <a name="create-an-ios-project"></a>Tworzenie projektu dla systemu iOS

1. W Xcode Utwórz nowy projekt dla systemu iOS i wybierz szablon **aplikacji pojedynczego widoku**   .

2. Podczas ustawiania opcji dla nowego projektu:
   - Określ **nazwę produktu**   (PushDemo) i **identyfikator organizacji**   ( `com.<organization>` ), który został użyty podczas ustawiania **identyfikatora pakietu**   w portalu dla deweloperów firmy Apple.
   - Wybierz **zespół**,   dla którego skonfigurowano **Identyfikator aplikacji**   .
   - Ustaw **Język**   na **SWIFT**.
   - Wybierz pozycję  **Dalej**.

3. Utwórz nowy folder o nazwie **SupportingFiles**.

4. Utwórz nowy plik listy p o nazwie **devsettings. plist**   w folderze **SupportingFiles**   Pamiętaj, aby dodać ten folder do pliku **GITIGNORE**   , aby nie był on zatwierdzany podczas pracy z repozytorium git. W aplikacji produkcyjnej mogą być warunkowo ustawiane te klucze tajne jako część zautomatyzowanego procesu kompilacji. Te ustawienia nie zostały omówione w tym samouczku.

5. Zaktualizuj **devsettings. plist**,   Aby uwzględnić następujące wpisy konfiguracji przy użyciu własnych wartości z centrum powiadomień, które zostało zainicjowane:

   | **Klucz**                  | **Typ** | **Wartość**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Ciąg   | `<hubKey>`       |
   | notificationHubKeyName   | Ciąg   | `<hubKeyName>`   |
   | Ustaw      | Ciąg   | `<hubName>`      |
   | notificationHubNamespace | Ciąg   | `<hubNamespace>` |

   Wymagane wartości można znaleźć, przechodząc do zasobu centrum powiadomień w Azure Portal. W szczególności wartości **Ustaw**   i **notificationHubNamespace**   znajdują się w prawym górnym rogu podsumowania **Essentials**na   stronie **Przegląd**   .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Wymagane wartości":::

   Możesz również znaleźć wartości **notificationHubKeyName**   i **notificationHubKey**,   przechodząc do **zasad dostępu**   i wybierając odpowiednie **zasady dostępu**, takie jak **DefaultFullSharedAccessSignature**. Następnie skopiuj z **podstawowych parametrów połączenia**   wartość prefiksu  `SharedAccessKeyName=`   dla **NotificationHubKeyName**i wartość prefiksu  `SharedAccessKey=`   dla **notificationHubKey**. Parametry połączenia powinny mieć następujący format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Dla uproszczenia Określ **DefaultFullSharedAccessSignature**, tak aby można było wysyłać powiadomienia przy użyciu tokenu. W przypadku **DefaultListenSharedAccessSignature**   jest lepszym wyborem w przypadku sytuacji, w których chcesz otrzymywać tylko powiadomienia.

6. W obszarze **projekt nawigatora**wybierz **nazwę projektu**,   a następnie wybierz kartę **Ogólne**   .

7. Znajdź **tożsamość**,   a następnie ustaw wartość **identyfikatora pakietu**   tak, aby była zgodna  `com.<organization>.PushDemo` , która jest wartością użytą dla **identyfikatora aplikacji**   z poprzedniego kroku.

8. Znajdź **możliwości & podpisywania**, a następnie wybierz odpowiedni **zespół**   dla **konta dewelopera firmy Apple**. Wartość **zespołu**   powinna być zgodna z tą, w której zostały utworzone certyfikaty i profile.

9. Xcode powinien automatycznie pobrać odpowiednią wartość **profilu aprowizacji**   na podstawie **identyfikatora pakietu**. Jeśli nie widzisz nowej wartości **profilu aprowizacji**   , spróbuj odświeżyć profile dla **tożsamości podpisywania**   , wybierając pozycję **Xcode**, a następnie pozycję **Preferencje**, **konto**, a następnie wybierz przycisk **Pobierz profile ręczne**,   Aby pobrać profile.

10. Na karcie **możliwości & podpisywania**   kliknij przycisk **+ możliwość**   i dwukrotnie naciśnij pozycję **powiadomienia wypychane**na   liście, aby upewnić się, że **powiadomienia wypychane**   są włączone.

11. Otwórz plik **AppDelegate. Swift**   w celu zaimplementowania protokołu **UNUserNotificationCenterDelegate**   i Dodaj następujący kod na początku klasy:

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

    Będziesz używać tych członków później. W tym celu użyjesz **tags**   elementu członkowskiego tagów jako części rejestracji przy użyciu **szablonu niestandardowego**. Aby uzyskać więcej informacji na temat tagów, zobacz [Tagi rejestracji](notification-hubs-tags-segment-push-message.md)   i [rejestracji szablonów](notification-hubs-templates-cross-platform-push-messages.md).

12. W tym samym pliku Dodaj następujący kod do funkcji **didFinishLaunchingWithOptions** :

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

    Ten kod pobiera ustawienia z **devsettings. plist**, ustawia klasę **AppDelegate**   jako **UNUserNotificationCenter**   delegata UNUserNotificationCenter, żąda autoryzacji powiadomień wypychanych, a następnie wywołuje **registerForRemoteNotifications**.

    Dla uproszczenia kod obsługuje tylko system iOS 10 i nowsze. Możesz dodać obsługę wcześniejszych wersji systemu iOS, warunkowo używając odpowiednich interfejsów API i metod, jak zwykle.

13. W tym samym pliku Dodaj następujący kod:

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

    Ten kod używa wartości **Identyfikator InstallationID**   i **pushChannel**   do zarejestrowania się w centrum powiadomień. W takim przypadku używasz **UIDevice. Current. identifierForVendor**,   Aby podać unikatową wartość, aby zidentyfikować urządzenie, a następnie sformatować **deviceToken**   w celu zapewnienia odpowiedniej wartości **pushChannel**   . Funkcja **showAlert**   istnieje po prostu, aby wyświetlić tekst komunikatu dla celów demonstracyjnych.

14. W pliku **AppDelegate. Swift**   Dodaj funkcje **WillPresent**   i **didReceive**   do **UNUserNotificationCenterDelegate**. Te funkcje wyświetlają alert, gdy zostanie powiadomiony o tym, że aplikacja działa na pierwszym planie lub w tle.

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

15. Dodaj `print` instrukcje do dolnej części funkcji **didRegisterForRemoteNotificationsWithDeviceToken**   , aby sprawdzić, czy wartości **Identyfikator InstallationID**   i **pushChannel**   są przypisywane:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Utwórz foldery **modele**, **usługi**i **Narzędzia**   dla podstawowych składników, które zostaną później dodane do projektu.

17. Sprawdź, czy projekt jest kompilowany i uruchamiany na urządzeniu fizycznym. Powiadomień wypychanych nie można przetestować za pomocą symulatora.

## <a name="create-models"></a>Tworzenie modeli

W tym kroku utworzysz zestaw modeli reprezentujący ładunki [interfejsu API REST Notification Hubs](/rest/api/notificationhubs/)   i przechowywania wymaganych danych tokenu sygnatury dostępu współdzielonego (SAS).

1. Dodaj nowy plik SWIFT o nazwie **PushTemplate. Swift**   do folderu **models**   . Ten model definiuje strukturę, która reprezentuje **treść**   pojedynczego szablonu jako część ładunku **DeviceInstallation**   .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Dodaj nowy plik SWIFT o nazwie **DeviceInstallation. Swift**   do folderu **models**   . Ten plik definiuje strukturę, która reprezentuje ładunek do tworzenia lub aktualizowania **instalacji urządzenia**. Dodaj następujący kod do pliku:

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

3. Dodaj nowy plik SWIFT o nazwie **TokenData. Swift**   do folderu **models**   . Ten model jest używany do przechowywania tokenu sygnatury dostępu współdzielonego wraz z jego wygaśnięciem. Dodaj następujący kod do pliku:

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

## <a name="generate-a-sas-token"></a>Generowanie tokenu SAS

Notification Hubs używa tej samej infrastruktury zabezpieczeń co Azure Service Bus. Aby wywołać interfejs API REST, program [programowo generuje token SAS](/rest/api/eventhub/generate-sas-token),   który może być używany w **Authorization**   nagłówku autoryzacji żądania.

Token otrzymany będzie w następującym formacie:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Sam proces obejmuje te same sześć kroków:

1. Oblicz czas wygaśnięcia w formacie [czasu epoki systemu UNIX](https://en.wikipedia.org/wiki/Unix_time)   , co oznacza liczbę sekund, które upłynęły od północy skoordynowanego czasu uniwersalnego, 1 stycznia 1970.

2. Sformatuj **ResourceUrl**   , który reprezentuje zasób, do którego próbujesz uzyskać dostęp, tak aby był zakodowany za pomocą wartości procentowej i małych liter.  **ResourceUrl**   ma format `https://<namespace>.servicebus.windows.net/<hubName>` .

3. Przygotuj **StringToSign**, który jest sformatowany jako `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` .

4. Obliczenia i **kodowanie w formacie base64 przy**   użyciu skrótu HMAC-SHA256 wartości **StringToSign**   . Wartość skrótu jest używana z **kluczową**   częścią **parametrów połączenia**   dla odpowiedniej **reguły autoryzacji**.

5. Sformatuj **podpis**kodowany algorytmem Base64   , aby został zakodowany procentowo.

6. Utwórz token w oczekiwanym formacie przy użyciu wartości **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**i **UrlEncodedResourceUrl**   .

Zapoznaj się z [dokumentacją Azure Service Bus](../service-bus-messaging/service-bus-sas.md)   , aby zapoznać się z bardziej szczegółowym omówieniem sygnatur dostępu współdzielonego oraz jak Azure Service Bus i Notification Hubs go używać.

Na potrzeby tego przykładu można użyć biblioteki CommonCrypto typu open source firmy Apple w **CommonCrypto**   celu ułatwienia mieszania sygnatury. Ponieważ jest to biblioteka języka C, nie jest dostępna w usłudze SWIFT z poziomu pola. Bibliotekę można udostępnić przy użyciu nagłówka mostkowania.

Aby dodać i skonfigurować nagłówek mostkowania:

1. W Xcode wybierz pozycję **plik**, a następnie pozycję **Nowy**, a następnie **plik**, a następnie wybierz pozycję **plik nagłówka**. Nazwij plik nagłówka **BridgingHeader. h**.

2. Edytuj plik, aby zaimportować **CommonHMAC. h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Zaktualizuj **Ustawienia kompilacji**docelowej,   Aby odwoływać się do nagłówka mostkowania:

   1. Wybierz projekt **PushDemo**   i przewiń w dół do sekcji **SWIFT kompilatora**   .

   2. Upewnij się, że opcja **Zainstaluj w nagłówku zgodności dla języka C**   ma ustawioną **wartość tak**.

   3. Wprowadź ścieżkę pliku  `<ProjectName>/BridgingHeader.h`   w opcji **nagłówka mostkowania "cel-C"**   . To jest ścieżka pliku do nagłówka mostkowania.

   Jeśli nie możesz znaleźć tych opcji, upewnij się, że masz **All**   wybrany widok wszystkie, a nie **podstawowy**   lub **dostosowany**.

   Istnieje wiele dostępnych bibliotek otoki typu open-source innych firm, które mogą ułatwić korzystanie z **CommonCrypto**   . Jednak dyskusje dotyczące tych bibliotek wykraczają poza zakres tego artykułu.

4. Dodaj nowy plik SWIFT o nazwie **TokenUtility. Swift**   w folderze **Utilities**   i Dodaj następujący kod:

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

   Jak wspomniano wcześniej, funkcja **getSasToken**organizuje   czynności wysokiego poziomu wymagane do przygotowania tokenu. Funkcja zostanie wywołana przez usługę instalacji w dalszej części tego samouczka.

   Pozostałe dwie funkcje są wywoływane przez funkcję **getSasToken**   : **sha256HMac**   do przetwarzania podpisu i **urlEncodedString**   do kodowania skojarzonego z nim ciągu adresu URL. Funkcja **urlEncodedString**   jest wymagana, ponieważ nie jest możliwe uzyskanie wymaganych danych wyjściowych przy użyciu wbudowanej funkcji **addingPercentEncoding**   .

    [Zestaw SDK usługi Azure Storage dla systemu iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)   to doskonały przykład sposobu podejścia do tych operacji w celu osiągnięcia tego celu. Więcej informacji o tokenach SAS Azure Service Bus można znaleźć w [dokumentacji Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

5. W **AppDelegate. Swift**Dodaj następujący kod do  `didRegisterForRemoteNotificationsWithDeviceToken`   funkcji, aby sprawdzić, czy **TokenUtility. getSasToken**   generuje prawidłowy token

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Upewnij się, że symbole zastępcze w ciągu **BaseAddress**są zastępowane   własnymi wartościami.

## <a name="verify-the-sas-token"></a>Weryfikowanie tokenu SAS

Przed zaimplementowaniem usługi instalacji na kliencie Sprawdź, czy aplikacja prawidłowo generuje token SYGNATURy dostępu współdzielonego za pomocą narzędzia HTTP. Na potrzeby tego samouczka nasze wybrane narzędzie to **program.**

Zanotuj wartości **Identyfikator InstallationID**   i **tokenów**   wygenerowanych przez aplikację.

Wykonaj następujące kroki, aby wywołać **installations**   interfejs API instalacji:

1. W programie **Poster**Otwórz nową kartę.
2. Ustaw żądanie, aby **uzyskać**   i określić następujący adres:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Skonfiguruj nagłówki żądania w następujący sposób:

   | **Klucz**       | **Wartość**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autoryzacja | \<sasToken\>     |
   | x-MS-Version  | 2015-01          |

4. Wybierz przycisk **kod**   , który pojawia się w prawym górnym rogu przycisku **Zapisz**   . Żądanie powinno wyglądać podobnie do poniższego przykładu:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Wybierz przycisk **Wyślij**   .

W tym momencie nie istnieje Rejestracja dla określonego **Identyfikator InstallationID**   . Weryfikacja powinna spowodować, że odpowiedź "404 nie zostanie znaleziona" zamiast odpowiedzi "401 Brak autoryzacji". Ten wynik powinien potwierdzić, że token sygnatury dostępu współdzielonego został zaakceptowany.

## <a name="implement-the-installation-service-class"></a>Implementowanie klasy usługi instalacji

Następnie Zaimplementuj podstawową otokę wokół [interfejsu API REST instalacji](/rest/api/notificationhubs/create-overwrite-installation).

Dodaj nowy plik SWIFT o nazwie **NotificationRegistrationService. Swift**   w folderze **usługi**   , a następnie Dodaj następujący kod do tego pliku:

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

Szczegóły wymagania są udostępniane w ramach inicjalizacji. Tagi i szablony są opcjonalnie przesyłane do funkcji **register**   w celu utworzenia części ładunku JSON **instalacji urządzenia**   .

Funkcja **register**   wywołuje inne funkcje prywatne w celu przygotowania żądania. Po odebraniu odpowiedzi zostaje wywołana i wskazuje, czy rejestracja zakończyła się pomyślnie.

Punkt końcowy żądania jest konstruowany przez funkcję **GetBaseAddress —**   . Konstrukcja używa *przestrzeni nazw*   i nazwy centrum powiadomień, *name*   które zostały podane podczas inicjalizacji.

Funkcja **getSasToken**   sprawdza, czy aktualnie przechowywany token jest prawidłowy. Jeśli token jest nieprawidłowy, funkcja wywołuje **TokenUtility**   w celu wygenerowania nowego tokenu, a następnie zapisuje go przed zwróceniem wartości.

Na koniec **encodeToJson**   konwertuje odpowiednie obiekty modelu na kod JSON do użycia jako część treści żądania.

## <a name="invoke-the-notification-hubs-rest-api"></a>Wywoływanie interfejsu API REST Notification Hubs

Ostatnim krokiem jest aktualizowanie **AppDelegate**,   Aby użyć **NotificationRegistrationService**   do zarejestrowania się w **NotificationHub**.

1. Otwórz **AppDelegate. Swift**   i Dodaj zmienne na poziomie klasy, aby zapisać odwołanie do **NoficiationRegistrationService**   i rodzajowego **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. W tym samym pliku zaktualizuj funkcję **didRegisterForRemoteNotificationsWithDeviceToken**,   Aby zainicjować **NotificationRegistrationService**   z parametrami wymaganymi, a następnie wywołaj funkcję **register**   .

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

   Dla uproszczenia kod używa `print` instrukcji do aktualizowania okna danych wyjściowych z wynikiem operacji **rejestracji**   .

3. Kompiluj i uruchamiaj aplikację na urządzeniu fizycznym. W oknie danych wyjściowych powinna zostać wyświetlona wartość **zarejestrowane** .

## <a name="test-the-solution"></a>Testowanie rozwiązania

W tym momencie aplikacja jest zarejestrowana w usłudze **NotificationHub**   i może odbierać powiadomienia wypychane. W programie Xcode Zatrzymaj debuger i Zamknij aplikację, jeśli jest aktualnie uruchomiona. Następnie sprawdź, czy szczegóły **instalacji urządzenia**   są wyświetlane zgodnie z oczekiwaniami i czy aplikacja może teraz odbierać powiadomienia wypychane.

### <a name="verify-the-device-installation"></a>Weryfikowanie instalacji urządzenia

Teraz można wykonać takie samo żądanie jak wcześniej, przy użyciu programu **Poster**   do [weryfikowania tokenu sygnatury dostępu współdzielonego](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Przy założeniu, że token sygnatury dostępu współdzielonego nie wygasł, odpowiedź powinna teraz zawierać podane wcześniej informacje o instalacji, takie jak szablony i Tagi.

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

Jeśli poprzedni token sygnatury dostępu współdzielonego utracił ważność, można dodać punkt przerwania do wiersza 24 klasy **TokenUtility**,   Aby uzyskać nowy token sygnatury **dostępu**współdzielonego i zaktualizować   nagłówek autoryzacji za pomocą tej nowej wartości.

### <a name="send-a-test-notification-azure-portal"></a>Wyślij testowe powiadomienie (Azure Portal)

Najszybszym sposobem na przetestowanie, że teraz możesz otrzymywać powiadomienia, jest przechodzenie do centrum powiadomień w Azure Portal:

1. W Azure Portal przejdź do karty **Przegląd**   w centrum powiadomień.

2. Wybierz pozycję **Testuj test**, która znajduje się powyżej **Essentials**   podsumowania Essentials w lewym górnym rogu okna portalu:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Wysyłanie testu podsumowania Notification Hubs Essentials":::

3. Wybierz **szablon niestandardowy**   z listy **platformy**   .

4. Wprowadź **12345**   dla **wyrażenia tagu Wyślij do**. Ten tag został poprzednio określony w instalacji.

5. Opcjonalnie można edytować **komunikat**   w ładunku JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs wysyłanie testowe":::

6. Wybierz pozycję **Wyślij**. Portal powinien wskazać, czy powiadomienie zostało pomyślnie wysłane do urządzenia:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Wynik wysyłania testu":::

   Przy założeniu, że aplikacja nie jest uruchomiona na pierwszym planie, należy również sprawdzić powiadomienie w **centrum powiadomień**   na urządzeniu. Naciśnięcie powiadomienia powinno otworzyć aplikację i wyświetlić alert.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Testuj powiadomienie":::

### <a name="send-a-test-notification-mail-carrier"></a>Wyślij powiadomienie testowe (przewoźnik poczty)

Powiadomienia można wysyłać za pośrednictwem [interfejsu API REST](/rest/api/notificationhubs/)   przy użyciu programu **Poster**, który może być wygodniejszym sposobem na przetestowanie.

1. Otwórz nową kartę w programie **Poster**.

2. Ustaw żądanie na **wpis**, a następnie wprowadź następujący adres:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Skonfiguruj nagłówki żądania w następujący sposób:

   | Klucz                           | Wartość                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | Application/JSON; charset = utf-8 |
   | Autoryzacja                 | \<sasToken\>                   |
   | ServiceBusNotification — format | szablon                       |
   | Tagi                          | "12345"                        |

4. Skonfiguruj treść żądania **BODY**   w taki sposób, aby korzystała z **formatu RAW-JSON (application.json)**   z następującym ładunkiem JSON:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Wybierz przycisk **kod**   , który znajduje się poniżej przycisku **Zapisz**   w prawym górnym rogu okna. Żądanie powinno wyglądać podobnie do poniższego przykładu:

   ```xml
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

6. Wybierz przycisk **Wyślij**   .

Należy uzyskać **201 Created**   kod stanu powodzenia i otrzymać powiadomienie 201 na urządzeniu klienckim.

## <a name="next-steps"></a>Następne kroki

Dostępna jest teraz podstawowa aplikacja SWIFT systemu iOS połączona z centrum powiadomień za pośrednictwem [interfejsu API REST Notification Hubs](/rest/api/notificationhubs/)i może wysyłać i odbierać powiadomienia. Aby dowiedzieć się więcej o sposobach wysyłania powiadomień do określonych urządzeń, przejdź do następującego samouczka:

- [Samouczek: powiadomienia wypychane do określonych urządzeń](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

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
