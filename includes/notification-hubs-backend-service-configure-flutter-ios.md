---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147802"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Skonfiguruj element docelowy programu Runner i informacje. plist

1. W **Visual Studio Code**, **Control**  +  **kliknij przycisk** w folderze **systemu iOS** , a następnie wybierz **Otwórz w Xcode**.

1. W **Xcode**kliknij pozycję **Runner** ( **XCODEPROJ** w górnej części, a nie folder), a następnie wybierz miejsce docelowe programu **uruchamiającego** , a następnie **podpisywanie & funkcji**. Po wybraniu wybranej **konfiguracji kompilacji wybierz** konto dewelopera dla **zespołu**. Upewnij się, że opcja "automatycznie Zarządzaj podpisem" jest zaznaczona, a certyfikat podpisywania i profil aprowizacji są wybierane automatycznie.

    > [!NOTE]
    > Jeśli nie widzisz nowego profilu aprowizacji, spróbuj odświeżyć profile dla tożsamości podpisującej, wybierając pozycję **Xcode**  >  **Preferences**,  >  **Account** a następnie wybierz przycisk **Pobierz profile ręczne** , aby pobrać profile.

1. Kliknij pozycję **+ możliwości**, a następnie wyszukaj pozycję **powiadomienia wypychane**. Kliknij **dwukrotnie pozycję** **powiadomienia wypychane** , aby dodać tę funkcję.

1. Otwórz **info. plist** i ustaw **minimalną wersję systemu** na **13,0**.

    > [!NOTE]
    > Tylko urządzenia z systemem **iOS 13,0 lub nowszym** są obsługiwane na potrzeby tego samouczka, jednak można je zwiększyć, aby obsługiwać urządzenia z starszymi wersjami.

1. Otwórz moduł **uruchamiającego. uprawnienia** i upewnij się, że ustawienie **środowiska APS** ma wartość **programowanie**.

### <a name="handle-push-notifications-for-ios"></a>Obsługa powiadomień wypychanych dla systemu iOS

1. **Kontrolka**  +  **Kliknij** folder **Runner** (w ramach projektu programu uruchamiającego), a następnie wybierz pozycję **Nowa grupa** , używając **usług** jako nazwy.

1. **Kontrolka**  +  **Kliknij** folder **usługi** , a następnie wybierz pozycję **nowy plik.**... Następnie wybierz **plik SWIFT** i kliknij przycisk **dalej**. Określ **DeviceInstallationService** dla nazwy, a następnie kliknij przycisk **Utwórz**.

1. Zaimplementuj **DeviceInstallationService. Swift** przy użyciu następującego kodu.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/deviceinstallation` kanału. Zostało to zdefiniowane w części Flutter aplikacji w **DeviceInstallationService. dart**. W takim przypadku wywołania są wykonywane ze wspólnego kodu do macierzystego hosta. Pamiętaj, aby zastąpić **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.
    >
    > Ta klasa udostępnia unikatowy identyfikator (przy użyciu wartości [UIDevice. identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) ) w ramach ładunku rejestracji centrum powiadomień.

1. Dodaj kolejny **plik SWIFT** do folderu **Services** o nazwie *NotificationRegistrationService*, a następnie Dodaj następujący kod.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/notificationregistration` kanału. Zostało to zdefiniowane w części Flutter aplikacji w **NotificationRegistrationService. dart**. W takim przypadku wywołania są nawiązywane z macierzystego hosta do wspólnego kodu. Należy również zastanowić się nad zastępowaniem **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.

1. Dodaj kolejny **plik SWIFT** do folderu **Services** o nazwie *NotificationActionService*, a następnie Dodaj następujący kod.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa implementuje odpowiedni dla danej platformy odpowiednik dla `com.<your_organization>.pushdemo/notificationaction` kanału. To zostało zdefiniowane w części Flutter aplikacji w **NotificationActionService. dart**. W takim przypadku wywołania mogą być wykonywane w obu kierunkach. Pamiętaj, aby zastąpić **<your_organization>** własną organizacją, wszędzie tam, gdzie jest to używane.

1. W **AppDelegate. Swift**Dodaj zmienne do przechowywania odwołań do utworzonych wcześniej usług.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Dodaj funkcję o nazwie **processNotificationActions** do przetwarzania danych powiadomień. Warunkowego wyzwalania tej akcji lub zapisywania jej do użycia później, jeśli akcja jest przetwarzana podczas uruchamiania aplikacji.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Przesłoń funkcję **didRegisterForRemoteNotificationsWithDeviceToken** , ustawiając wartość **tokenu** dla **DeviceInstallationService**. Następnie Wywołaj **refreshRegistration** w **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Zastąp funkcję **didReceiveRemoteNotification** , przekazując argument **UserInfo** do funkcji **processNotificationActions** .

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Zastąp funkcję **didFailToRegisterForRemoteNotificationsWithError** w celu zarejestrowania błędu.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Jest to bardzo duży symbol zastępczy. Należy zaimplementować odpowiednie rejestrowanie i obsługę błędów dla scenariuszy produkcyjnych.

1. W **didFinishLaunchingWithOptions**, Utwórz wystąpienie zmiennych **deviceInstallationService**, **notificationRegistrationService**i **notificationActionService** .

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. W tej samej funkcji warunkowo Zażądaj autoryzacji i zarejestruj się w celu otrzymywania powiadomień zdalnych.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Jeśli **launchOptions** zawiera klucz **remoteNotification** , wywołaj **processNotificationActions** na końcu funkcji **didFinishLaunchingWithOptions** . Przekaż wynikowy obiekt **userInfo** i Użyj *wartości true* dla argumentu *launchAction* . Wartość *true* oznacza, że akcja jest przetwarzana podczas uruchamiania aplikacji.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
