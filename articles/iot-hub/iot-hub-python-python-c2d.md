---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (Python) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla języka Python. Zmodyfikowana aplikacja urządzenia jest modyfikowana w celu odbierania komunikatów z chmury do urządzenia i modyfikowania aplikacji zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f0760f6e61904295771ba349f8101e2d6dc6afe3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759749"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Wysyłanie wiadomości z chmury do urządzenia za pomocą usługi IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod symulowanej aplikacji urządzenia, która wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się na [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md)hub . To pokazuje, jak:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć w [przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli Python:

* **SimulatedDevice.py**, zmodyfikowanej wersji aplikacji utworzonej w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-python.md), który łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage.py**, który wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia za pośrednictwem usługi IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, aby symulować urządzenie i odbierać komunikaty z chmury do urządzenia z centrum IoT hub.

1. W wierszu polecenia w katalogu roboczym zainstaluj **zestaw SDK urządzenia usługi Azure IoT Hub dla języka Python:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Za pomocą edytora tekstu utwórz plik o nazwie **SimulatedDevice.py**.

1. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SimulatedDevice.py:**

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Dodaj następujący kod do **pliku SimulatedDevice.py.** Zastąp wartość symbolu `{deviceConnectionString}` zastępczego ciągiem połączenia urządzenia dla urządzenia utworzonego w [przewodniku Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT Hub:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Dodaj następującą funkcję, aby wydrukować odebrane wiadomości do konsoli:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Dodaj następujący kod, aby zainicjować klienta i poczekać na odebranie komunikatu z chmury do urządzenia:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Dodaj następującą funkcję główną:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Zapisz i zamknij plik **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-python.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, która wysyła komunikaty z chmury do urządzenia do symulowanej aplikacji urządzenia. Potrzebny jest identyfikator urządzenia dodanego w danych [telemetrycznych wysyłania z urządzenia do szybkiego startu centrum IoT Hub.](quickstart-send-telemetry-python.md) Potrzebny jest również ciąg połączenia centrum IoT skopiowany wcześniej w [pliku Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

1. W katalogu roboczym otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Usługi Azure IoT Hub dla języka Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Za pomocą edytora tekstu utwórz plik o nazwie **SendCloudToDeviceMessage.py**.

1. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SendCloudToDeviceMessage.py:**

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Dodaj następujący kod do **pliku SendCloudToDeviceMessage.py.** Zastąp `{iot hub connection string}` wartości i `{device id}` symbol zastępczy ciągiem połączenia i identyfikatorem urządzenia, który wcześniej zauważyłeś:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Dodaj następujący kod, aby wysyłać wiadomości do urządzenia:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Dodaj następującą funkcję główną:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Zapisz i zamknij **plik SendCloudToDeviceMessage.py.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym uruchom następujące polecenie, aby nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    python SimulatedDevice.py
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-python-python-c2d/device-1.png)

1. Otwórz nowy wiersz polecenia w katalogu roboczym i uruchom następujące polecenie, aby wysyłać komunikaty z chmury do urządzenia:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Uruchamianie aplikacji w celu wysłania polecenia chmury do urządzenia](./media/iot-hub-python-python-c2d/service.png)

1. Zanotuj wiadomości odebrane przez urządzenie.

    ![Odebrano wiadomość](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
