---
title: Wysyłanie danych telemetrycznych urządzenia Azure IoT Hub szybki start (Node.js)
description: W tym przewodniku Szybki start użyjemy zestawu SDK Azure IoT Hub dla usługi Node.js do wysyłania danych telemetrycznych z urządzenia do centrum IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876980"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT (Node.js)

**Dotyczy:** [Opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku Szybki start poznasz podstawowy przepływ pracy tworzenia aplikacji urządzeń IoT. Interfejs wiersza polecenia platformy Azure umożliwia utworzenie centrum Azure IoT Hub i symulowanego urządzenia, a następnie użycie zestawu SDK usługi Azure IoT Node.js w celu uzyskania dostępu do urządzenia i wysłania danych telemetrycznych do centrum.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz subskrypcję [bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku Szybki start można uruchomić przy użyciu Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która działa w przeglądarce. Jeśli używasz Cloud Shell, nie musisz niczego instalować. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Jeśli używasz programu Azure Cloud Shell, nie aktualizuj zainstalowanej wersji programu Node.js. Ten Azure Cloud Shell ma już najnowszą Node.js wersję.

    Sprawdź bieżącą wersję aplikacji Node.js komputerze dewelopera przy użyciu następującego polecenia:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Wysyłanie komunikatów za pomocą Node.js SDK
W tej sekcji użyjemy zestawu SDK usługi Node.js do wysyłania komunikatów z urządzenia symulowanego do centrum IoT. 

1. Otwórz nowe okno terminalu. Użyjesz tego terminalu do zainstalowania zestawu SDK Node.js i pracy z Node.js przykładowym kodem. Powinny być teraz otwarte dwa terminale: ten, który został właśnie otwarty do pracy z usługą Node.js, oraz powłoka interfejsu wiersza polecenia użyta w poprzednich sekcjach do wprowadzania poleceń interfejsu wiersza polecenia platformy Azure.

1. Skopiuj [przykłady urządzeń zestawu SDK usługi Azure IoT Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) na maszynę lokalną:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Przejdź do katalogu *azure-iot-sdk-node/device/samples/pnp:*

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Zainstaluj zestaw SDK usługi Azure IoT Node.js i niezbędne zależności:

    ```console
    npm install
    ```

    To polecenie instaluje odpowiednie zależności określone w pliku *package.jsw* katalogu przykładów urządzeń.

1. Ustaw obie poniższe zmienne środowiskowe, aby umożliwić urządzeniu symulowanemu łączenie się z usługą Azure IoT.
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
    > W przypadku usługi CMD systemu Windows nie ma znaków cudzysłowu otaczających wartości ciągu dla każdej zmiennej.

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
1. W otwartej powłoki interfejsu wiersza polecenia uruchom polecenie [az iot hub monitor-events,](/cli/azure/iot/hub#az_iot_hub_monitor_events) aby rozpocząć monitorowanie zdarzeń na symulowanym urządzeniu IoT.  Komunikaty o zdarzeniach będą wyświetlane w terminalu po ich przybyciu.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. W terminalu Node.js uruchom kod zainstalowanego przykładowego *pliku,* simple_thermostat.js. Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do centrum IoT.

    Aby uruchomić przykład Node.js z terminalu:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Ten przykładowy kod używa usługi Azure IoT Plug and Play, która umożliwia integrowanie urządzeń inteligentnych z rozwiązaniami bez konieczności ręcznej konfiguracji.  Domyślnie większość przykładów w tej dokumentacji używa IoT Plug and Play. Aby dowiedzieć się więcej o zaletach usługi IoT PnP i przypadkach jej używania, zobacz Co to [jest IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Ponieważ kod Node.js wysyła symulowany komunikat telemetrii z urządzenia do centrum IoT Hub, komunikat jest wyświetlany w powłoki interfejsu wiersza polecenia, która monitoruje zdarzenia:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
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

W tym przewodniku Szybki start poznano podstawowy przepływ pracy aplikacji usługi Azure IoT, który umożliwia bezpieczne łączenie urządzenia z chmurą i wysyłanie danych telemetrycznych z urządzenia do chmury. Interfejs wiersza polecenia platformy Azure został użyty do utworzenia centrum IoT i symulowanego urządzenia, a następnie usłudze Azure IoT Node.js SDK w celu uzyskania dostępu do urządzenia i wysłania danych telemetrycznych do centrum. 

Następnym krokiem jest eksplorowanie zestawu SDK usługi Azure IoT Node.js za pomocą przykładów aplikacji.

- [Więcej Node.js przykładów:](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)ten katalog zawiera więcej przykładów z repozytorium zestawu SDK Node.js, które zawierają IoT Hub scenariuszy.