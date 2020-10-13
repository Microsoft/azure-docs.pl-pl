---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Python z platformą Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką języka Python z aplikacją usługi Azure IoT Central. Szablon urządzenia można utworzyć przez zaimportowanie modelu możliwości urządzenia i dodanie widoków, które umożliwiają współpracę z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968133"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku przedstawiono sposób, w jaki deweloper urządzenia nawiązuje połączenie aplikacji klienckiej języka Python z aplikacją usługi Azure IoT Central. Aplikacja języka Python symuluje zachowanie urządzenia czujnika środowiska. Korzystając z przykładowego _modelu możliwości urządzenia_ , można utworzyć _szablon urządzenia_ w IoT Central. Dodaj widoki do szablonu urządzenia, aby umożliwić operatorowi współpracujące z urządzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportuj model możliwości urządzenia, aby utworzyć szablon urządzenia.
> * Dodawanie domyślnych i niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia i Dodaj rzeczywiste urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia w języku Python i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie poleceń synchronicznych i asynchronicznych w celu sterowania urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja IoT Central platformy Azure utworzona przy użyciu szablonu **aplikacji niestandardowej** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md). Aplikacja musi zostać utworzona w dniu lub po 07/14/2020.
* Komputer deweloperski z zainstalowanym programem [Python](https://www.python.org/) w wersji 3,7 lub nowszej. `python3 --version`Aby sprawdzić swoją wersję, można uruchomić w wierszu polecenia. Język Python jest dostępny dla wielu różnych systemów operacyjnych. W instrukcjach przedstawionych w tym samouczku założono, że uruchomiono polecenie **python3** w wierszu polecenia systemu Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Tworzenie aplikacji w języku Python

Poniższe kroki pokazują, jak utworzyć aplikację kliencką języka Python, która nawiązuje połączenie z rzeczywistym urządzeniem dodanym do aplikacji. Ta aplikacja języka Python symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia przejdź do `environmental-sensor` folderu utworzonego wcześniej.

1. Aby zainstalować wymagane biblioteki, uruchom następujące polecenia:

    ```cmd
    pip install azure-iot-device
    ```

1. Utwórz plik o nazwie **environmental_sensor. PR** w `environmental-sensor` folderze.

1. Dodaj następujące `import` instrukcje na początku pliku **environmental_sensor. PR** :

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

1. Dodaj następującą `main` deklarację funkcji asynchronicznej i zmiennych do pliku:

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

    Aktualizowanie symboli zastępczych `{your Scope ID}` , `{your Device ID}` i `{your Primary Key}` z wartościami, które zostały wcześniej wykonane. W prawdziwej aplikacji nie należy niczego zakodować w aplikacji.

    Wszystkie poniższe definicje funkcji i kod są zagnieżdżone w `main` funkcji.

1. Dodaj następujące dwie funkcje wewnątrz funkcji, `main` Aby zarejestrować urządzenie i połączyć je z aplikacją IoT Central. Rejestracja używa usługi Azure Device Provisioning:

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
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. Dodaj następującą funkcję wewnątrz funkcji, `main` Aby wysłać dane telemetryczne do aplikacji IoT Central:

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    Nazwy elementów telemetrycznych ( `temp` i `humid` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujące funkcje wewnątrz funkcji, `main` Aby obsłużyć polecenia wywoływane z aplikacji IoT Central:

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

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

    Nazwy poleceń ( `blink` , `turnon` , `turnoff` i `rundiagnostics` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

    Obecnie IoT Central nie korzysta ze schematu odpowiedzi zdefiniowanego w modelu możliwości urządzenia. W przypadku polecenia synchronicznego ładunek odpowiedzi może być dowolnym prawidłowym kodem JSON. W przypadku polecenia asynchronicznego urządzenie powinno natychmiast zwrócić odpowiedź 202, a następnie zgłosić aktualizację właściwości po zakończeniu pracy. Format raportowanej aktualizacji właściwości to:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operator może wyświetlać ładunek odpowiedzi w historii poleceń.

1. Dodaj następujące funkcje wewnątrz funkcji, `main` Aby obsłużyć aktualizacje właściwości wysyłane z aplikacji IoT Central. Komunikat wysyłany przez urządzenie w odpowiedzi na [aktualizację właściwości zapisywalnej](concepts-telemetry-properties-commands.md#writeable-property-types) musi zawierać `av` `ac` pola i. `ad`Pole jest opcjonalne:

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
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

    Gdy operator ustawia właściwość do zapisu w aplikacji IoT Central, aplikacja używa odpowiedniej właściwości przędzy urządzenia do wysłania wartości do urządzenia. Następnie urządzenie odpowiada za pomocą właściwości zgłoszonej przez urządzenie. Gdy IoT Central otrzymuje raportowaną wartość właściwości, aktualizuje widok właściwości ze stanem **zsynchronizowane**.

    Nazwy właściwości ( `name` i `brightness` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujące funkcje wewnątrz funkcji, `main` Aby kontrolować aplikację:

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
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
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

1. Zapisz plik **environmental_sensor. PR** .

## <a name="run-your-python-application"></a>Uruchamianie aplikacji języka Python

Aby uruchomić aplikację kliencką urządzenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd
python3 environmental_sensor.py
```

Możesz zobaczyć, że urządzenie nawiązuje połączenie z aplikacją IoT Central platformy Azure i zacznie wysyłać dane telemetryczne:

![Uruchom aplikację kliencką](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Można sprawdzić, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

![Obserwuj aplikację kliencką](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz korzystać z zestawu samouczków IoT Central i dowiedzieć się więcej na temat tworzenia rozwiązania IoT Central, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)

Jako deweloper urządzenia teraz znasz podstawowe informacje dotyczące sposobu tworzenia urządzenia przy użyciu języka Python, a niektóre sugerowane następne kroki to:

* Przeczytaj [co to są szablony urządzeń?](./concepts-device-templates.md) aby dowiedzieć się więcej na temat roli szablonów urządzeń podczas implementowania kodu urządzenia.
* Aby dowiedzieć się więcej o sposobach rejestrowania urządzeń w usłudze IoT Central i sposobach IoT Central zabezpieczania połączeń urządzeń, przeczytaj artykuł [wprowadzenie do usługi Azure IoT Central](./concepts-get-connected.md) .