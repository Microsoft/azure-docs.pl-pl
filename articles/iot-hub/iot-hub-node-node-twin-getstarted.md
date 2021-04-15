---
title: Rozpoczynanie pracy z Azure IoT Hub bliźniaczych reprezentacji urządzeń (Node) | Microsoft Docs
description: Jak używać Azure IoT Hub bliźniaczych reprezentacji urządzenia w celu dodawania tagów, a następnie IoT Hub zapytania. Zestawy SDK usługi Azure IoT dla usługi Node.js są używane do implementowania aplikacji urządzenia symulowanego i aplikacji usługi, która dodaje tagi i uruchamia IoT Hub zapytania.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2047de2c1e61554ba96af8471905a22291861184
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484578"
---
# <a name="get-started-with-device-twins-nodejs"></a>Wprowadzenie do bliźniaczych reprezentacji urządzeń (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie Node.js konsoli:

* **AddTagsAndQuery.js**, Node.js aplikacji back-end, która dodaje tagi i wysyła zapytania do bliźniaczych reprezentacji urządzeń.

* **TwinSimulatedDevice.js**, Node.js, która symuluje urządzenie, które łączy się z centrum IoT przy użyciu utworzonej wcześniej tożsamości urządzenia i raportuje jego stan łączności.

> [!NOTE]
> Artykuł Azure IoT SDKs (Zestawy SDK usługi [Azure IoT)](iot-hub-devguide-sdks.md) zawiera informacje o zestawach SDK usługi Azure IoT, których można użyć do tworzenia aplikacji zarówno na urządzeniach, jak i w aplikacjach końcowych.
>

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Node.js wersji 10.0.x lub nowszej.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [](https://azure.microsoft.com/pricing/free-trial/) bezpłatne konto w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym artykule używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsoli Node.js, która dodaje metadane lokalizacji do bliźniaczej reprezentacji urządzenia skojarzonej z **myDeviceId.** Następnie wysyła zapytanie do bliźniaczych reprezentacji urządzeń przechowywanych w centrum IoT, wybierając urządzenia znajdujące się w USA, a następnie te, które raportują połączenie komórkowe.

1. Utwórz nowy pusty folder o nazwie **addtagsandqueryapp.** W **folderze addtagsandqueryapp** utwórz nową package.jspliku przy użyciu następującego polecenia w wierszu polecenia. Parametr `--yes` akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **addtagsandqueryapp** uruchom następujące polecenie, aby zainstalować **pakiet azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów utwórz nowy **plikAddTagsAndQuery.js** w **folderze addtagsandqueryapp.**

4. Dodaj następujący kod do **AddTagsAndQuery.js** plików. Zastąp `{iot hub connection string}` wartość IoT Hub parametrów połączenia skopiowanych w teście Get the [IoT hub connection string (Uzyskiwanie parametrów połączenia centrum IoT).](#get-the-iot-hub-connection-string)

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

    Obiekt **Registry** uwidacznia wszystkie metody wymagane do interakcji z bliźniaczą reprezentacji urządzenia z usługi. Poprzedni kod najpierw inicjuje obiekt **Registry,** a następnie pobiera bliźniacza reprezentacji urządzenia **dla myDeviceId,** a na koniec aktualizuje tagi za pomocą informacji o żądanej lokalizacji.

    Po zaktualizowaniu tagów wywołuje funkcję **queryTwins.**

5. Dodaj następujący kod na końcu **AddTagsAndQuery.js,** aby zaimplementować **funkcję queryTwins:**

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

    Poprzedni kod wykonuje dwa zapytania: pierwsze wybiera tylko bliźniacze reprezentacji urządzeń znajdujących się w zakładzie **Redmond43,** a drugie uściśli zapytanie, aby wybrać tylko te urządzenia, które również są połączone za pośrednictwem sieci komórkowej.

    Gdy kod tworzy obiekt **zapytania,** określa maksymalną liczbę zwracanych dokumentów w drugim parametrze. Obiekt **zapytania** zawiera właściwość logiczną **hasMoreResults,** która umożliwia wielokrotne wywołanie metod **nextAsTwin** w celu pobrania wszystkich wyników. Metoda o nazwie **next** jest dostępna dla wyników, które nie są bliźniaczych reprezentacji urządzeń, na przykład wyników zapytań agregacji.

6. Uruchom aplikację za pomocą:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   W wynikach zapytania powinno zostać wyświetlony jedno urządzenie z pytaniem o wszystkie urządzenia znajdujące się w **Redmond43** i żadne dla zapytania, które ogranicza wyniki do urządzeń, które korzystają z sieci komórkowej.

   ![Wyświetlanie jednego urządzenia w wynikach zapytania](media/iot-hub-node-node-twin-getstarted/service1.png)

W następnej sekcji utworzysz aplikację urządzenia, która będzie zgłaszać informacje o łączności i zmieniać wynik zapytania w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli usługi Node.js, która łączy się z centrum jako **myDeviceId,** a następnie aktualizuje zgłaszane właściwości bliźniaczej reprezentacji urządzenia, aby zawierała informacje o tym, że jest połączona przy użyciu sieci komórkowej.

1. Utwórz nowy pusty folder o nazwie **reportconnectivity**. W **folderze reportconnectivity** utwórz nową package.jspliku przy użyciu następującego polecenia w wierszu polecenia. Parametr `--yes` akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **reportconnectivity** uruchom następujące polecenie, aby zainstalować pakiety **azure-iot-device** i **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów utwórz nowy **plikReportConnectivity.js** w **folderze reportconnectivity.**

4. Dodaj następujący kod do **ReportConnectivity.js** plików. Zastąp ciąg ciągiem połączenia urządzenia skopiowanym podczas tworzenia tożsamości `{device connection string}` **urządzenia myDeviceId** w teście Rejestrowanie nowego urządzenia w [centrum IoT.](#register-a-new-device-in-the-iot-hub)

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

    Obiekt **Client** uwidacznia wszystkie metody wymagane do interakcji z bliźniaczą reprezentacji urządzenia z urządzenia. Poprzedni kod, po zainicjowaniu obiektu **Client,** pobiera bliźniacza reprezentacji urządzenia dla **myDeviceId** i aktualizuje zgłaszaną właściwość przy użyciu informacji o łączności.

5. Uruchamianie aplikacji urządzenia

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Powinien zostać wyświetlony komunikat `twin state reported`.

6. Teraz, gdy urządzenie zgłosiło informacje o łączności, powinno pojawić się w obu zapytaniach. Wstecz w **folderze addtagsandqueryapp** i ponownie uruchom zapytania:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Tym razem **myDeviceId** powinien pojawić się w obu wynikach zapytania.

    ![Pokaż myDeviceId w obu wynikach zapytania](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako tagi z aplikacji back-end i napisaliśmy symulowaną aplikację urządzenia w celu zgłoszenia informacji o łączności urządzenia w bliźniaczej reprezentacji urządzenia. Opisano również sposób wykonywania zapytań dotyczących tych informacji przy użyciu języka zapytań przypomina IoT Hub SQL.

Aby dowiedzieć się, jak to zrobić, skorzystaj z następujących zasobów:

* wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [Wprowadzenie IoT Hub](quickstart-send-telemetry-node.md) danych,

* konfigurowanie urządzeń przy użyciu żądanych właściwości bliźniaczej reprezentacji urządzenia w [samouczku Use desired properties to configure devices (Konfigurowanie urządzeń przy użyciu żądanych właściwości)](tutorial-device-twins.md)

* interaktywną kontrolę urządzeń (np. włączanie wentylatora z aplikacji kontrolowanej przez użytkownika) za pomocą [samouczka Używanie metod](quickstart-control-device-node.md) bezpośrednich.
