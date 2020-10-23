---
title: Łączenie urządzeń z certyfikatami X. 509 w aplikacji IoT Central platformy Azure
description: Jak połączyć urządzenia z certyfikatami X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Central aplikacji
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c2af331304decd7955892ef4911d1644518f57b8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427879"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Jak połączyć urządzenia z certyfikatami X. 509 za pomocą zestawu SDK urządzeń Node.js dla IoT Central aplikacji

IoT Central obsługuje zarówno sygnatury dostępu współdzielonego, jak i certyfikat X. 509 w celu zabezpieczenia komunikacji między urządzeniem a aplikacją. W samouczku [Tworzenie i łączenie aplikacji klienckiej z aplikacją platformy Azure IoT Central używany jest](./tutorial-connect-device-nodejs.md) sygnatura dostępu współdzielonego. W tym artykule dowiesz się, jak zmodyfikować przykładowy kod, aby użyć X. 509.  Certyfikaty X. 509 są zalecane w środowiskach produkcyjnych. Aby uzyskać więcej informacji, zobacz temat [połączono z usługą Azure IoT Central](./concepts-get-connected.md).

W tym artykule przedstawiono dwa sposoby używania rejestracji X. 509 — [grupy](how-to-connect-devices-x509.md#use-a-group-enrollment) są zwykle używane w środowisku produkcyjnym, a [indywidualne rejestracje](how-to-connect-devices-x509.md#use-an-individual-enrollment) przydatne do testowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [tworzenia i łączenia aplikacji klienckiej z samouczkiem aplikacji Azure IoT Central (Node.js)](./tutorial-connect-device-nodejs.md) .
- Usługi [git](https://git-scm.com/download/).
- Pobierz i zainstaluj [OpenSSL](https://www.openssl.org/). Jeśli używasz systemu Windows, możesz użyć plików binarnych ze [strony OpenSSL na sourceforge](https://sourceforge.net/projects/openssl/).

## <a name="use-a-group-enrollment"></a>Korzystanie z rejestracji grupowej

Użyj certyfikatów X. 509 z rejestracją grupy w środowisku produkcyjnym. W przypadku rejestracji grupy należy dodać główny lub pośredni certyfikat X. 509 do aplikacji IoT Central. Urządzenia z certyfikatami liścia pochodzącymi z certyfikatu głównego lub pośredniego mogą łączyć się z Twoją aplikacją.

## <a name="generate-root-and-device-cert"></a>Generuj certyfikat certyfikatu głównego i urządzenia

W tej sekcji użyto certyfikatu X. 509, aby połączyć urządzenie z certyfikatem uzyskanym z certyfikatu grupy rejestracji, który może nawiązać połączenie z aplikacją IoT Central.

> [!WARNING]
> Ten sposób generowania certyfikatów X. 509 jest przeznaczony wyłącznie do celów testowych. W środowisku produkcyjnym należy używać oficjalnego, bezpiecznego mechanizmu generowania certyfikatów.

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub na potrzeby skryptów generowania certyfikatów:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Przejdź do skryptu generatora certyfikatów i zainstaluj wymagane pakiety:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Utwórz certyfikat główny, a następnie wyuzyskuj certyfikat urządzenia, uruchamiając skrypt:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

    > [!TIP]
    > Identyfikator urządzenia może zawierać litery, cyfry i `-` znaki.

Te polecenia tworzą trzy pliki dla katalogu głównego i certyfikatu urządzenia

filename | Contents
-------- | --------
\<name\>_cert. pem | Publiczna część certyfikatu x509
\<name\>_key. pem | Klucz prywatny certyfikatu x509
\<name\>_fullchain. pem | Cały łańcuch kluczy certyfikatu x509.

## <a name="create-a-group-enrollment"></a>Tworzenie rejestracji grupowej

1. Otwórz aplikację IoT Central i przejdź do opcji **Administracja**  w okienku po lewej stronie i wybierz pozycję **połączenie z urządzeniem**.

1. Wybierz pozycję **+ Utwórz grupę rejestracji**i Utwórz nową grupę rejestracji o nazwie Moja _x509_ z typem zaświadczania **certyfikatów (X. 509)**.

1. Otwórz utworzoną grupę rejestracji i wybierz pozycję **Zarządzaj podstawową**.

1. Wybierz opcję plik i Przekaż plik certyfikatu głównego o nazwie _mytestrootcert_cert. pem_ , który został wygenerowany wcześniej:

    ![Przekazywanie certyfikatu](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Aby ukończyć weryfikację, wygeneruj kod weryfikacyjny, skopiuj go, a następnie użyj go do utworzenia certyfikatu weryfikacji X. 509 w wierszu polecenia:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Przekaż podpisany certyfikat weryfikacji _verification_cert. pem_ , aby ukończyć weryfikację:

    ![Zweryfikowany certyfikat](./media/how-to-connect-devices-x509/verified.png)

Teraz można połączyć urządzenia, które mają certyfikat X. 509 pochodzący z tego podstawowego certyfikatu głównego. Po zapisaniu grupy rejestracji Zanotuj zakres identyfikatorów.

## <a name="run-sample-device-code"></a>Uruchom przykładowy kod urządzenia

1. W aplikacji IoT Central platformy Azure wybierz pozycję **urządzenia**i Utwórz nowe urządzenie z _Mytestdevice_ jako **Identyfikator urządzenia** na podstawie szablonu urządzenia **czujnika środowiska** .

1. Skopiuj pliki _mytestdevice_key. pem_ i _mytestdevice_cert. pem_ do folderu, który zawiera aplikację _environmentalSensor.js_ . Ta aplikacja została utworzona po zakończeniu pracy z [samouczkiem łączenie urządzenia (Node.js)](./tutorial-connect-device-nodejs.md).

1. Przejdź do folderu, który zawiera environmentalSensor.js aplikację, a następnie uruchom następujące polecenie, aby zainstalować pakiet X. 509:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Edytuj plik **environmentalSensor.js** .
    - Zastąp `idScope` wartość **zakresem identyfikatorów** , który został wcześniej zanotowany.
    - Zastąp `registrationId` wartość wartością `mytestdevice` .

1. Edytuj `require` instrukcje w następujący sposób:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Edytuj sekcję tworzącą klienta w następujący sposób:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

1. Zmodyfikuj sekcję otwierającą połączenie w następujący sposób:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Wykonaj skrypt i sprawdź, czy urządzenie zostało pomyślnie zainicjowane:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Możesz również sprawdzić, czy dane telemetryczne są wyświetlane na pulpicie nawigacyjnym.

    ![Weryfikuj dane telemetryczne urządzenia](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Korzystanie z rejestracji indywidualnej

Aby przetestować urządzenie i rozwiązanie, użyj certyfikatów X. 509 z rejestracją indywidualną. W przypadku rejestracji indywidualnej w aplikacji IoT Central nie ma głównego lub pośredniego certyfikatu X. 509. Urządzenia używają certyfikatu X. 509 z podpisem własnym w celu nawiązania połączenia z aplikacją.

## <a name="generate-self-signed-device-cert"></a>Generuj certyfikat urządzenia z podpisem własnym

W tej sekcji użyto certyfikatu X. 509 z podpisem własnym, aby połączyć urządzenia dla rejestracji indywidualnej, które są używane do rejestrowania jednego urządzenia. Certyfikaty z podpisem własnym służą wyłącznie do celów testowych.

Utwórz certyfikat urządzenia X. 509 z podpisem własnym, uruchamiając skrypt. Należy pamiętać, aby użyć tylko małych liter alfanumerycznych i łączników dla nazwy certyfikatu:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Utwórz rejestrację indywidualną

1. W aplikacji IoT Central platformy Azure wybierz pozycję **urządzenia**i Utwórz nowe urządzenie z **identyfikatorem urządzenia** jako _Mytestselfcertprimary_ z szablonu urządzenia czujnika środowiska. Zanotuj **zakres identyfikatora**, który będzie używany później.

1. Otwórz utworzone urządzenie i wybierz pozycję **Połącz**.

1. Wybierz opcję **rejestracje indywidualne** jako **metodę połączenia** i **Certyfikaty (X. 509)** jako mechanizm:

    ![Rejestracja indywidualna](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Wybierz opcję plik w obszarze podstawowy i Przekaż plik certyfikatu o nazwie _mytestselfcertprimary_cert. pem_ , który został wcześniej wygenerowany.

1. Wybierz opcję plik certyfikatu pomocniczego i Przekaż plik certyfikatu o nazwie _mytestselfcertsecondary_cert. pem._ Następnie wybierz pozycję **Zapisz**:

    ![Przekazywanie certyfikatu rejestracji indywidualnej](./media/how-to-connect-devices-x509/individual-enrollment.png)

Urządzenie jest teraz obsługiwane z certyfikatem X. 509.

## <a name="run-a-sample-individual-enrollment-device"></a>Uruchamianie przykładowego urządzenia rejestracji indywidualnej

1. Skopiuj pliki _mytestselfcertprimary_key. pem_ i _mytestselfcertprimary_cert. pem_ do folderu, który zawiera aplikację environmentalSensor.js. Ta aplikacja została utworzona po zakończeniu pracy z [samouczkiem łączenie urządzenia (Node.js)](./tutorial-connect-device-nodejs.md).

1. Edytuj plik **environmentalSensor.js** w następujący sposób i Zapisz go.
    - Zastąp `idScope` wartość **zakresem identyfikatorów** , który został wcześniej zanotowany.
    - Zastąp `registrationId` wartość wartością `mytestselfcertprimary` .
    - Zastąp **wariancję deviceCert** jako:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Wykonaj skrypt i sprawdź, czy urządzenie zostało pomyślnie zainicjowane:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Możesz również sprawdzić, czy dane telemetryczne są wyświetlane na pulpicie nawigacyjnym.

    ![Rejestracja indywidualna telemetrii](./media/how-to-connect-devices-x509/telemetry-primary.png)

Powyższe kroki można powtórzyć również dla certyfikatu _mytestselfcertsecondary_ .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć urządzenia za pomocą certyfikatów X. 509, sugerowanym następnym krokiem jest zapoznanie się z tematem [Monitorowanie łączności urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](howto-monitor-devices-azure-cli.md)
