---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Python z platformą Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką języka Python z aplikacją usługi Azure IoT Central. Użytkownik modyfikuje automatycznie wygenerowany szablon urządzenia przez dodanie widoków, które umożliwiają operatorowi współpracujące z podłączonym urządzeniem.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 47a178447533493911ae1f8ada5c617119bc3479
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987692"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Samouczek: Tworzenie aplikacji klienckiej i łączenie jej z aplikacją usługi Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku przedstawiono sposób, w jaki deweloper urządzenia nawiązuje połączenie aplikacji klienckiej języka Python z aplikacją usługi Azure IoT Central. Aplikacja języka Python symuluje zachowanie urządzenia z termostatem. Gdy aplikacja nawiązuje połączenie z IoT Central, wysyła identyfikator modelu urządzenia termostatu. IoT Central korzysta z identyfikatora modelu, aby pobrać model urządzenia i utworzyć dla Ciebie szablon urządzenia. Dodano dostosowania i widoki do szablonu urządzenia, aby umożliwić operatorowi współpracujące z urządzeniem.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz i uruchom kod urządzenia w języku Python i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Dodawanie niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie polecenia w celu sterowania urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja IoT Central platformy Azure utworzona przy użyciu szablonu **aplikacji niestandardowej** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md). Aplikacja musi zostać utworzona w dniu lub po 14 lipca 2020.
* Komputer deweloperski z zainstalowanym programem [Python](https://www.python.org/) w wersji 3,7 lub nowszej. `python --version`Aby sprawdzić swoją wersję, można uruchomić w wierszu polecenia. Język Python jest dostępny dla wielu różnych systemów operacyjnych. W instrukcjach przedstawionych w tym samouczku założono, że uruchomiono polecenie **Python** w wierszu polecenia systemu Windows.
* Lokalna kopia usługi [Microsoft Azure IoT SDK dla](https://github.com/Azure/azure-iot-sdk-python) repozytorium GitHub w języku Python, która zawiera przykładowy kod. Użyj tego linku, aby pobrać kopię repozytorium: [Pobierz plik zip](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Następnie Rozpakuj plik do odpowiedniej lokalizacji na komputerze lokalnym.

## <a name="review-the-code"></a>Przeglądanie kodu

W oknie Edytor tekstów na kopii Microsoft Azure IoT SDK dla języka Python pobranego wcześniej Otwórz plik *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP/simple_thermostat. PR* .

Po uruchomieniu przykładu w celu nawiązania połączenia z IoT Centralm program używa usługi Device Provisioning (DPS) do zarejestrowania urządzenia i wygenerowania parametrów połączenia. Przykład pobiera informacje o połączeniu usługi DPS wymagane z środowiska wiersza polecenia.

`main`Funkcja:

* Używa programu DPS do aprowizacji urządzenia. Informacje o aprowizacji zawierają identyfikator modelu.
* Tworzy `Device_client` obiekt i ustawia `dtmi:com:example:Thermostat;1` Identyfikator modelu przed otwarciem połączenia.
* Wysyła `maxTempSinceLastReboot` Właściwość do IoT Central.
* Tworzy odbiornik dla `getMaxMinReport` polecenia.
* Tworzy odbiornik właściwości, aby nasłuchiwać aktualizacji właściwości z możliwością zapisu.
* Uruchamia pętlę, aby wysyłać dane telemetryczne dotyczące temperatury co 10 sekund.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

`provision_device`Funkcja używa funkcji DPS do aprowizacji urządzenia i rejestrowania go w IoT Central. Funkcja zawiera identyfikator modelu urządzenia w ładunku aprowizacji:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener`Funkcja obsługuje żądania poleceń, uruchamia funkcję, `max_min_handler` gdy urządzenie otrzyma `getMaxMinReport` polecenie, i uruchamia `create_max_min_report_response` funkcję w celu wygenerowania odpowiedzi:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener`Obsługuje aktualizacje właściwości do zapisu, takie jak `targetTemperature` i GENERUJE odpowiedź JSON:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

`send_telemetry_from_thermostat`Funkcja wysyła komunikaty telemetryczne do IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić przykładową aplikację, Otwórz środowisko wiersza polecenia i przejdź do folderu *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP* zawierającego plik przykładu *simple_thermostat. PR* .

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Zainstaluj wymagane pakiety:

```cmd/sh
pip install azure-iot-device
```

Uruchom przykład:

```cmd/sh
python simple_thermostat.py
```

Poniższe dane wyjściowe pokazują urządzenie, które rejestruje i nawiązuje połączenie z IoT Central. Próbka wysyła `maxTempSinceLastReboot` Właściwość przed rozpoczęciem wysyłania telemetrii:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Można sprawdzić, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz korzystać z zestawu samouczków IoT Central i dowiedzieć się więcej na temat tworzenia rozwiązania IoT Central, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)

Jako deweloper urządzenia teraz znasz podstawowe informacje dotyczące sposobu tworzenia urządzenia przy użyciu języka Python, a niektóre sugerowane następne kroki to:

* Przeczytaj [co to są szablony urządzeń?](./concepts-device-templates.md) aby dowiedzieć się więcej na temat roli szablonów urządzeń podczas implementowania kodu urządzenia.
* Aby dowiedzieć się więcej o sposobach rejestrowania urządzeń w usłudze IoT Central i sposobach IoT Central zabezpieczania połączeń urządzeń, przeczytaj artykuł [wprowadzenie do usługi Azure IoT Central](./concepts-get-connected.md) .
* Odczytaj [ładunki telemetryczne, właściwości i poleceń,](concepts-telemetry-properties-commands.md) aby dowiedzieć się więcej o danych, które są wymieniane przez urządzenia z IoT Central.
