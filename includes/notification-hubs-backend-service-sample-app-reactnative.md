---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060500"
---
### <a name="create-the-react-native-solution"></a>Tworzenie rozwiązania do reagowania natywnego

1. W programie `Terminal` zaktualizuj narzędzia środowiska, które są wymagane do pracy z programem reagowanie natywnie przy użyciu następujących poleceń:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. W programie `Terminal` Uruchom następujące polecenie, jeśli masz `React Native` zainstalowany interfejs wiersza polecenia, aby go odinstalować. Użyj, `npx` Aby automatycznie uzyskiwać dostęp do najnowszej dostępnej wersji interfejsu wiersza polecenia w trybie reagowania na system:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > Obiekt macierzysty reaguje ma wbudowany interfejs wiersza polecenia. Zamiast instalować określoną wersję interfejsu wiersza polecenia i zarządzać nią globalnie, zalecamy dostęp do bieżącej wersji w czasie wykonywania przy użyciu programu `npx` , który jest dostarczany z Node.js. W programie `npx react-native <command>` Bieżąca stabilna wersja interfejsu wiersza polecenia zostanie pobrana i uruchomiona w momencie uruchomienia polecenia.

1. Przejdź do folderu Projects (projekty), w którym chcesz utworzyć nową aplikację. Użyj szablonu opartego na języku TypeScript, określając `--template` parametr:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Uruchom serwer Metro, który kompiluje pakiety języka JavaScript i monitoruje wszystkie aktualizacje kodu, aby odświeżyć zbiory w czasie rzeczywistym:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Uruchom aplikację systemu iOS, aby zweryfikować konfigurację. Przed wykonaniem następującego polecenia upewnij się, że został uruchomiony symulator systemu iOS lub połączono urządzenie z systemem iOS.

    ```bash
    npx react-native run-ios
    ```

1. Uruchom aplikację systemu Android, aby zweryfikować konfigurację. Wymaga to kilku dodatkowych kroków w celu skonfigurowania emulatora systemu Android lub urządzenia w celu uzyskania dostępu do natywnego serwera Metro. Następujące polecenia generują początkowy pakiet języka JavaScript dla systemu Android i umieszcza je w folderze Assets.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Ten skrypt zostanie wstępnie wdrożony z początkową wersją aplikacji. Po wdrożeniu Skonfiguruj emulator lub urządzenie w celu uzyskania dostępu do serwera Metro, określając adres IP serwera. Wykonaj następujące polecenie, aby skompilować i uruchomić aplikację systemu Android:

    ```bash
    npx react-native run-android
    ```

    Po dotarciu do aplikacji naciśnij `CMD+M` (emulator) lub potrząsnąć urządzenie, aby wypełnić ustawienia dewelopera, przejdź do `Settings`  >  `Change Bundle Location` , a następnie określ adres IP serwera Metro z domyślnym portem: `<metro-server-ip-address>:8081` .

1. W `App.tsx` pliku Zastosuj wszelkie zmiany w układzie strony, Zapisz go i wprowadź zmiany automatycznie odzwierciedlone w aplikacjach dla systemów iOS i Android.

    > [!NOTE]
    > Szczegółowy przewodnik konfigurowania środowiska programistycznego jest dostępny w [oficjalnej dokumentacji](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Aby ta przykład działała, potrzebne są następujące trzy pakiety:

1. [Reagowanie na natywne powiadomienia wypychane w systemie iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios)  -  [Project GitHub](https://github.com/react-native-community/push-notification-ios)

    Ten pakiet został utworzony podczas dzielenia PushNotificationIOS z rdzenia macierzystego. Pakiet natywnie implementuje powiadomienia wypychane dla systemu iOS i zapewnia natywny interfejs reaguje na dostęp do niego. Uruchom następujące polecenie, aby zainstalować pakiet:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Przereagowanie na natywne powiadomienia wypychane na wiele platform](https://www.npmjs.com/package/react-native-push-notification)

    Ten pakiet implementuje lokalne i zdalne powiadomienia w systemach iOS i Android w sposób Międzyplatformowy. Uruchom następujące polecenie, aby zainstalować pakiet:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Pakiet informacji o urządzeniu](https://www.npmjs.com/package/react-native-device-info) Pakiet zawiera informacje o urządzeniu w środowisku uruchomieniowym. Służy do definiowania identyfikatora urządzenia używanego do rejestrowania powiadomień wypychanych. Uruchom następujące polecenie, aby zainstalować pakiet:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Zaimplementuj składniki dla wielu platform

1. Utwórz i zaimplementuj `DemoNotificationHandler` :

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Utwórz i zaimplementuj `DemoNotificationService` :

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Utwórz i zaimplementuj `DemoNotificationRegistrationService` :

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Skonfiguruj aplikację. Otwórz `package.json` i Dodaj następującą definicję skryptu:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Następnie wykonaj ten skrypt, który skopiuje domyślną konfigurację do `config` folderu.

    ```bash
    yarn configure
    ```

    Ostatnim krokiem jest zaktualizowanie pliku konfiguracji skopiowanego w poprzednim kroku przy użyciu informacji o dostępie do interfejsu API. Określ `apiKey` `apiUrl` Parametry i:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementowanie międzyplatformowego interfejsu użytkownika

1. Definiuj układ strony

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Zastosuj style

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Inicjowanie składnika strony

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definiowanie uchwytów kliknięcia przycisku

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Obsługa odebranych rejestracji tokenów i powiadomień wypychanych

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```