---
title: Wysyłanie danych telemetrycznych urządzenia do Azure IoT Hub Szybki start (Python)
description: W tym przewodniku Szybki start użyjemy zestawu SDK Azure IoT Hub dla języka Python do wysyłania danych telemetrycznych z urządzenia do centrum IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: ea0b161a9038666e1e7ddd5a6c6af2078afff8aa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766542"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum Azure IoT Hub (Python)

**Dotyczy:** [Opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku Szybki start poznasz podstawowy przepływ pracy tworzenia aplikacji urządzeń IoT. Interfejs wiersza polecenia platformy Azure umożliwia utworzenie centrum Azure IoT Hub i urządzenia, a następnie użycie zestawu SDK języka Python usługi Azure IoT do utworzenia symulowanego urządzenia klienckiego i wysłania danych telemetrycznych do centrum. 

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) je bezpłatnie.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku Szybki start można uruchamiać przy użyciu Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia uruchamianej w przeglądarce. Jeśli używasz Cloud Shell, nie musisz niczego instalować. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Aby uzyskać informacje o innych obsługiwanych wersjach języka Python, [zobacz Funkcje urządzeń Azure IoT.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)
    
    Aby upewnić się, że wersja języka Python jest aktualne, `python --version` uruchom . Jeśli masz zainstalowane środowisko języka Python 2 i Python 3 i korzystasz ze środowiska języka Python 3, zainstaluj wszystkie biblioteki przy użyciu narzędzia `pip3` . To polecenie zapewnia, że biblioteki są zainstalowane w środowisku uruchomieniowym języka Python 3.
    > [!IMPORTANT]
    > W instalatorze języka Python wybierz opcję Dodaj język **Python do ścieżki**. Jeśli masz już zainstalowane środowisko języka Python w wersji 3.7 lub wyższej, upewnij się, że folder instalacji języka Python został dodany do `PATH` zmiennej środowiskowej .

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Wysyłanie komunikatów przy użyciu zestawu SDK języka Python
W tej sekcji użyjemy zestawu SDK języka Python do wysyłania komunikatów z symulowanego urządzenia do centrum IoT.

1. Otwórz nowe okno terminalu. Użyjesz tego terminalu do zainstalowania zestawu SDK języka Python i pracy z przykładowym kodem języka Python. Powinny być teraz otwarte dwa terminale: ten, który został właśnie otwarty do pracy z językem Python, oraz powłoka interfejsu wiersza polecenia użyta w poprzednich sekcjach do wprowadzania poleceń interfejsu wiersza polecenia platformy Azure.       

1. Skopiuj [przykłady urządzeń zestawu SDK języka Python usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) na maszynę lokalną:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Przejdź do katalogu *azure-iot-sdk-python/azure-iot-device/samples/pnp:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Zainstaluj zestaw SDK języka Python usługi Azure IoT:

    ```console
    pip install azure-iot-device
    ```
1. Ustaw obie poniższe zmienne środowiskowe, aby umożliwić urządzeniu symulowanym łączenie się z usługą Azure IoT.
    * Ustaw zmienną środowiskową o nazwie `IOTHUB_DEVICE_CONNECTION_STRING` . Jako wartości zmiennej użyj parametrów połączenia urządzenia, które zostały zapisane w poprzedniej sekcji.
    * Ustaw zmienną środowiskową o nazwie `IOTHUB_DEVICE_SECURITY_TYPE` . Dla zmiennej użyj wartości ciągu literału `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > W przypadku polecenia cmd systemu Windows nie ma znaków cudzysłowu otaczających wartości ciągu dla każdej zmiennej.

    **Program PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux lub Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. W otwartej powłoki interfejsu wiersza polecenia uruchom polecenie [az iot hub monitor-events,](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) aby rozpocząć monitorowanie zdarzeń na symulowanym urządzeniu IoT.  Komunikaty o zdarzeniach są wyświetlane w terminalu po ich przybyciu.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. W terminalu języka Python uruchom kod zainstalowanego przykładowego pliku *simple_thermostat.py.* Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do centrum IoT.

    Aby uruchomić przykład języka Python z terminalu:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Ten przykładowy kod używa usługi Azure IoT Plug and Play, która umożliwia integrowanie inteligentnych urządzeń z rozwiązaniami bez konieczności ręcznej konfiguracji.  Domyślnie większość przykładów w tej dokumentacji używa IoT Plug and Play. Aby dowiedzieć się więcej o zaletach usługi IoT PnP i przypadkach jej używania lub nie, zobacz Co to jest [IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 Gdy kod języka Python wysyła komunikat z urządzenia do centrum IoT Hub, komunikat jest wyświetlany w powłoki interfejsu wiersza polecenia, która monitoruje zdarzenia:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Urządzenie jest teraz bezpiecznie połączone i wysyła dane telemetryczne do Azure IoT Hub.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już zasobów platformy Azure utworzonych w tym przewodniku Szybki start, możesz je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów.

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom [polecenie az group delete.](/cli/azure/group#az_group_delete) To polecenie usuwa grupę zasobów, IoT Hub i utworzoną rejestrację urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [az group list,](/cli/azure/group#az_group_list) aby potwierdzić usunięcie grupy zasobów.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start poznaliśmy podstawowy przepływ pracy aplikacji usługi Azure IoT, który umożliwia bezpieczne łączenie urządzenia z chmurą i wysyłanie danych telemetrycznych z urządzenia do chmury. Przy użyciu interfejsu wiersza polecenia platformy Azure utworzyliśmy centrum IoT i urządzenie, a następnie usłudze Azure IoT Python SDK utworzyliśmy symulowane urządzenie i wysłaliśmy dane telemetryczne do centrum. 

W następnym kroku zapoznaj się z zestawem SDK języka Python usługi Azure IoT za pomocą przykładów aplikacji.

- [Przykłady asynchroniczne:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)ten katalog zawiera asynchroniczne przykłady języka Python dla IoT Hub scenariuszy.
- [Przykłady synchroniczne:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)ten katalog zawiera przykłady języka Python do użycia z językem Python 2.7 lub synchronicznymi scenariuszami zgodności dla języka Python 3.6+
- [IoT Edge przykłady:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)ten katalog zawiera przykłady języka Python do pracy z modułami usługi Edge i urządzeniami podrzędne.
