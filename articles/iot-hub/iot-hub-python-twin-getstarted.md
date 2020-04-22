---
title: Wprowadzenie do bliźniaczych urządzeń usługi Azure IoT Hub (Python) | Dokumenty firmy Microsoft
description: Jak dodać tagi za pomocą bliźniamcych urządzeń usługi Azure IoT Hub, a następnie użyć kwerendy Centrum IoT. Zestawów SDK usługi Azure IoT dla języka Python służy do implementowania aplikacji symulowanego urządzenia i aplikacji usługi, która dodaje tagi i uruchamia kwerendę Centrum IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 0bb1371de827fbb68afd5d114f49afa4acec0deb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759722"
---
# <a name="get-started-with-device-twins-python"></a>Wprowadzenie do bliźniąt urządzeń (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie aplikacje konsoli Pythona:

* **AddTagsAndQuery.py**, aplikacji zaplecza Pythona, która dodaje tagi i zapytania bliźniacze urządzenia.

* **ReportConnectivity.py**, aplikacji Języka Python, która symuluje urządzenie, które łączy się z centrum IoT hub z tożsamością urządzenia utworzonej wcześniej i zgłasza jego stan łączności.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsoli Języka Python, która dodaje metadane lokalizacji do bliźniaczej reprezentacji urządzenia skojarzonej z **{Device ID}**. Następnie wysyła zapytanie do bliźniąt urządzeń przechowywanych w centrum IoT, wybierając urządzenia znajdujące się w Redmond, a następnie te, które zgłaszają połączenie komórkowe.

1. W katalogu roboczym otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Usługi Azure IoT Hub dla języka Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Za pomocą edytora tekstu utwórz nowy plik **AddTagsAndQuery.py.**

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Dodaj następujący kod. Zamień `[IoTHub Connection String]` na skopiowany ciąg połączenia koncentratora IoT w pliku [Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string). Zamień `[Device Id]` identyfikator urządzenia zarejestrowany w [zarejestruj nowe urządzenie w centrum IoT](#register-a-new-device-in-the-iot-hub)hub .
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Dodaj następujący kod do pliku **AddTagsAndQuery.py:**

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **Obiekt IoTHubRegistryManager** udostępnia wszystkie metody wymagane do interakcji z bliźniaczymi urządzeń z usługi. Kod najpierw inicjuje **IoTHubRegistryManager** obiektu, a następnie aktualizuje bliźniaczej reprezentacji urządzenia dla **DEVICE_ID**, a na koniec uruchamia dwa zapytania. Pierwszy wybiera tylko bliźniacze urządzenia urządzeń znajdujących się w zakładzie **w Redmond43,** a drugi udoskonala zapytanie, aby wybrać tylko urządzenia, które są również połączone za pośrednictwem sieci komórkowej.

6. Dodaj następujący kod na końcu **AddTagsAndQuery.py,** aby zaimplementować funkcję **iothub_service_sample_run:**

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Uruchom aplikację za pomocą:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    W wynikach kwerendy powinna zostać wyświetlone jedno urządzenie z prośbą o wszystkie urządzenia znajdujące się w **redmond43,** a w przypadku kwerendy, która ogranicza wyniki, do urządzeń korzystających z sieci komórkowej.

    ![pierwsze zapytanie przedstawiające wszystkie urządzenia w Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje o łączności i zmienia wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, która łączy się z koncentratorem jako **{Device ID}**, a następnie aktualizuje zgłoszone właściwości bliźniaczej reprezentacji urządzenia, aby zawierały informacje, że jest ona połączona za pomocą sieci komórkowej.

1. W wierszu polecenia w katalogu roboczym zainstaluj **zestaw SDK urządzenia usługi Azure IoT Hub dla języka Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstu utwórz nowy plik **ReportConnectivity.py.**

3. Dodaj następujący kod, aby zaimportować wymagane moduły z sdk urządzenia:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Dodaj następujący kod. Zastąp wartość symbolu `[IoTHub Device Connection String]` zastępczego skopiowanym ciągiem połączenia urządzenia w polu Zarejestruj nowe urządzenie w centrum [IoT hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Dodaj następujący kod do pliku **ReportConnectivity.py,** aby zaimplementować funkcję bliźniacze urządzenia:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    **Obiekt IoTHubModuleClient** udostępnia wszystkie metody wymagane do interakcji z bliźniaczymi urządzeń z urządzenia. Poprzedni kod, po zainicjowaniu **obiektu IoTHubModuleClient,** pobiera bliźniaczej reprezentacji urządzenia dla urządzenia i aktualizuje jego zgłoszoną właściwość za pomocą informacji o łączności.

6. Dodaj następujący kod na końcu **ReportConnectivity.py,** aby zaimplementować funkcję **iothub_client_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Uruchom aplikację urządzenia:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Powinno zostać wyświetlone potwierdzenie, że właściwości zgłaszane dane bliźniaczej reprezentacji urządzenia zostały zaktualizowane.

    ![aktualizowanie zgłoszonych właściwości z aplikacji urządzenia](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Teraz, gdy urządzenie zgłosiło swoje informacje o łączności, powinno pojawić się w obu zapytaniach. Wróć i uruchom kwerendy ponownie:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tym razem w obu wynikach kwerendy powinien pojawić się **identyfikator {Device ID}.**

    ![drugie zapytanie w aplikacji usługi](./media/iot-hub-python-twin-getstarted/service-2.png)

    W aplikacji urządzenia zobaczysz potwierdzenie, że odebrano żądaną poprawkę bliźniaczej właściwości wysłaną przez aplikację usługi.

    ![odbieranie żądanych właściwości w aplikacji urządzenia](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodano metadane urządzenia jako znaczniki z aplikacji zaplecza i napisałeś symulowaną aplikację urządzenia, aby zgłosić informacje o łączności urządzenia w bliźniaczej reprezentacji urządzenia. Można również dowiedzieć się, jak zbadać te informacje przy użyciu rejestru.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń za pomocą samouczka [Wprowadzenie do usługi IoT Hub.](quickstart-send-telemetry-python.md)

* Skonfiguruj urządzenia przy użyciu żądanych właściwości bliźniaczej reprezentacji urządzenia za pomocą właściwości [Użyj żądanej do konfigurowania urządzeń](tutorial-device-twins.md) samouczka.

* Sterowanie urządzeniami interaktywnie (na przykład włączanie wentylatora z aplikacji kontrolowanej przez użytkownika), za pomocą [metod bezpośrednich](quickstart-control-device-python.md) samouczek.
