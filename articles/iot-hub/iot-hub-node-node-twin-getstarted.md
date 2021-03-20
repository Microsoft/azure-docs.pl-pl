---
title: Wprowadzenie do usługi Azure IoT Hub Device bliźniaczych reprezentacji (węzeł) | Microsoft Docs
description: Jak dodać tagi przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji, a następnie użyć kwerendy IoT Hubowej. Korzystając z zestawów SDK usługi Azure IoT dla Node.js, można zaimplementować aplikację symulowanego urządzenia i aplikację usługi, która dodaje Tagi i uruchamia IoT Hub zapytanie.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 65ced3812072bd2650fc36bbb7a7b0f3f75e0def
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336789"
---
# <a name="get-started-with-device-twins-nodejs"></a>Rozpoczynanie pracy z usługą Device bliźniaczych reprezentacji (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie Node.js aplikacje konsolowe:

* **AddTagsAndQuery.js**, Node.js aplikacji zaplecza, która dodaje Tagi i kwerendy bliźniaczych reprezentacji urządzeń.

* **TwinSimulatedDevice.js** aplikacja Node.js, która symuluje urządzenie, które nawiązuje połączenie z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia i zgłasza warunek łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) zawiera informacje na temat zestawów SDK usługi Azure IoT, których można użyć do tworzenia aplikacji zarówno dla urządzeń, jak i zaplecza.
>

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Node.js w wersji 10.0. x lub nowszej.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową Node.js, która dodaje metadane lokalizacji do sznurka urządzenia skojarzonego z **myDeviceId**. Następnie wysyła zapytanie do bliźniaczych reprezentacji urządzenia przechowywanego w usłudze IoT Hub, wybierając urządzenia znajdujące się w Stanach Zjednoczonych, a następnie te, które zgłaszają połączenie komórkowe.

1. Utwórz nowy pusty folder o nazwie **addtagsandqueryapp**. W folderze **addtagsandqueryapp** utwórz nowy package.jsw pliku przy użyciu następującego polecenia z poziomu wiersza polecenia. `--yes`Parametr akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **addtagsandqueryapp** Uruchom następujące polecenie, aby zainstalować pakiet **Azure-iothub** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz nowy plik **AddTagsAndQuery.js** w folderze **addtagsandqueryapp** .

4. Dodaj następujący kod do pliku **AddTagsAndQuery.js** . Zamień na `{iot hub connection string}` IoT Hub parametry połączenia, które zostały skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

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

    Obiekt **Registry** ujawnia wszystkie metody wymagane do współużytkowania z bliźniaczych reprezentacji urządzeń z usługi. Powyższy kod najpierw inicjuje obiekt **rejestru** , a następnie pobiera sznurki urządzenia dla **myDeviceId**, a wreszcie aktualizuje znaczniki z informacjami o żądanych lokalizacjach.

    Po zaktualizowaniu tagów wywołuje funkcję **queryTwins** .

5. Dodaj następujący kod na końcu  **AddTagsAndQuery.js** , aby zaimplementować funkcję **queryTwins** :

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

    Poprzedni kod wykonuje dwa zapytania: pierwszy wybiera tylko urządzenie bliźniaczych reprezentacji urządzenia znajdujące się w zakładzie **Redmond43** , a drugi zawęża zapytanie w celu wybrania tylko tych urządzeń, które są również połączone przez sieć komórkową.

    Gdy kod tworzy obiekt **zapytania** , określa maksymalną liczbę zwracanych dokumentów w drugim parametrze. Obiekt **zapytania** zawiera właściwość logiczną **hasMoreResults** , która umożliwia wywoływanie metod **nextAsTwin** wiele razy, aby pobrać wszystkie wyniki. Metoda o nazwie **Next** jest dostępna dla wyników, które nie są bliźniaczych reprezentacji urządzenia, na przykład wyników zapytań agregacji.

6. Uruchom aplikację przy użyciu:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Powinno zostać wyświetlone jedno urządzenie w wynikach zapytania z pytaniem o wszystkie urządzenia znajdujące się w **Redmond43** i brak dla zapytania, które ogranicza wyniki do urządzeń korzystających z sieci komórkowej.

   ![Zobacz jedno urządzenie w wynikach zapytania](media/iot-hub-node-node-twin-getstarted/service1.png)

W następnej sekcji utworzysz aplikację urządzenia, która zgłosi informacje o łączności i zmieni wynik zapytania w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową Node.js, która łączy się z centrum jako **myDeviceId**, a następnie aktualizuje raportowane właściwości z użyciem sznurka urządzenia, aby zawierała informacje, które są połączone z siecią komórkową.

1. Utwórz nowy pusty folder o nazwie **reportconnectivity**. W folderze **reportconnectivity** utwórz nowy package.jsw pliku przy użyciu następującego polecenia z poziomu wiersza polecenia. `--yes`Parametr akceptuje wszystkie wartości domyślne.

    ```cmd/sh
    npm init --yes
    ```

2. W wierszu polecenia w folderze **reportconnectivity** Uruchom następujące polecenie, aby zainstalować pakiety **Azure-IoT-Device** i **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów Utwórz nowy plik **ReportConnectivity.js** w folderze **reportconnectivity** .

4. Dodaj następujący kod do pliku **ReportConnectivity.js** . Zamień na `{device connection string}` Parametry połączenia urządzenia skopiowane podczas tworzenia tożsamości urządzenia **myDeviceId** w temacie [Rejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).

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

    Obiekt **Client** uwidacznia wszystkie metody wymagane do współpracy z urządzeniem bliźniaczych reprezentacji z urządzenia. Poprzedni kod, po zainicjowaniu obiektu **klienta** , pobiera sznurki urządzenia dla **myDeviceId** i aktualizuje swoją raportowaną Właściwość informacjami o łączności.

5. Uruchamianie aplikacji urządzenia

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Powinien zostać wyświetlony komunikat `twin state reported`.

6. Teraz, gdy urządzenie zgłosiło swoje informacje o łączności, powinno być wyświetlane w obu zapytaniach. Wróć do folderu **addtagsandqueryapp** i ponownie uruchom zapytania:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Ten czas **myDeviceId** powinien pojawić się w obu wynikach zapytania.

    ![Pokaż myDeviceId w obu wynikach zapytania](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako Tagi z aplikacji zaplecza i zapisały symulowaną aplikację urządzenia do raportowania informacji o łączności urządzenia w ramach sznurka urządzenia. Dowiesz się również, jak wykonywać zapytania dotyczące tych informacji przy użyciu języka zapytań IoT Hub w programie SQL Server.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [wprowadzenie do IoT Hub](quickstart-send-telemetry-node.md) ,

* Konfigurowanie urządzeń za pomocą odpowiednich właściwości sznurka urządzenia z użyciem [wymaganych właściwości do konfigurowania urządzeń](tutorial-device-twins.md) ,

* Interakcyjne sterowanie urządzeniami (na przykład Włączanie wentylatorów z poziomu aplikacji sterowanej przez użytkownika) przy użyciu samouczka [Korzystanie z metod bezpośrednich](quickstart-control-device-node.md) .
