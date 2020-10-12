---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (Python) | Microsoft Docs
description: Jak za pomocą IoT Hub zarządzanie urządzeniami zainicjować zdalne ponowne uruchomienie urządzenia. Zestaw SDK usługi Azure IoT dla języka Python służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872722"
---
# <a name="get-started-with-device-management-python"></a>Wprowadzenie do zarządzania urządzeniami (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj Azure Portal, aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią, która uruchamia ponownie to urządzenie. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsolową w języku Python, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem Centrum IoT.

Na końcu tego samouczka masz dwie aplikacje konsolowe języka Python:

* **dmpatterns_getstarted_device. PR**, który łączy się z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje rozruch fizyczny i raportuje godzinę ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service. PR**, która wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane raportowane właściwości.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej w języku Python, która reaguje na bezpośrednią metodę wywoływaną przez chmurę

* Symulowanie ponownego uruchamiania urządzenia

* Użyj raportowanych właściwości, aby włączyć do identyfikowania urządzeń i podczas ostatniego ponownego uruchomienia zapytania o sznury

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstów Utwórz plik o nazwie **dmpatterns_getstarted_device. PR** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_device. PR** .

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Dodaj zmienną **CONNECTION_STRING** . Zastąp `{deviceConnectionString}` wartość symbolu zastępczego parametrami połączenia urządzenia. Te parametry połączenia zostały wcześniej skopiowane przez [zarejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Dodaj następujące wywołania zwrotne funkcji w celu zaimplementowania metody bezpośredniej na urządzeniu.

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

6. Uruchom odbiornik metody Direct i poczekaj.

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

7. Zapisz i zamknij plik **dmpatterns_getstarted_device. PR** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową języka Python, która inicjuje zdalne ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań o bliźniaczych urządzeniach do wykrywania czasu ostatniego rozruchu dla tego urządzenia.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Za pomocą edytora tekstów Utwórz plik o nazwie **dmpatterns_getstarted_service. PR** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_service. PR** .

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Dodaj następujące deklaracje zmiennych. Zastąp `{IoTHubConnectionString}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub skopiowanymi wcześniej w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string). Zastąp `{deviceId}` wartość symbolu zastępczego identyfikatorem urządzenia zarejestrowanym w [Zarejestruj nowe urządzenie w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Dodaj następującą funkcję, aby wywołać metodę urządzenia w celu ponownego uruchomienia urządzenia docelowego, a następnie wyszukać bliźniaczych reprezentacji urządzenia i uzyskać czas ostatniego ponownego uruchomienia.

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

6. Zapisz i zamknij plik **dmpatterns_getstarted_service. PR** .

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. W innym wierszu polecenia Uruchom następujące polecenie, aby wyzwolić zdalne ponowne uruchomienie i zapytanie dla sznurka urządzenia, aby znaleźć czas ostatniego ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Odpowiedź urządzenia na metodę bezpośrednią w konsoli programu.

   Poniżej przedstawiono odpowiedzi urządzenia na metodę bezpośredniego ponownego uruchomienia:

   ![Dane wyjściowe aplikacji symulowanego urządzenia](./media/iot-hub-python-python-device-management-get-started/device.png)

   Poniżej przedstawiono usługę wywołującą metodę bezpośredniego ponownego uruchomienia i sondowanie sznurka urządzenia pod kątem stanu:

   ![Wyzwalanie danych wyjściowych usługi ponownego uruchomienia](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
