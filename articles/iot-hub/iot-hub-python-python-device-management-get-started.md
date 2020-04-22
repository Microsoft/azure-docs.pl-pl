---
title: Wprowadzenie do zarządzania urządzeniami usługi Azure IoT Hub (Python) | Dokumenty firmy Microsoft
description: Jak za pomocą zarządzania urządzeniami Usługi IoT Hub zainicjować ponowne uruchomienie urządzenia zdalnego. Użyj zestawu SDK usługi Azure IoT dla języka Python, aby zaimplementować aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f376831175840284fdfd15f367542d33ad9f7177
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759754"
---
# <a name="get-started-with-device-management-python"></a>Wprowadzenie do zarządzania urządzeniami (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure Portal, aby utworzyć centrum IoT Hub i utworzyć tożsamość urządzenia w centrum IoT hub.

* Utwórz aplikację symulowanego urządzenia, która zawiera bezpośrednią metodę ponownego uruchomienia tego urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsoli Języka Python, która wywołuje metodę bezpośredniego ponownego uruchomienia w symulowanej aplikacji urządzenia za pośrednictwem centrum IoT hub.

Na końcu tego samouczka masz dwie aplikacje konsoli Python:

* **dmpatterns_getstarted_device.py**, który łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje fizyczny restart i raportuje czas ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.py**, który wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane zgłoszone właściwości.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsoli języka Python, która reaguje na metodę bezpośrednią wywoływaną przez chmurę

* Symulowanie ponownego uruchamiania urządzenia

* Użyj zgłoszonych właściwości, aby włączyć zapytania bliźniaczej reprezentacji urządzeń do identyfikowania urządzeń i kiedy ostatnio zostały ponownie uruchomione

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **urządzenia azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstu utwórz plik o nazwie **dmpatterns_getstarted_device.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku **pliku dmpatterns_getstarted_device.py.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Dodaj **zmienną CONNECTION_STRING.** Zastąp wartość symbolu `{deviceConnectionString}` zastępczego ciągiem połączenia urządzenia. Ten ciąg połączenia został skopiowany wcześniej w [rejestrze nowego urządzenia w centrum IoT hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Dodaj następujące wywołania zwrotne funkcji, aby zaimplementować metodę bezpośrednią na urządzeniu.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Uruchom odbiornik metody bezpośredniej i poczekaj.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Zapisz i zamknij plik **dmpatterns_getstarted_device.py.**

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsoli Języka Python, która inicjuje zdalny ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczej reprezentacji urządzenia, aby odkryć ostatni czas ponownego uruchomienia dla tego urządzenia.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Za pomocą edytora tekstu utwórz plik o nazwie **dmpatterns_getstarted_service.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku **pliku dmpatterns_getstarted_service.py.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Dodaj następujące deklaracje zmiennych. Zastąp wartość symbolu `{IoTHubConnectionString}` zastępczego parametryem połączenia centrum IoT skopiowanym wcześniej w polu Pobierz ciąg połączenia centrum [IoT](#get-the-iot-hub-connection-string). Zastąp wartość symbolu `{deviceId}` zastępczego identyfikatorem urządzenia zarejestrowanym w polu Zarejestruj nowe urządzenie w centrum [IoT hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Dodaj następującą funkcję, aby wywołać metodę urządzenia, aby ponownie uruchomić urządzenie docelowe, a następnie zapytanie o bliźniacze urządzenia i uzyskać ostatni czas ponownego uruchomienia.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Zapisz i zamknij plik **dmpatterns_getstarted_service.py.**

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W wierszu polecenia uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. W innym wierszu polecenia uruchom następujące polecenie, aby wyzwolić zdalny ponowny rozruch i zapytać o bliźniaczej reprezentacji urządzenia, aby znaleźć ostatni czas ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Zostanie wyświetlana odpowiedź urządzenia na metodę bezpośrednią w konsoli.

   Poniżej przedstawiono odpowiedź urządzenia na metodę bezpośrednią ponownego uruchomienia:

   ![Symulowane wyjście aplikacji urządzenia](./media/iot-hub-python-python-device-management-get-started/device.png)

   Poniżej przedstawiono usługę wywołującą metodę bezpośrednią ponownego uruchomienia i sondującą bliźniaczątkę urządzenia pod kątem stanu:

   ![Wyzwalanie danych wyjściowych usługi ponownego uruchamiania](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]