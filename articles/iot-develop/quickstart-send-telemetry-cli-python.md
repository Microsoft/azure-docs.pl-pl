---
title: Wysyłanie danych telemetrycznych urządzenia do usługi Azure IoT Hub — szybki start (Python)
description: W tym przewodniku szybki start użyjesz zestawu SDK urządzeń IoT Hub platformy Azure dla języka Python, aby wysyłać dane telemetryczne z urządzenia do centrum IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d73f8eeb7b69440f8db67d0b95b40ed6258ee8e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201791"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do usługi Azure IoT Hub (Python)

**Dotyczy**: [opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku szybki start nauczysz się podstawowy przepływ pracy tworzenia aplikacji dla urządzeń IoT. Za pomocą interfejsu wiersza polecenia platformy Azure można utworzyć centrum Azure IoT Hub i urządzenie, a następnie użyć zestawu Azure IoT Python SDK do utworzenia symulowanego urządzenia klienckiego i wysłania telemetrii do centrum. 

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku szybki start można uruchomić za pomocą Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która jest uruchamiana w przeglądarce. W przypadku korzystania z Cloud Shell nie trzeba instalować żadnych elementów. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
- [Python 3.7 +](https://www.python.org/downloads/). W przypadku innych obsługiwanych wersji języka Python zobacz [funkcje urządzenia IoT Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Aby upewnić się, że wersja języka Python jest aktualna, uruchom polecenie `python --version` . Jeśli masz zainstalowany język Python 2 i Python 3 i korzystasz ze środowiska Python 3, zainstaluj wszystkie biblioteki przy użyciu programu `pip3` . Dzięki temu biblioteki są instalowane w środowisku uruchomieniowym języka Python 3.
    > [!IMPORTANT]
    > W instalatorze języka Python wybierz opcję dodania języka **Python do ścieżki**. Jeśli masz już zainstalowany język Python 3,7 lub nowszy, upewnij się, że dodano folder instalacji języka Python do `PATH` zmiennej środowiskowej.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Wysyłanie komunikatów za pomocą zestawu SDK języka Python
W tej sekcji użyjesz zestawu SDK języka Python do wysyłania komunikatów z symulowanego urządzenia do centrum IoT Hub.

1. Otwórz nowe okno terminalu. Ten Terminal zostanie użyty do zainstalowania zestawu SDK języka Python i pracy z przykładowym kodem języka Python. Powinny teraz być otwarte dwa terminale: ten właśnie otwarty do pracy z językiem Python oraz powłoką interfejsu wiersza polecenia, która została użyta w poprzednich sekcjach, do wprowadzania poleceń interfejsu wiersza polecenia platformy Azure.       

1. Skopiuj [przykłady urządzeń zestawu Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) na komputer lokalny:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    Przejdź do katalogu *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples* :

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Zainstaluj zestaw SDK usługi Azure IoT Python:

    ```console
    pip install azure-iot-device
    ```
1. Ustaw parametry połączenia urządzenia jako zmienną środowiskową o nazwie `IOTHUB_DEVICE_CONNECTION_STRING` . Jest to ciąg uzyskany w poprzedniej sekcji po utworzeniu symulowanego urządzenia w języku Python.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > W przypadku polecenia Windows CMD nie ma znaków cudzysłowu otaczających parametry połączenia.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. W otwartej powłoki interfejsu wiersza polecenia Uruchom polecenie [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) , aby rozpocząć monitorowanie zdarzeń na symulowanym urządzeniu IoT.  Komunikaty o zdarzeniach będą drukowane w terminalu po ich nadejściu.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. W terminalu języka Python Uruchom kod dla zainstalowanego pliku przykładowego *simple_send_message. PR* . Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do centrum IoT Hub.

    Aby uruchomić przykład języka Python z terminalu:
    ```console
    python ./simple_send_message.py
    ```

    Opcjonalnie można uruchomić kod języka Python z przykładu w środowisku IDE języka Python:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Ponieważ kod w języku Python wysyła komunikat z urządzenia do centrum IoT Hub, komunikat pojawi się w powłoce interfejsu wiersza polecenia, które jest monitorowane zdarzenia:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Urządzenie jest teraz bezpiecznie połączone i wysyła dane telemetryczne do usługi Azure IoT Hub.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start nie są już potrzebne, można je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów.

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom polecenie [AZ Group Delete](/cli/azure/group#az-group-delete) . Spowoduje to usunięcie grupy zasobów, IoT Hub i utworzonej rejestracji urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [AZ Group list](/cli/azure/group#az-group-list) , aby potwierdzić, że grupa zasobów została usunięta.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start przedstawiono podstawowy przepływ pracy aplikacji Azure IoT służący do bezpiecznego łączenia urządzenia z chmurą i wysyłania danych telemetrycznych z urządzenia do chmury. Interfejs wiersza polecenia platformy Azure został użyty do utworzenia Centrum IoT i urządzenia, a następnie użyto zestawu Azure IoT Python SDK do utworzenia symulowanego urządzenia i wysłania telemetrii do centrum. 

Następnym krokiem jest zapoznanie się z zestawem SDK usługi Azure IoT Python za pomocą przykładów aplikacji.

- [Przykłady asynchroniczne](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): ten katalog zawiera asynchroniczne przykłady języka Python dla dodatkowych scenariuszy IoT Hub.
- [Próbki synchroniczne](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): ten katalog zawiera przykłady języka Python do użycia z scenariuszami w języku Python 2,7 lub synchroniczną zgodnością dla języka Python 3.5 +
- [Przykłady IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): ten katalog zawiera przykłady języka Python do pracy z modułami krawędziowymi i urządzeniami podrzędnymi.