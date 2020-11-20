---
title: Szybki Start — Inicjowanie obsługi symulowanego urządzenia TPM na platformie Azure IoT Hub przy użyciu Node.js
description: Szybki Start — tworzenie i Inicjowanie obsługi symulowanego urządzenia TPM przy użyciu zestawu SDK urządzeń Node.js dla usługi Azure IoT Hub Device Provisioning Service (DPS). W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 6e7e986f658570553763001afdd58d7bb1880f94
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968183"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki Start: Tworzenie i Inicjowanie obsługi symulowanego urządzenia TPM przy użyciu zestawu SDK urządzeń Node.js dla IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

W tym przewodniku szybki start utworzysz symulowane urządzenie IoT na komputerze z systemem Windows. Symulowane urządzenie obejmuje symulator modułu TPM jako sprzętowy moduł zabezpieczeń (HSM). Możesz użyć przykładowego kodu Node.js urządzenia do połączenia tego symulowanego urządzenia z usługą IoT Hub przy użyciu indywidualnej rejestracji w usłudze Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się z pojęciami [aprowizacji](about-iot-dps.md#provisioning-process) .
- Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v 4.0 +](https://nodejs.org).
- Usługi [git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Upewnij się, że na komputerze jest zainstalowane [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 


## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

1. Otwórz wiersz polecenia lub powłokę Git Bash. Sklonuj repozytorium GitHub `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Przejdź do folderu głównego usługi GitHub i uruchom symulator [modułu TPM jako moduł](/windows/device-security/tpm/trusted-platform-module-overview) [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) dla symulowanego urządzenia. Nasłuchuje on przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; należy zachować ten symulator do momentu zakończenia tego przewodnika Szybki Start: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Utwórz nowy, pusty folder o nazwie **registerdevice**. W folderze **registerdevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia. Pamiętaj, aby odpowiedzieć na wszystkie pytania zadawane przez narzędzie `npm` lub zaakceptuj wartości domyślne, jeśli Ci one odpowiadają:
   
    ```cmd/sh
    npm init
    ```

1. Zainstaluj następujące pakiety prekursorów:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Istnieją znane problemy związane z instalowaniem powyższych pakietów. Aby je rozwiązać, uruchom polecenie `npm install --global --production windows-build-tools` przy użyciu wiersza polecenia w trybie **Uruchom jako administrator**, uruchom polecenie `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` po zastąpieniu ścieżki zainstalowaną wersją, a następnie uruchom ponownie powyższe polecenia instalacji.
    >

1. Zainstaluj następujące pakiety zawierające składniki używane podczas rejestracji:

   - klienta zabezpieczeń, który współdziała z modułem TPM: `azure-iot-security-tpm`
   - transport dla urządzenia w celu połączenia z usługą aprowizowania urządzeń: `azure-iot-provisioning-device-http` albo `azure-iot-provisioning-device-amqp`
   - klienta do używania transportu i klienta zabezpieczeń: `azure-iot-provisioning-device`

     Po zarejestrowaniu urządzenia możesz użyć zwykłych pakietów klienta urządzenia centrum IoT w celu połączenia urządzenia przy użyciu poświadczeń dostarczonych podczas rejestracji. Potrzebne elementy:

   - klient urządzenia: `azure-iot-device`
   - transport: dowolne z `azure-iot-device-amqp`, `azure-iot-device-mqtt` lub `azure-iot-device-http`
   - klient zabezpieczeń, który został już zainstalowany: `azure-iot-security-tpm`

     > [!NOTE]
     > Poniższe przykłady używają transportów `azure-iot-provisioning-device-http` i `azure-iot-device-mqtt`.
     > 

     Możesz zainstalować wszystkie te pakiety jednocześnie, uruchamiając następujące polecenie w wierszu polecenia w folderze **registereddevice**:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Za pomocą edytora tekstów utwórz nowy plik **ExtractDevice.js** w folderze **registerdevice**.

1. Dodaj następujące instrukcje `require` na początku pliku **ExtractDevice.js**:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Dodaj następującą funkcję, aby wdrożyć metodę:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Zapisz i zamknij plik **ExtractDevice.js**. Uruchom przykład:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. W oknie danych wyjściowych zostanie wyświetlony **_Klucz poręczenia_** i **_Identyfikator rejestracji_** wymagany do rejestracji urządzeń. Zapisz te wartości. 


## <a name="create-a-device-entry"></a>Tworzenie wpisu urządzenia

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służy do rejestrowania jednego urządzenia.

W tym artykule przedstawiono rejestracje indywidualne.

1. Zaloguj się do Azure Portal, wybierz przycisk **wszystkie zasoby** w menu po lewej stronie i Otwórz swoją usługę Device Provisioning.

1. W menu usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **indywidualne rejestracje** i wybierz u góry przycisk **Dodaj rejestrację indywidualną** . 

1. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **TPM** jako *Mechanizm* poświadczania tożsamości.
   - Wprowadź *Identyfikator rejestracji* i *Klucz poręczenia* dla urządzenia TPM z wartości zanotowanych wcześniej.
   - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
   - Opcjonalnie można podać następujące informacje:
       - Wprowadź unikatowy *Identyfikator urządzenia*. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. W przypadku wybrania opcji nie należy podać identyfikator rejestracji zostanie użyty do zidentyfikowania urządzenia.
       - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij przycisk **Zapisz** . 

     ![Wprowadzanie informacji o rejestracji urządzenia w bloku portalu](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Po pomyślnej rejestracji *Identyfikator rejestracji* Twojego urządzenia pojawia się na liście na karcie *Indywidualne rejestracje*. 


## <a name="register-the-device"></a>Rejestrowanie urządzenia

1. W Azure Portal wybierz blok **Przegląd** dla swojej usługi Device Provisioning i zanotuj wartości **_globalny punkt końcowy urządzenia_** oraz **_zakres identyfikatorów_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Za pomocą edytora tekstów utwórz nowy plik **RegisterDevice.js** w folderze **registerdevice**.

1. Dodaj następujące instrukcje `require` na początku pliku **RegisterDevice.js**:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Zestaw Azure IoT SDK dla środowiska Node.js** obsługuje dodatkowe protokoły, takie jak _AMQP_, _AMQP WS_ i _MQTT WS_.  Aby uzyskać więcej przykładów, zobacz [Device Provisioning Service SDK for Node.js samples (Przykłady zestawu SDK aprowizacji urządzeń dla środowiska Node.js)](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Dodaj zmienne **globalDeviceEndpoint** i **idScope** oraz użyj ich do utworzenia wystąpienia **ProvisioningDeviceClient**. Zastąp elementy **{globalDeviceEndpoint}** i **{idScope}** wartościami **_Globalny punkt końcowy urządzenia_** i **_Zakres identyfikatorów_** z **kroku 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Dodaj następującą funkcję, aby wdrożyć metodę na urządzeniu:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Zapisz i zamknij plik **RegisterDevice.js**. Uruchom przykład:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Zwróć uwagę na komunikaty symulujące uruchamianie urządzenia i łączenie z usługą Device Provisioning Service w celu pobrania informacji z Twojego centrum IoT. Po pomyślnej aprowizacji symulowanego urządzenia w centrum IoT Hub powiązanym z Twoją usługą aprowizacji identyfikator urządzenia jest wyświetlany w bloku **urządzenia IoT** centrum. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładowego klienta urządzenia, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Zamknij okno symulatora modułu TPM na swojej maszynie.
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz usługę Device Provisioning. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie wybierz kartę **rejestracje indywidualne** . Zaznacz pole wyboru obok *identyfikatora rejestracji* urządzenia zarejestrowanego w ramach tego przewodnika Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka. 
1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje centrum IoT Hub. Otwórz blok **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora urządzenia* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie naciśnij przycisk **Usuń** w górnej części okienka.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono symulowane urządzenie modułu TPM na maszynie i udostępniono je Centrum IoT Hub przy użyciu IoT Hub Device Provisioning Service. Aby dowiedzieć się, jak zarejestrować urządzenie TPM programowo, przejdź do przewodnika Szybki Start dotyczącego rejestrowania programowego dla urządzenia TPM. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start platformy Azure — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)