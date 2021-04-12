---
title: Wysyłanie danych telemetrycznych urządzenia do usługi Azure IoT Central — szybki start (Python)
description: W tym przewodniku szybki start użyjesz zestawu SDK urządzeń IoT Hub platformy Azure dla języka Python, aby wysyłać dane telemetryczne z urządzenia do usługi IoT Central.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047171"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do usługi Azure IoT Central (Python)

**Dotyczy**: [opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku szybki start nauczysz się podstawowy przepływ pracy tworzenia aplikacji dla urządzeń IoT. Najpierw Użyj usługi Azure IoT Central, aby utworzyć aplikację w chmurze. Następnie użyj zestawu SDK języka Python usługi Azure IoT, aby utworzyć symulowane urządzenie, nawiązać połączenie z usługą IoT Central i wysłać dane telemetryczne z urządzenia do chmury. 

## <a name="prerequisites"></a>Wymagania wstępne
- [Python 3.7 +](https://www.python.org/downloads/). W przypadku innych obsługiwanych wersji języka Python zobacz [funkcje urządzenia IoT Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Aby upewnić się, że wersja języka Python jest aktualna, uruchom polecenie `python --version` . Jeśli masz zainstalowany język Python 2 i Python 3 i korzystasz ze środowiska Python 3, zainstaluj wszystkie biblioteki przy użyciu programu `pip3` . Uruchomienie programu `pip3` gwarantuje, że biblioteki są zainstalowane w środowisku uruchomieniowym języka Python 3.
    > [!IMPORTANT]
    > W instalatorze języka Python wybierz opcję dodania języka **Python do ścieżki**. Jeśli masz już zainstalowany język Python 3,7 lub nowszy, upewnij się, że dodano folder instalacji języka Python do `PATH` zmiennej środowiskowej.

## <a name="create-an-application"></a>Tworzenie aplikacji
W tej sekcji utworzysz aplikację IoT Centralową. IoT Central to platforma aplikacji IoT oparta na portalu, która pomaga w zmniejszeniu złożoności i kosztów tworzenia rozwiązań IoT oraz zarządzania nimi.

Aby utworzyć aplikację usługi Azure IoT Central:
1. Przejdź do [usługi Azure IoT Central](https://apps.azureiotcentral.com/) i zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.
1. Przejdź do opcji **kompilacja** i wybierz pozycję **aplikacje niestandardowe**.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Strona początkowa IoT Central":::
1. W polu **Nazwa aplikacji** wprowadź unikatową nazwę lub użyj wygenerowanej nazwy.
1. W polu **adres URL** wprowadź zapamiętany prefiks adresu URL aplikacji lub użyj wygenerowanego PREFIKSU adresu URL.
1. Pozostaw **szablon aplikacji** ustawiony na *aplikację niestandardową*. Lista rozwijana może zawierać inne opcje, jeśli istnieją już szablony na koncie.
1. Wybierz opcję **planu cenowego** . 
    - Aby bezpłatnie korzystać z aplikacji przez siedem dni, wybierz opcję **bezpłatna**. Możesz przekonwertować bezpłatną aplikację na standardową cenę przed jej wygaśnięciem.
    - Opcjonalnie możesz wybrać standardowy plan cenowy. W przypadku wybrania cennika standardowego są wyświetlane więcej opcji i należy ustawić **katalog**, **subskrypcję platformy Azure** i **lokalizację**. Aby dowiedzieć się więcej o cenach, zobacz [Cennik usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Katalog** jest Azure Active Directory, w którym tworzysz aplikację. Azure Active Directory zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz Azure Active Directory, jest on tworzony podczas tworzenia subskrypcji platformy Azure.
        - **Subskrypcja platformy Azure** umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją [utworzyć bezpłatnie](https://azure.microsoft.com/free/). Po utworzeniu subskrypcji Wróć do strony IoT Central **nowej aplikacji** . Twoja nowa subskrypcja zostanie wyświetlona na liście rozwijanej **subskrypcja platformy Azure** .
        - **Lokalizacja jest lokalizacją** geograficzną [platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której tworzysz aplikację. Wybierz lokalizację, która jest fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Po wybraniu lokalizacji nie można przenieść aplikacji do innej lokalizacji.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Okno dialogowe IoT Central nowej aplikacji":::
1. Wybierz przycisk **Utwórz**.
    
    Po utworzeniu aplikacji IoT Central przekieruje Cię do pulpitu nawigacyjnego aplikacji.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central nowy pulpit nawigacyjny aplikacji":::

## <a name="add-a-device"></a>Dodawanie urządzenia
W tej sekcji dodasz nowe urządzenie do aplikacji IoT Central. Urządzenie to wystąpienie szablonu urządzenia, które reprezentuje rzeczywiste lub symulowane urządzenie, które zostanie połączone z aplikacją. 

Aby utworzyć nowe urządzenie:
1. W lewym okienku wybierz pozycję **urządzenia**, a następnie wybierz pozycję **+ Nowy**. Spowoduje to otwarcie okna dialogowego nowe urządzenie.
1. Pozostaw ustawiony **szablon urządzenia** jako *nieprzypisany*.

    > [!NOTE]
    > W tym przewodniku szybki start dla uproszczenia nastąpi podłączenie symulowanego urządzenia korzystającego z nieprzypisanego szablonu. W przypadku kontynuowania korzystania z usługi IoT Central do zarządzania urządzeniami należy zapoznać się z tematem korzystanie z szablonów urządzeń. Aby zapoznać się z omówieniem pracy z szablonami urządzeń, zobacz [Szybki Start: Dodawanie symulowanego urządzenia do aplikacji IoT Central](../iot-central/core/quick-create-simulated-device.md).
1. Ustaw przyjazną **nazwę urządzenia** i **Identyfikator urządzenia**. Opcjonalnie można użyć wygenerowanych wartości.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Okno dialogowe IoT Central nowego urządzenia":::
1. Wybierz przycisk **Utwórz**.

    Utworzone urządzenie zostanie wyświetlone na liście **wszystkie urządzenia** .
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Lista wszystkich urządzeń IoT Central":::
    
Aby pobrać szczegóły połączenia dla nowego urządzenia:
1. Na liście **wszystkie urządzenia** kliknij dwukrotnie nazwę urządzenia połączonego, aby wyświetlić szczegóły. 
1. W górnym menu wybierz polecenie **Połącz**.

    W oknie dialogowym **połączenie z urządzeniem** są wyświetlane szczegóły połączenia:  :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="szczegóły połączenia urządzenia IoT Central":::
1. Skopiuj następujące wartości z okna dialogowego **połączenie z urządzeniem** do bezpiecznej lokalizacji. Zostaną one użyte w następnej sekcji, aby podłączyć urządzenie do IoT Central.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>Wysyłanie komunikatów i monitorowanie telemetrii
W tej sekcji użyjesz zestawu SDK języka Python do utworzenia symulowanego urządzenia i wysłania telemetrii do aplikacji IoT Central. 

1. Otwórz Terminal przy użyciu polecenia Windows CMD lub PowerShell lub bash (dla systemu Windows lub Linux). Terminal zostanie użyty do zainstalowania zestawu SDK języka Python, zaktualizowania zmiennych środowiskowych i uruchomienia przykładu kodu języka Python.

1. Skopiuj [przykłady urządzeń zestawu Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) do komputera lokalnego.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Przejdź do katalogu *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples* .

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Zainstaluj zestaw SDK usługi Azure IoT Python.

    ```console
    pip install azure-iot-device
    ```

1. Ustaw następujące zmienne środowiskowe, aby umożliwić symulowanemu urządzeniu łączenie się z IoT Central. W `ID_SCOPE` przypadku `DEVICE_ID` opcji, i `DEVICE_KEY` należy użyć wartości zapisanych w oknie dialogowym IoT Central *połączenia z urządzeniem* .

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > W przypadku polecenia Windows CMD nie ma znaków cudzysłowu otaczających parametry połączenia lub inne wartości zmiennych.

    **Program PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux lub Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. W terminalu uruchom kod pliku przykładowego * simple_send_temperature. pr. Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do IoT Central.

    Aby uruchomić przykład języka Python z terminalu:
    ```console
    python ./simple_send_temperature.py
    ```

    Opcjonalnie można uruchomić kod języka Python z przykładu w środowisku IDE języka Python:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Ponieważ kod języka Python wysyła komunikat z urządzenia do aplikacji IoT Central, komunikaty są wyświetlane na karcie **dane pierwotne** urządzenia w IoT Central. Może być konieczne odświeżenie strony w celu wyświetlenia najnowszych komunikatów.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Zrzut ekranu przedstawiający IoT Central nieprzetworzonych danych wyjściowych":::

Urządzenie jest teraz bezpiecznie połączone i wysyła dane telemetryczne do usługi Azure IoT.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już zasobów IoT Central utworzonych w ramach tego samouczka, możesz je usunąć z portalu IoT Central. Opcjonalnie, jeśli planujesz kontynuować pracę z dokumentacją w tym przewodniku, możesz zachować utworzoną aplikację i użyć jej ponownie dla innych przykładów.

Aby usunąć przykładową aplikację platformy Azure IoT Central i wszystkie jej urządzenia i zasoby:
1. Wybierz pozycję **Administracja**  >  **Twoja aplikacja**.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono podstawowy przepływ pracy aplikacji Azure IoT służący do bezpiecznego łączenia urządzenia z chmurą i wysyłania danych telemetrycznych z urządzenia do chmury. Za pomocą usługi Azure IoT Central utworzyć aplikację i urządzenie, a następnie użyć zestawu Azure IoT Python SDK do utworzenia symulowanego urządzenia i wysłania telemetrii. Używane są również IoT Central do monitorowania danych telemetrycznych.

Następnym krokiem jest zapoznanie się z zestawem SDK usługi Azure IoT Python za pomocą przykładów aplikacji.

- [Przykłady asynchroniczne](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): ten katalog zawiera asynchroniczne przykłady języka Python dla dodatkowych scenariuszy IoT Hub.
- [Próbki synchroniczne](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): ten katalog zawiera przykłady języka Python do użycia z scenariuszami w języku Python 2,7 lub synchroniczną zgodnością dla języka Python 3.6 +
- [Przykłady IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): ten katalog zawiera przykłady języka Python do pracy z modułami krawędziowymi i urządzeniami podrzędnymi.
