---
title: Jak skompilować i wdrożyć usługę IoT Plug and Play Bridge | Microsoft Docs
description: Zidentyfikuj składniki mostka Plug and Play IoT. Dowiedz się, jak uruchamiać je na urządzeniach IoT, bramach i jako moduł IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4612e1236af5fbe47db9a3569e2f4da2378017e2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784901"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>Kompilowanie i wdrażanie mostka Plug and Play IoT

[Mostek Plug and Play IoT](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) umożliwia podłączenie istniejących urządzeń podłączonych do bramy do centrum IoT Hub. Za pomocą mostka można mapować interfejsy Plug and Play IoT na dołączone urządzenia. Interfejs Plug and Play IoT definiuje dane telemetryczne wysyłane przez urządzenie, właściwości zsynchronizowane między urządzeniem a chmurą oraz polecenia, na które odpowiada urządzenie. Możesz zainstalować i skonfigurować aplikację mostka "open source" w bramach systemu Windows lub Linux. Dodatkowo mostek można uruchomić jako moduł środowiska uruchomieniowego Azure IoT Edge.

W tym artykule szczegółowo wyjaśniono, jak:

- Konfigurowanie mostka.
- Jak kompilować i uruchamiać mostek w różnych środowiskach.

Aby zapoznać się z prostym przykładem, który pokazuje, jak korzystać z mostka, zobacz [jak podłączyć przykład usługi IoT Plug and Play Bridge, który działa w systemie Linux lub Windows, aby IoT Hub](howto-use-iot-pnp-bridge.md).

Wskazówki i przykłady w tym artykule założono podstawową wiedzę na temat [usługi Azure Digital bliźniaczych reprezentacji](../digital-twins/overview.md) i [IoT Plug and Play](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Ogólne wymagania wstępne

### <a name="supported-os-platforms"></a>Obsługiwane platformy systemu operacyjnego

Obsługiwane są następujące platformy i wersje systemu operacyjnego:

|Platforma  |Obsługiwane wersje  |
|---------|---------|
|Windows 10 |     Obsługiwane są wszystkie jednostki SKU systemu Windows. Na przykład: IoT Enterprise, Server, Desktop, IoT Core. *W przypadku funkcji monitorowania kondycji aparatu 20H1 zaleca się kompilację lub nowszą. Wszystkie inne funkcje są dostępne we wszystkich kompilacjach systemu Windows 10.*  |
|Linux     |Przetestowane i obsługiwane w systemie Ubuntu 18,04, funkcjonalność innych dystrybucji nie została przetestowana.         |
||

### <a name="hardware"></a>Sprzęt

- Dowolna platforma sprzętowa obsługująca powyższe jednostki SKU i wersje systemu operacyjnego.
- Urządzenia peryferyjne oraz czujniki USB, Bluetooth i Camera są obsługiwane natywnie. Mostek Plug and Play IoT można rozszerzyć w celu obsługi dowolnego niestandardowego urządzenia peryferyjnego lub czujnika.

### <a name="azure-iot-products-and-tools"></a>Produkty i narzędzia usługi Azure IoT

- **Azure IoT Hub** — w ramach subskrypcji platformy Azure potrzebujesz [usługi Azure IoT Hub](../iot-hub/index.yml) , aby podłączyć urządzenie do programu. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/). Jeśli nie masz Centrum IoT Hub, [postępuj zgodnie z tymi instrukcjami, aby je utworzyć](../iot-hub/iot-hub-create-using-cli.md). Obsługa Plug and Play IoT nie jest uwzględniona w centrach IoT warstwy Podstawowa.
- **Eksplorator IoT Azure** — aby móc korzystać z urządzenia Plug and Play IoT, możesz użyć narzędzia Azure IoT Explorer. [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego. Skonfiguruj narzędzie Azure IoT Explorer, aby połączyć się z Centrum IoT Hub, a następnie wyszukaj definicje modeli w folderze *pnpbridge\docs\schemas* w sklonowanym **mostku IoT-Plug-and-Play** .

## <a name="configure-a-bridge"></a>Konfigurowanie mostka

Istnieją dwa sposoby skonfigurowania mostka:

- Jeśli mostek działa natywnie na urządzeniu IoT lub w bramie, użyj pliku konfiguracji. W tym scenariuszu można użyć komputera z systemem Linux lub Windows.
- Jeśli mostek działa jako moduł IoT Edge w środowisku uruchomieniowym IoT Edge, użyj odpowiedniej właściwości sznurka modułu. W tym scenariuszu przyjęto założenie, że środowisko uruchomieniowe IoT Edge jest hostowane w środowisku systemu Linux.

### <a name="configuration-file"></a>Plik konfiguracji

Gdy Plug and Play Bridge działa natywnie na urządzeniu IoT lub w bramie, używa pliku konfiguracji w celu:

- Pobierz IoT Central lub IoT Hub informacje o połączeniu.
- Skonfiguruj urządzenia, dla których są publikowane interfejsy Plug and Play IoT.

Użyj jednej z następujących opcji, aby dostarczyć plik konfiguracyjny do mostka:

- Przekaż ścieżkę do pliku konfiguracji jako parametr wiersza polecenia do pliku wykonywalnego mostka.
- Użyj istniejącego *config.jsw* pliku w folderze *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* .

[Schemat pliku konfiguracji](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) jest dostępny w repozytorium GitHub.

> [!TIP]
> Jeśli edytujesz plik konfiguracyjny mostka w VS Code, możesz użyć tego pliku schematu do zweryfikowania pliku.

### <a name="iot-edge-module-configuration"></a>IoT Edge Konfiguracja modułu

Gdy mostek działa jako moduł IoT Edge w środowisku uruchomieniowym IoT Edge, plik konfiguracji jest wysyłany z chmury jako aktualizacja `PnpBridgeConfig` odpowiedniej właściwości. Mostek czeka na tę aktualizację właściwości przed skonfigurowaniem kart i składników.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Kompilowanie i uruchamianie mostka na urządzeniu IoT lub w bramie

| Platforma | Obsługiwane |
| :-----------: | :-----------: |
| Windows |  Tak |
| Linux | Tak |

### <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę sekcję, należy zainstalować następujące oprogramowanie na komputerze lokalnym:

- Środowisko programistyczne obsługujące kompilowanie języka C++ [, takiego jak Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas instalowania programu Visual Studio uwzględniono składnik Menedżera pakietów NuGet i **Programowanie aplikacji klasycznych w języku C++** .
- [CMAKE](https://cmake.org/download/) — po zainstalowaniu CMAKE wybierz opcję **Dodaj CMAKE do ścieżki systemowej**.
- W przypadku kompilowania w systemie Windows należy również pobrać [zestaw Windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Kod źródłowy

Sklonuj repozytorium programu [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) na komputerze lokalnym:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Oczekiwano, że poprzednie polecenie zajmie kilka minut.

> [!NOTE]
> Jeśli występują problemy z `git submodule update` niepowodzeniem w systemie Windows, spróbuj wykonać następujące polecenie, aby rozwiązać ten problem: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Tworzenie mostka w systemie Windows

Otwórz **wiersz polecenia dla deweloperów programu VS 2019** i przejdź do folderu zawierającego Sklonowane repozytorium i uruchom następujące polecenia:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Oczekiwano, że poprzednie polecenie zajmie kilka minut.

Opcjonalnie możesz otworzyć wygenerowany plik rozwiązania *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. sln* w programie Visual Studio, aby edytować, skompilować i debugować kod.

> [!TIP]
> Ten projekt używa CMAKE do wygenerowania plików projektu. Wszelkie modyfikacje wprowadzone w projekcie w programie Visual Studio mogą zostać utracone, jeśli odpowiednie pliki CMAKE nie zostaną zaktualizowane.

### <a name="build-the-bridge-on-linux"></a>Tworzenie mostka w systemie Linux

Przy użyciu powłoki bash przejdź do folderu zawierającego Sklonowane repozytorium i uruchom następujące polecenia:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Konfigurowanie czujników ogólnych

Zmodyfikuj następujące parametry w `pnp_bridge_connection_parameters` węźle *config.js* w pliku w folderze *IoT-Plug-and-Play-bridge\pnpbridge\cmake\ Pnpbridge_x86 \src\pnpbridge\samples\console* w systemie Windows lub folderze *IoT-Plug-and-Play-bridge/pnpbridge/CMAKE/pnpbridge_linux \ src/pnpbridge/Samples/Console* w systemie Windows:

Jeśli używasz parametrów połączenia do nawiązywania połączenia z Centrum IoT Hub:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> `symmetric_key`Wartość musi być zgodna z kluczem sygnatury dostępu współdzielonego w parametrach połączenia.

Jeśli używasz usługi DPS do nawiązywania połączenia z usługą IoT Hub lub IoT Central aplikacji:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Przejrzyj resztę pliku konfiguracji, aby zobaczyć, które składniki interfejsu i parametry globalne zostały skonfigurowane w tym przykładzie.

### <a name="start-the-bridge-in-windows"></a>Uruchamianie mostka w systemie Windows

Uruchom mostek, uruchamiając go w wierszu polecenia:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Aby użyć pliku konfiguracji z innej lokalizacji, należy przekazać jego ścieżkę jako parametr wiersza polecenia podczas uruchamiania mostka.
  
> [!TIP]
> Jeśli masz wbudowany aparat fotograficzny lub aparat USB podłączony do komputera, możesz uruchomić aplikację korzystającą z aparatu, taką jak wbudowana aplikacja **aparatu** . Gdy aplikacja **aparatu fotograficznego** jest uruchomiona, w danych wyjściowych konsoli programu Bridge zostanie wyświetlona Statystyka monitorowania i szybkość klatek jest raportowana za pomocą interfejsu Digital bliźniaczy do centrum IoT Hub.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Kompilowanie i uruchamianie mostka jako modułu IoT Edge

| Platforma | Obsługiwane |
| :-----------: | :-----------: |
| Windows |  Nie |
| Linux | Tak |

### <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę sekcję, potrzebujesz bezpłatnej lub standardowej warstwy usługi Azure IoT Hub. Aby oboszczędnić sposób tworzenia Centrum IoT Hub, zobacz [Tworzenie Centrum IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

W krokach w tej sekcji założono, że masz następujące środowisko programistyczne na komputerze z systemem Windows 10. Te narzędzia umożliwiają kompilowanie i wdrażanie modułu IoT Edge na urządzeniu IoT Edge:

- Podsystem Windows dla systemu Linux (WSL) 2 z uruchomioną Ubuntu 18,04 LTS. Aby dowiedzieć się więcej, zobacz [Przewodnik instalacji systemu Windows w systemie Linux dla systemu Windows 10](/windows/wsl/install-win10).
- Program Docker Desktop dla systemu Windows skonfigurowany do korzystania z WSL 2. Aby dowiedzieć się więcej, zobacz [Docker Desktop WSL 2 zaplecza](https://docs.docker.com/docker-for-windows/wsl/).
- [Visual Studio Code zainstalowane w środowisku systemu Windows](https://code.visualstudio.com/docs/setup/windows) z następującymi trzema zainstalowanymi rozszerzeniami:

  - [Pakiet rozszerzenia do zdalnego programowania](https://aka.ms/vscode-remote/download/extension) — to rozszerzenie umożliwia kompilowanie i uruchamianie kodu w WSL 2.
  - [Docker for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) — to rozszerzenie musi być włączone w środowisku vs Code **WSL: Ubuntu-18,04** .
  - [Narzędzia Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) — to rozszerzenie należy włączyć w środowisku vs Code **WSL: Ubuntu-18,04** .

- Aby zainstalować wymagane narzędzia do kompilacji, uruchom następujące polecenia w środowisku WSL 2:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) został zainstalowany w środowisku WSL 2 w celu zarządzania zasobami platformy Azure.

  > [!TIP]
  > Jeśli wolisz, możesz uruchomić `az` polecenia w [Azure Cloud Shell](https://shell.azure.com/) , na którym jest wstępnie zainstalowany interfejs wiersza polecenia.

### <a name="create-an-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Polecenia w tym miejscu tworzą IoT Edge urządzenia uruchomionego na maszynie wirtualnej platformy Azure. Uruchamianie urządzenia IoT Edge na maszynie wirtualnej jest przydatne do testowania wdrożenia, jeśli nie masz dostępu do rzeczywistego urządzenia.

Aby utworzyć IoT Edge rejestrację urządzenia w usłudze IoT Hub, uruchom następujące polecenia w środowisku WSL 2. Użyj `az login` polecenia, aby zalogować się do subskrypcji platformy Azure:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Aby utworzyć maszynę wirtualną platformy Azure z zainstalowanym środowiskiem uruchomieniowym IoT Edge, uruchom następujące polecenia. Aktualizowanie symboli zastępczych odpowiednimi wartościami:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Na maszynie wirtualnej jest teraz uruchomione środowisko uruchomieniowe IoT Edge. Aby sprawdzić, czy **$edgeAgent** i **$edgeHub** są uruchomione na urządzeniu, można użyć następującego polecenia:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Opłaty są naliczane, gdy ta maszyna wirtualna jest uruchomiona. Pamiętaj, aby zamknąć go, gdy nie jest używany, i usuń go po zakończeniu pracy z nim.

### <a name="download-the-source-code"></a>Pobierz kod źródłowy

W poniższych krokach utworzysz obraz platformy Docker w środowisku WSL 2. Aby sklonować repozytorium **IoT-Plug-and-Play-Bridge** , uruchom następujące polecenia w powłoce WSL 2 bash w odpowiednim folderze:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Oczekiwano, że poprzednie polecenie zajmie kilka minut.

### <a name="update-the-dockerfile"></a>Aktualizowanie *pliku dockerfile*

Uruchom VS Code, Otwórz paletę poleceń, wprowadź *Remote WSL: Open folder in WSL*, a następnie otwórz folder *IoT-Plug-and-Play-Bridge* zawierający Sklonowane repozytorium.

Otwórz plik *pnpbridge\Dockerfile.amd64* . Edytuj definicje zmiennych środowiskowych w następujący sposób:

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Aby pobrać parametry połączenia urządzenia, można użyć następującego polecenia: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Istnieją przykładowe *wieloetapowe dockerfile* dla innych architektur.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Tworzenie obrazu modułu mostka Plug and Play IoT

W VS Code Otwórz paletę poleceń, wprowadź *rejestry Docker: Zaloguj się do interfejsu wiersza polecenia platformy Docker*, a następnie wybierz swoją subskrypcję i rejestr kontenerów Azure. To polecenie umożliwia platformie Docker łączenie się z rejestrem kontenerów i przekazywanie obrazu modułu.

Otwórz plik *pnpbridge/module.jsw* pliku. Dodaj `{your container registry name}.azurecr.io/iotpnpbridge` jako repozytorium obrazów kontenerów i `v1` jako wersję.

W VS Code kliknij prawym przyciskiem myszy plik *pnpbridge/module.js* w widoku **Eksploratora** , wybierz pozycję **kompilacja i wypychanie obrazu modułu IoT Edge**, a następnie wybierz pozycję **amd64** jako platformę.

W VS Code w widoku **platformy Docker** można przeglądać zawartość rejestru kontenerów, która zawiera teraz obraz modułu **iotpnpbridge: V1-amd64** .

### <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Urządzenie IoT Edge pobiera jego obrazy modułów z rejestru kontenerów. W tym przykładzie jest wykorzystywana usługa Azure Container Registry.

Utwórz usługę Azure Container Registry w grupie zasobów **mostkowanie zasobów** . Następnie Włącz dostęp administratora do rejestru kontenerów i uzyskaj poświadczenia wymagane przez urządzenie IoT Edge, aby pobrać obrazy modułu:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Tworzenie manifestu wdrożenia

IoT Edge manifest wdrożenia określa moduły i wartości konfiguracji, które mają zostać wdrożone na urządzeniu IoT Edge.

W VS Code Otwórz plik *pnpbridge/deployment.template.jsw* pliku:

- Zaktualizuj `registryCredentials` program przy użyciu wartości z poprzedniego polecenia. Wartość będzie wyglądać następująco `address` `{your container registry name}.azurecr.io` .
- Zaktualizuj `image` wartość dla `ModulePnpBridge` . Obraz modułu wygląda następująco: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Zastąp `PnPBridgeConfig` następującym kodem JSON, aby skonfigurować adapter wykrywania CO2:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Zapisz zmiany.

W VS Code kliknij prawym przyciskiem myszy plik *pnpbridge/deployment.template.js* w widoku **Eksploratora** , wybierz polecenie **Generuj IoT Edge manifest wdrożenia**. To polecenie generuje *pnpbridge/config/deployment.amd64.jsw* manifeście wdrożenia.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Wdrażanie mostka na urządzeniu IoT Edge

W VS Code Otwórz paletę poleceń, wprowadź *Azure IoT Hub: wybierz pozycję IoT Hub*, a następnie wybierz swoją subskrypcję i usługę IoT Hub.

W VS Code kliknij prawym przyciskiem myszy plik *pnpbridge/config/deployment.amd64.js* w widoku **Eksploratora** , wybierz opcję **Utwórz wdrożenie dla jednego urządzenia** i wybierz **mostek-Urządzenie brzegowe**.

Aby wyświetlić stan modułów na urządzeniu, uruchom następujące polecenie:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

Lista uruchomionych modułów zawiera teraz moduł **ModulePnpBridge** , który jest skonfigurowany do korzystania z karty wykrywania CO2.

### <a name="tidy-up"></a>Uporządkowanego

Aby usunąć maszynę wirtualną i rejestr kontenerów z subskrypcji platformy Azure, uruchom następujące polecenie:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Struktura folderów

*pnpbridge\deps\azure-IoT-SDK-c-PnP*: moduły podrzędne usługi git zawierające zestaw SDK urządzeń Azure IoT dla języka c SDK.

*pnpbridge\scripts*: Kompiluj skrypty.

*pnpbridge\src*: kod źródłowy dla programu IoT Plug and Play Bridge Core.

*pnpbridge\src\adapters*: kod źródłowy dla różnych kart mostka Plug and Play IoT.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi IoT Plug and Play Bridge, odwiedź repozytorium usługi [iot Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) w witrynie GitHub.