---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147806"
---
### <a name="create-the-flutter-solution"></a>Tworzenie rozwiązania Flutter

1. Otwórz nowe wystąpienie **Visual Studio Code**.

1. Otwórz **paletę poleceń** (**SHIFT**  +  **Command**  +  **P**).

1. Wybierz polecenie **Flutter: nowy projekt** , a następnie naciśnij klawisz **Enter**.

1. Wprowadź *push_demo* dla **nazwy projektu** , a następnie wybierz **lokalizację projektu**.

1. Po wyświetleniu monitu wybierz pozycję **Pobierz pakiety**.

1. **Kontrolka**  +  **Kliknij** folder **Kotlin** (w obszarze **App**  >  **src**  >  **Main**), a następnie wybierz polecenie **Odsłoń w programie Finder**. Następnie zmień nazwy folderów podrzędnych (w folderze **Kotlin** ) na ```com``` , ```<your_organization>``` i ```pushdemo``` odpowiednio.

    > [!NOTE]
    > W przypadku korzystania z szablonu **Visual Studio Code** domyślne foldery to **com**, **przykład** **<Project_Name>**. Przy założeniu, że **mobcat** jest używany w **organizacji**, struktura folderów powinna indicatively wyglądać następująco:
    >
    > - kotlin
    >     - Dodatki
    >         - mobcat
    >             - pushdemo

1. Wróć do **Visual Studio Code**, zaktualizuj wartość identyfikator **aplikacji** w **android**  >  **aplikacji**Android  >  **Build. Gradle** do `com.<your_organization>.pushdemo` .

    > [!NOTE]
    > Należy użyć własnej nazwy organizacji dla symbolu zastępczego *your_organization<>* . Na przykład użycie **mobcat** jako organizacji spowoduje, że **Nazwa pakietu** ma wartość *com. mobcat. pushdemo*.

1. Zaktualizuj atrybut **Package** w plikach **AndroidManifest.xml** , w obszarze Debuguj **src**  >  **debug**, **src**  >  **Main**i **src**  >  **Profile** . Upewnij się, że wartości pasują do **identyfikatorów aplikacji** użytych w poprzednim kroku.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Zaktualizuj ```android:label``` atrybut w pliku **AndroidManifest.xml** w ścieżce **src**  >  **Main** to *PushDemo*. Następnie Dodaj ```android:allowBackup``` atrybut bezpośrednio w obszarze ```android:label``` , ustawiając jego wartość na **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Otwórz plik **Build. Gradle kompilacji** na poziomie aplikacji (**Android**  >  **App**  >  **Build. Gradle**), a następnie zaktualizuj *compileSdkVersion* (z sekcji **Android** ), aby użyć interfejsu API **29**. Następnie zaktualizuj wartości *minSdkVersion* i *targetSdkVersion* (z sekcji **defaultConfig** ) do **26** i **29** odpowiednio.

    > [!NOTE]
    > Na potrzeby tego samouczka są obsługiwane tylko te urządzenia, na których jest uruchomiony **poziom interfejsu API w wersji 26 lub nowszej** . można jednak go zwiększyć, aby obsługiwać urządzenia z starszymi wersjami.

1. **Kontrolka**  +  **Kliknij** folder **iOS** , a następnie wybierz **Otwórz w Xcode**.

1. W **Xcode**kliknij pozycję **Runner** ( **XCODEPROJ** u góry, a nie folder). Następnie wybierz miejsce docelowe **modułu uruchamiającego** i wybierz kartę **Ogólne** . Po wybraniu konfiguracji **wszystkich** kompilacji zaktualizuj **Identyfikator pakietu** do `com.<your_organization>.PushDemo` .

    > [!NOTE]
    > Należy użyć własnej nazwy organizacji dla symbolu zastępczego *your_organization<>* . Na przykład użycie **mobcat** jako organizacji spowoduje powstanie **identyfikatora pakietu** *com. mobcat. PushDemo*.

1. Kliknij pozycję **info. plist** , a następnie zaktualizuj wartość **Nazwa pakietu** do **PushDemo**

1. Zamknij **Xcode** i wróć do **Visual Studio Code**.

1. Wróć do **Visual Studio Code**, Otwórz **pubspec. YAML**, dodaj pakiety [http](https://pub.dev/packages/http) i [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **dart** jako zależności. Następnie Zapisz plik i po wyświetleniu monitu kliknij pozycję **Pobierz pakiety** .

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. W **terminalu**Zmień katalog na folder **systemu iOS** (dla projektu Flutter). Następnie wykonaj polecenie [**Zainstaluj pod**](https://guides.cocoapods.org/using/getting-started.html#installation) , aby zainstalować nowe (wymagane przez pakiet [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) ).

1. **Kontrolka**  +  **Kliknij** folder **lib** , a następnie wybierz pozycję **nowy plik** z menu przy użyciu *main_page. dart* jako nazwę pliku. Następnie Dodaj następujący kod.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. W **Main. dart**Zastąp kod z następującym szablonem.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. W obszarze **Terminal**Kompiluj i uruchom aplikację na każdej platformie docelowej w celu przetestowania aplikacji z szablonami uruchomionymi na urządzeniach. Upewnij się, że obsługiwane urządzenia są połączone.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Zaimplementuj składniki dla wielu platform

1. **Kontrolka**  +  **Kliknij** folder **lib** , a następnie wybierz pozycję **Nowy folder** z menu, używając *modeli* jako **nazwy folderu**.

1. **Kontrolka**  +  **Kliknij** folder **modele** , a następnie wybierz pozycję **nowy plik** z menu przy użyciu *device_installation. dart* jako nazwę pliku. Następnie Dodaj następujący kod.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Dodaj nowy plik do folderu **models** o nazwie *push_demo_action. dart* definiuje wyliczenie akcji obsługiwanych w tym przykładzie.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Dodaj nowy folder do projektu o nazwie **usługi** , a następnie Dodaj nowy plik do tego folderu o nazwie *device_installation_service. dart* z następującą implementacją.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Należy użyć własnej nazwy organizacji dla symbolu zastępczego *your_organization<>* . Na przykład przy użyciu **mobcat** jako organizacja spowoduje to [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) nazwę *com. mobcat. pushdemo/deviceinstallation*.
    >
    > Ta klasa hermetyzuje pracę z podstawową platformą natywną w celu uzyskania szczegółowych informacji o instalacji urządzenia. [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) ułatwia dwukierunkową komunikację asynchroniczną z podstawowymi platformami macierzystymi. Odpowiedni dla platformy odpowiednik dla tego kanału zostanie utworzony w dalszych krokach.

1. Dodaj do tego folderu inny plik o nazwie *notification_action_service. dart* z następującą implementacją.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Jest on używany jako prosty mechanizm scentralizowania obsługi akcji powiadamiania, dzięki czemu mogą być obsługiwane w sposób Międzyplatformowy przy użyciu wyliczenia z jednoznacznie określonym typem. Usługa umożliwia źródłową platformę natywną wyzwalać akcję, gdy jest ona określona w ładunku powiadomienia. Umożliwia również wspólnemu kodowi sprawdzenie, czy akcja została określona podczas uruchamiania aplikacji, gdy Flutter jest gotowa do przetworzenia. Na przykład po uruchomieniu aplikacji, wybierając powiadomienie z centrum powiadomień.

1. Dodaj nowy plik do folderu **Services** o nazwie *notification_registration_service. dart* z następującą implementacją.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Ta klasa hermetyzuje użycie **DeviceInstallationService** i żądań do usługi wewnętrznej bazy danych w celu wykonania wymaganych akcji rejestracji, wyrejestrowywania i odświeżania. Argument **apiKey** jest wymagany tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .

1. Dodaj nowy plik do folderu **lib** o nazwie *config. dart* z następującą implementacją.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Służy to prosty sposób definiowania wpisów tajnych aplikacji. Zastąp wartości zastępcze własnymi. Należy zanotować te zmiany podczas tworzenia usługi zaplecza. **Adres URL aplikacji interfejsu API** powinien mieć wartość `https://<api_app_name>.azurewebsites.net/` . Element członkowski **apiKey** jest wymagany tylko w przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) .
    >
    > Pamiętaj, aby dodać ten plik do pliku GITIGNORE, aby uniknąć przekazywania tych kluczy tajnych do kontroli źródła.

### <a name="implement-the-cross-platform-ui"></a>Implementowanie międzyplatformowego interfejsu użytkownika

1. W **main_page. dart**Zastąp funkcję **Build** następującą opcją.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Dodaj wymagane Importy na początku pliku **main_page. dart** .

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Dodaj pole do klasy **_MainPageState** , aby zapisać odwołanie do **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. W klasie **_MainPageState** Implementuj procedury obsługi zdarzeń dla przycisków **zarejestruj** i **Wyrejestruj** po **naciśnięciu** zdarzeń. Wywołaj odpowiednie metody **wyrejestrowywania rejestru**, / **Deregister** a następnie Wyświetl alert, aby wskazać wynik.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Teraz w **Main. dart**upewnij się, że następujące Importy znajdują się u góry pliku.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Zadeklaruj zmienną do przechowywania odwołań do wystąpienia **NotificationActionService** i zainicjuj ją.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Dodaj funkcje do obsługi wyświetlania alertu, gdy akcja jest wyzwalana.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Zaktualizuj funkcję **Main** , aby obserwować strumień **NotificationActionService** **actionTriggered** i sprawdzić wszystkie akcje przechwycone podczas uruchamiania aplikacji.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Jest to po prostu zademonstrowanie przyjęcia i propagacji akcji powiadomień wypychanych. Zwykle te byłyby obsługiwane dyskretnie, na przykład przechodzenie do określonego widoku lub odświeżanie niektórych danych zamiast wyświetlania alertu w tym przypadku.
