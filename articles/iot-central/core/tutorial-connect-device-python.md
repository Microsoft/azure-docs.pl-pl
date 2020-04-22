---
title: Samouczek — łączenie ogólnej aplikacji klienta języka Python z usługą Azure IoT Central | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak jako deweloper urządzeń połączyć urządzenie z uruchomieniem aplikacji klienckiej języka Python z aplikacją Azure IoT Central. Szablon urządzenia można utworzyć, importując model możliwości urządzenia i dodaj widoki, które umożliwiają interakcję z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6c44c81db78fa76eeaf4b7181cca34fb8e81523
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758187"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak jako deweloper urządzeń połączyć aplikację kliencką języka Python z aplikacją Azure IoT Central. Aplikacja Python symuluje zachowanie urządzenia czujnika środowiska. Przykładowego _modelu możliwości urządzenia_ służy do tworzenia _szablonu urządzenia_ w centrum IoT. Widoki są dodawanye do szablonu urządzenia, aby umożliwić operatorowi interakcję z urządzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportowanie modelu możliwości urządzenia w celu utworzenia szablonu urządzenia.
> * Dodawanie widoków domyślnych i niestandardowych do szablonu urządzenia.
> * Opublikuj szablon urządzenia i dodaj prawdziwe urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia Python i zobacz, jak łączy się z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzenia.
> * Wywołaj polecenia synchroniczne i asynchroniczne, aby sterować urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja Azure IoT Central utworzona przy użyciu szablonu **aplikacji niestandardowej.** Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Komputer dewelopera z [zainstalowanym pythonem](https://www.python.org/) w wersji 3.7 lub nowszej. Można uruchomić `python3 --version` w wierszu polecenia, aby sprawdzić swoją wersję. Python jest dostępny dla szerokiej gamy systemów operacyjnych. Instrukcje w tym samouczku zakładają, że używasz polecenia **python3** w wierszu polecenia systemu Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Tworzenie aplikacji w języku Python

Poniższe kroki pokazują, jak utworzyć aplikację kliencką języka Python, która łączy się z rzeczywistym urządzeniem dodanym do aplikacji. Ta aplikacja Języka Python symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia `environmental-sensor` przejdź do folderu utworzonego wcześniej.

1. Aby zainstalować wymagane biblioteki, uruchom następujące polecenia:

    ```cmd
    pip install azure-iot-device
    ```

1. Utwórz plik o nazwie **environmental_sensor.py** w folderze. `environmental-sensor`

1. Dodaj następujące `import` instrukcje na początku **pliku environmental_sensor.py:**

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Dodaj do pliku następującą `main` funkcję asynchronizacyjną i deklaracje zmiennych:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Zaktualizuj `{your Scope ID}` `{your Device ID}`symbole `{your Primary Key}` zastępcze , a także wartościami, które zostały wcześniej zanotowane. W prawdziwej aplikacji nie koduj na miejscu te informacje w aplikacji.

    Wszystkie następujące definicje funkcji i kod `main` są zagnieżdżone w ramach funkcji.

1. Dodaj następujące dwie funkcje wewnątrz funkcji, `main` aby zarejestrować urządzenie i podłączyć je do aplikacji IoT Central. Rejestracja korzysta z usługi inicjowania obsługi administracyjnej urządzeń platformy Azure:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Dodaj następującą funkcję `main` wewnątrz funkcji, aby wysłać dane telemetryczne do aplikacji IoT Central:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    Nazwy elementów telemetrycznych`temp` `humid`( i ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujące funkcje `main` wewnątrz funkcji do obsługi poleceń wywoływanych z aplikacji IoT Central:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    `blink`Nazwy poleceń ( , `turnon` `turnoff`, `rundiagnostics`, i ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

    Obecnie usługa IoT Central nie używa schematu odpowiedzi zdefiniowanego w modelu możliwości urządzenia. Dla polecenia synchronicznego ładunku odpowiedzi może być dowolny prawidłowy JSON. W przypadku polecenia asynchronicznego urządzenie powinno natychmiast zwrócić odpowiedź 202, a następnie zgłoszoną aktualizację właściwości po zakończeniu pracy. Format zgłoszonej aktualizacji właściwości jest:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operator może wyświetlić ładunek odpowiedzi w historii poleceń.

1. Dodaj następujące funkcje `main` wewnątrz funkcji do obsługi aktualizacji właściwości wysyłanych z aplikacji IoT Central:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    Gdy operator ustawia zapisowalne właściwości w aplikacji IoT Central, aplikacja używa bliźniaczej reprezentacji urządzenia żądanej właściwości, aby wysłać wartość do urządzenia. Urządzenie następnie odpowiada przy użyciu urządzenia bliźniaczej reprezentacji zgłaszane właściwości. Gdy aplikacja IoT Central odbiera zgłoszoną wartość właściwości, aktualizuje widok właściwości o stan **zsynchronizowany**.

    Nazwy właściwości (`name` i `brightness`) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujące funkcje `main` wewnątrz funkcji, aby kontrolować aplikację:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Zapisz plik **environmental_sensor.py.**

## <a name="run-your-python-application"></a>Uruchamianie aplikacji Języka Python

Aby uruchomić aplikację kliencką urządzenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd
python3 environmental_sensor.py
```

Możesz zobaczyć, że urządzenie łączy się z aplikacją Azure IoT Central i rozpoczyna wysyłanie danych telemetrycznych:

![Uruchamianie aplikacji klienckiej](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Możesz zobaczyć, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

![Obserwuj aplikację kliencką](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Następne kroki

Jako deweloper urządzeń, teraz, gdy już znasz podstawy tworzenia urządzenia przy użyciu node.js, niektóre sugerowane następne kroki są następujące:

- Dowiedz się, jak połączyć rzeczywiste urządzenie z centrum IoT w [artykule Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central.](./howto-connect-devkit.md)
- Przeczytaj artykuł [Nawiązanie połączenia z usługą Azure IoT Central,](./concepts-get-connected.md) aby dowiedzieć się więcej o rejestrowaniu urządzeń w usłudze IoT Central i sposobie zabezpieczania połączeń urządzeń przez usługę IoT Central.

Jeśli wolisz kontynuować tworzenie samouczków W centrum IoT i dowiedzieć się więcej o tworzeniu rozwiązania Centrum IoT, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)