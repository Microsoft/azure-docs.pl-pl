---
title: Wysyłanie danych telemetrycznych urządzenia do usługi Azure IoT Hub szybki start (Node.js)
description: W tym przewodniku szybki start użyjesz zestawu SDK urządzeń IoT Hub Azure dla Node.js do wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 047700be674dfab997b5c87f7446c19fdea9e0eb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605964"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub (Node.js)

**Dotyczy**: [opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku szybki start nauczysz się podstawowy przepływ pracy tworzenia aplikacji dla urządzeń IoT. Za pomocą interfejsu wiersza polecenia platformy Azure można utworzyć centrum Azure IoT Hub i symulowane urządzenie, a następnie za pomocą zestawu SDK usługi Azure IoT Node.js uzyskać dostęp do urządzenia i wysyłać dane telemetryczne do centrum.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku szybki start można uruchomić za pomocą Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która jest uruchamiana w przeglądarce. W przypadku korzystania z Cloud Shell nie trzeba instalować żadnych elementów. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). W przypadku korzystania z Azure Cloud Shell nie należy aktualizować zainstalowanej wersji programu Node.js. Azure Cloud Shell ma już najnowszą wersję Node.js.

    Sprawdź bieżącą wersję Node.js na komputerze deweloperskim, używając następującego polecenia:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Używanie zestawu SDK Node.js do wysyłania komunikatów
W tej sekcji użyjesz zestawu SDK Node.js do wysyłania komunikatów z symulowanego urządzenia do centrum IoT Hub. 

1. Otwórz nowe okno terminalu. Ten terminal będzie używany do instalowania zestawu SDK Node.js i pracy z Node.js przykładowym kodem. Powinny teraz być otwarte dwa terminale: ten właśnie otwarty do pracy z Node.js i powłoką interfejsu wiersza polecenia, która została użyta w poprzednich sekcjach, do wprowadzania poleceń interfejsu wiersza polecenia platformy Azure.

1. Skopiuj [przykłady urządzeń z zestawem SDK usługi Azure IoT Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) na komputer lokalny:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Przejdź do katalogu *Azure-IoT-SDK-Node/Device/Samples/PnP* :

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Zainstaluj zestaw SDK usługi Azure IoT Node.js i wymagane zależności:

    ```console
    npm install
    ```

    To polecenie powoduje zainstalowanie odpowiednich zależności określonych w *package.js* w pliku w katalogu przykłady urządzeń.

1. Ustaw obie następujące zmienne środowiskowe, aby umożliwić symulowanemu urządzeniu łączenie się z usługą Azure IoT.
    * Ustaw zmienną środowiskową o nazwie `IOTHUB_DEVICE_CONNECTION_STRING` . W polu wartość zmiennej Użyj parametrów połączenia urządzenia, które zostały zapisane w poprzedniej sekcji.
    * Ustaw zmienną środowiskową o nazwie `IOTHUB_DEVICE_SECURITY_TYPE` . Dla zmiennej Użyj wartości ciągu literału `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Dla polecenia Windows CMD nie ma cudzysłowu otaczającego wartości ciągu dla każdej zmiennej.

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
1. W otwartej powłoki interfejsu wiersza polecenia Uruchom polecenie [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) , aby rozpocząć monitorowanie zdarzeń na symulowanym urządzeniu IoT.  Komunikaty o zdarzeniach będą drukowane w terminalu po ich nadejściu.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. W terminalu Node.js Uruchom kod dla zainstalowanego przykładowego pliku *simple_thermostat.js* . Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do centrum IoT Hub.

    Aby uruchomić przykład Node.js z terminalu:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Ten przykładowy kod używa usługi Azure IoT Plug and Play, która umożliwia integrowanie urządzeń inteligentnych z rozwiązaniami bez konieczności ręcznej konfiguracji.  Domyślnie większość przykładów w tej dokumentacji używa Plug and Play IoT. Aby dowiedzieć się więcej o zaletach usługi IoT PnP i przypadkach korzystania z niej lub ich używać, zobacz artykuł [co to jest IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Ponieważ kod Node.js wysyła symulowany komunikat telemetrii z urządzenia do usługi IoT Hub, komunikat pojawi się w powłoce interfejsu wiersza polecenia, które jest monitorowane zdarzenia:

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

Urządzenie jest teraz bezpiecznie połączone i wysyła dane telemetryczne do usługi Azure IoT Hub.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start nie są już potrzebne, można je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom polecenie [AZ Group Delete](/cli/azure/group#az-group-delete) . To polecenie usuwa grupę zasobów, IoT Hub i utworzoną rejestrację urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [AZ Group list](/cli/azure/group#az-group-list) , aby potwierdzić, że grupa zasobów została usunięta.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono podstawowy przepływ pracy aplikacji Azure IoT służący do bezpiecznego łączenia urządzenia z chmurą i wysyłania danych telemetrycznych z urządzenia do chmury. Interfejs wiersza polecenia platformy Azure został użyty do utworzenia Centrum IoT i symulowanego urządzenia, a następnie do uzyskania dostępu do urządzenia i wysłania telemetrii do centrum użyto zestawu SDK usługi Azure IoT Node.js. 

Następnym krokiem jest zapoznanie się z zestawem SDK usługi Azure IoT Node.js za pomocą przykładów aplikacji.

- [Więcej przykładów Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): ten katalog zawiera więcej przykładów z repozytorium zestawu SDK Node.js w celu pokazania IoT Hub scenariuszy.