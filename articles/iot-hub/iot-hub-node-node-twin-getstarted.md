---
title: Wprowadzenie do bliźniaczych urządzeń usługi Azure IoT Hub (węzeł) | Dokumenty firmy Microsoft
description: Jak dodać tagi za pomocą bliźniamcych urządzeń usługi Azure IoT Hub, a następnie użyć kwerendy Centrum IoT. Zestawów SDK usługi Azure IoT dla node.js służy do implementowania symulowanej aplikacji urządzenia i aplikacji usługi, która dodaje tagi i uruchamia kwerendę Centrum IoT.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt
ms.openlocfilehash: e65c781bd5cb62bdaa693b854caafd5f91fd497e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732277"
---
# <a name="get-started-with-device-twins-nodejs"></a>Wprowadzenie do bliźniaczych urządzeń (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie aplikacje konsoli Node.js:

* **AddTagsAndQuery.js**, aplikacja zaplecza Node.js, która dodaje tagi i zapytania bliźniaczy urządzeń.

* **TwinSimulatedDevice.js**, aplikacja Node.js, która symuluje urządzenie, które łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej i raportuje jego stan łączności.

> [!NOTE]
> Artykuł [Zestaw SDK usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera informacje o zestawach SDK usługi Azure IoT, których można używać do tworzenia aplikacji na urządzeniach i zapleczu.
>

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Node.js w wersji 10.0.x lub nowszej.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsoli Node.js, która dodaje metadane lokalizacji do bliźniaczej reprezentacji urządzenia skojarzonej z **identyfikatorem myDeviceId**. Następnie wysyła zapytanie do bliźniąt urządzeń przechowywanych w centrum IoT, wybierając urządzenia znajdujące się w Stanach Zjednoczonych, a następnie te, które zgłaszają połączenie komórkowe.

1. Utwórz nowy pusty folder o nazwie **addtagsandqueryapp**. W folderze **addtagsandqueryapp** utwórz nowy plik package.json, używając następującego polecenia w wierszu polecenia. Parametr `--yes` akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **addtagsandqueryapp** uruchom następujące polecenie, aby zainstalować pakiet **azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstu utwórz nowy plik **AddTagsAndQuery.js** w folderze **addtagsandqueryapp.**

4. Dodaj następujący kod do pliku **AddTagsAndQuery.js.** Zamień `{iot hub connection string}` na skopiowany ciąg połączenia Usługi IoT Hub w pliku [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Obiekt rejestru** udostępnia wszystkie metody wymagane do interakcji z bliźniaczych urządzeń z usługi. Poprzedni kod najpierw inicjuje obiekt **Rejestru,** a następnie pobiera bliźniaczej reprezentacji urządzenia dla **myDeviceId**i na koniec aktualizuje swoje tagi z żądanymi informacjami o lokalizacji.

    Po zaktualizowaniu tagów wywołuje **queryTwins** funkcji.

5. Dodaj następujący kod na końcu **AddTagsAndQuery.js,** aby zaimplementować **queryTwins** funkcji:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Poprzedni kod wykonuje dwa zapytania: pierwszy wybiera tylko bliźniacze urządzenia urządzeń znajdujących się w zakładzie **Redmond43,** a drugi udoskonala kwerendę, aby wybrać tylko urządzenia, które są również połączone za pośrednictwem sieci komórkowej.

    Gdy kod tworzy obiekt **kwerendy,** określa maksymalną liczbę zwróconych dokumentów w drugim parametrze. Obiekt **kwerendy** zawiera właściwość logiczną **hasMoreResults,** której można użyć do wywołania metod **nextAsTwin** wiele razy w celu pobrania wszystkich wyników. Metoda o nazwie **next** jest dostępna dla wyników, które nie są bliźniacze urządzenia, na przykład wyniki kwerend agregacji.

6. Uruchom aplikację za pomocą:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   W wynikach kwerendy powinna zostać wyświetlone jedno urządzenie z prośbą o wszystkie urządzenia znajdujące się w **redmond43,** a w przypadku kwerendy, która ogranicza wyniki, do urządzeń korzystających z sieci komórkowej.

   ![Wyświetlanie jednego urządzenia w wynikach kwerendy](media/iot-hub-node-node-twin-getstarted/service1.png)

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje o łączności i zmienia wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która łączy się z koncentratorem jako **myDeviceId**, a następnie aktualizuje zgłaszane właściwości bliźniaczej reprezentacji urządzenia, aby zawierały informacje, że jest on połączony za pomocą sieci komórkowej.

1. Utwórz nowy pusty folder o nazwie **reportconnectivity**. W folderze **reportconnectivity** utwórz nowy plik package.json, używając następującego polecenia w wierszu polecenia. Parametr `--yes` akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **reportconnectivity** uruchom następujące polecenie, aby zainstalować pakiety **azure-iot-device**i **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstu utwórz nowy plik **ReportConnectivity.js** w folderze **reportconnectivity.**

4. Dodaj następujący kod do pliku **ReportConnectivity.js.** Zamień `{device connection string}` na ciąg połączenia urządzenia skopiowany podczas tworzenia tożsamości urządzenia **myDeviceId** w [Rejestrze nowego urządzenia w centrum IoT hub](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    **Client** Obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniacze urządzenia z urządzenia. Poprzedni kod, po zainicjowaniu **client** obiektu, pobiera bliźniaczej reprezentacji urządzenia dla **myDeviceId** i aktualizuje jego zgłaszane właściwości z informacjami o łączności.

5. Uruchamianie aplikacji urządzenia

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Powinien zostać wyświetlony komunikat `twin state reported`.

6. Teraz, gdy urządzenie zgłosiło swoje informacje o łączności, powinno pojawić się w obu zapytaniach. Wróć do folderu **addtagsandqueryapp** i uruchom kwerendy ponownie:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Tym razem **myDeviceId** powinien pojawić się w obu wynikach kwerendy.

    ![Pokaż myDeviceId w obu wynikach kwerendy](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodano metadane urządzenia jako znaczniki z aplikacji zaplecza i napisałeś symulowaną aplikację urządzenia, aby zgłosić informacje o łączności urządzenia w bliźniaczej reprezentacji urządzenia. Dowiedzialiście się również, jak zbadać te informacje przy użyciu języka zapytań usługi IoT Hub podobnej do języka SQL.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [Wprowadzenie do usługi IoT Hub,](quickstart-send-telemetry-node.md)

* konfigurowanie urządzeń przy użyciu żądanych właściwości bliźniaczej reprezentacji urządzenia za [pomocą właściwości Użyj żądanego, aby skonfigurować urządzenia,](tutorial-device-twins.md)

* urządzenia sterujące interaktywnie (na przykład włączanie wentylatora z aplikacji kontrolowanej przez użytkownika), za [pomocą metod bezpośrednich](quickstart-control-device-node.md) samouczek.
