---
title: Samouczek — Tworzenie wystąpienia IoT Edge analizy wideo na platformie Azure IoT Central (Intel NUC)
description: W tym samouczku pokazano, jak utworzyć wystąpienie IoT Edge analizy wideo do użycia z szablonem aplikacji analiza filmów wideo — obiekt i ruch.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831930"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Samouczek: Tworzenie wystąpienia IoT Edge dla analiz wideo (Intel NUC)

Azure IoT Edge to w pełni zarządzana usługa, która umożliwia lokalne dostarczanie analiz chmurowych przez wdrożenie i uruchomienie:

* Logika niestandardowa
* Usługi platformy Azure
* Sztuczna inteligencja

W IoT Edge te usługi działają bezpośrednio na międzyplatformowych urządzeniach IoT, co pozwala bezpiecznie uruchamiać rozwiązanie IoT i w dużej skali w chmurze lub w trybie offline.

W tym samouczku pokazano, jak zainstalować i skonfigurować środowisko uruchomieniowe IoT Edge na urządzeniu Intel NUC.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Aktualizowanie i Konfigurowanie IoT Edge
> * Skonfiguruj bramę IoT Edge
> * Podłączanie lokalnego aparatu fotograficznego zgodnego z ONVIF do urządzenia Intel NUC

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem należy wykonać poprzednią [aplikację Tworzenie aplikacji wideo na żywo w usłudze azure IoT Central (Yolo v3)](./tutorial-video-analytics-create-app-yolo-v3.md) lub [utworzyć analizę wideo w samouczku usługi Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md).
* Urządzenie, takie jak Intel NUC, z systemem Linux, które umożliwia uruchamianie kontenerów platformy Docker i ma wystarczającą moc obliczeniową do uruchamiania analizy wideo.
* [Środowisko uruchomieniowe IoT Edge zainstalowane](../../iot-edge/how-to-install-iot-edge.md) i uruchomione na urządzeniu.
* Aby można było nawiązać połączenie z urządzeniem IoT Edge z komputera z systemem Windows, potrzebny jest [klient SSH](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) lub równoważne narzędzie.
* Potrzebna jest również subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć bezpłatnie na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Jeśli nie masz zainstalowanego środowiska uruchomieniowego IoT Edge na komputerze z procesorem Intel NUC, zobacz temat [Instalowanie środowiska wykonawczego Azure IoT Edge na podstawie instrukcji systemów Linux opartych na Debian](../../iot-edge/how-to-install-iot-edge.md) .

Aby zaktualizować środowisko uruchomieniowe IoT Edge:

1. Użyj narzędzia do nawiązywania połączenia z urządzeniem IoT Edge.

1. Uruchom następujące polecenia, aby zaktualizować i sprawdzić wersję środowiska uruchomieniowego IoT Edge. W momencie pisania wersja jest 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Użyj następujących poleceń, aby utworzyć foldery używane przez wdrożenie z niezbędnymi uprawnieniami:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Aby dodać *state.jsw* pliku konfiguracyjnym do folderu */data/storage* na urządzeniu IoT Edge:

1. Użyj edytora tekstów, aby otworzyć *state.jsw* pliku w folderze *LVA-Configuration* na komputerze lokalnym.

1. Zaktualizuj `system` `iotCentral > properties` symbole zastępcze i przy użyciu wartości ciągu, które opisują urządzenie bramy. Te wartości można wyświetlić później na pulpicie nawigacyjnym aplikacji IoT Central.

1. Zaktualizuj `iotCentral > appKeys` symbole zastępcze wartościami, które zostały zanotowane w pliku *scratchpad.txt* w poprzednim samouczku. Zapisz zmiany.

1. Użyj `scp` Narzędzia instrukcje w wierszu polecenia, aby skopiować *state.js* pliku, który właśnie edytowano do folderu */Data/Storage* na urządzeniu IoT Edge. Ten przykład używa `192.168.0.144` jako przykładowego adresu IP, zastąp go adresem IP urządzenia IoT Edge:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Skonfiguruj IoT Edge, aby zarejestrować aplikację IoT Central i nawiązać z nią połączenie:

1. Użyj narzędzia do nawiązywania połączenia z urządzeniem IoT Edge.

1. Użyj edytora tekstów, takiego jak `nano` , aby otworzyć plik IoT Edge config. YAML.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > Pliki YAML nie mogą używać tabulatorów do wcięcia, należy zamiast tego użyć dwóch spacji. Elementy najwyższego poziomu nie mogą zawierać odstępów wiodących.

1. Przewiń w dół do momentu wyświetlenia `# Manual provisioning configuration` . Dodaj komentarz do następnych trzech wierszy, jak pokazano w poniższym fragmencie kodu:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Przewiń w dół do momentu wyświetlenia `# DPS symmetric key provisioning configuration` . Usuń komentarz z następnych ośmiu wierszy, jak pokazano w poniższym fragmencie kodu:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Zamień na `{scope_id}` **zakres identyfikatorów** , które zostały zanotowane w pliku *scratchpad.txt* w poprzednim samouczku.

1. Zastąp `{registration_id}` *ciąg opcją Gateway-001*, czyli urządzeniem utworzonym w poprzednim samouczku.

1. Zamień na `{symmetric_key}` **klucz podstawowy** urządzenia **brama-001** zanotowano w pliku *scratchpad.txt* w poprzednim samouczku.

1. Uruchom następujące polecenie, aby ponownie uruchomić demona IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Aby sprawdzić stan modułów IoT Edge, uruchom następujące polecenie:

    ```bash
    iotedge list
    ```

    Dane wyjściowe polecenia poprzedniej przedstawiają pięć uruchomionych modułów. Możesz również wyświetlić stan uruchomionych modułów w aplikacji IoT Central.

    > [!TIP]
    > Możesz ponownie uruchomić to polecenie, aby sprawdzić stan. Może być konieczne poczekanie, aż wszystkie moduły zaczną działać.

Jeśli moduły IoT Edge nie są uruchamiane prawidłowo, zobacz [Rozwiązywanie problemów z urządzeniem IoT Edge](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Zbieranie strumienia RTSP z aparatu

Zidentyfikuj adresy URL strumieni RTSP dla kamer podłączonych do urządzenia IoT Edge, na przykład:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Spróbuj wyświetlić strumień aparatu na komputerze IoT Edge przy użyciu odtwarzacza multimedialnego, takiego jak VLC.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z aplikacją, możesz usunąć wszystkie utworzone zasoby w następujący sposób:

1. W aplikacji IoT Central przejdź do strony **aplikacji** w sekcji **Administracja** . Następnie wybierz pozycję **Usuń**.
1. W Azure Portal Usuń grupę zasobów **LVA-RG** .
1. Na maszynie lokalnej Zatrzymaj kontener platformy Docker **przeglądarki amp** .

## <a name="next-steps"></a>Następne kroki

Środowisko uruchomieniowe IoT Edge i moduły LVA są teraz wdrożone na urządzeniu bramy Intel NUC Gateway.

Aby zarządzać kamerami, postępuj zgodnie z następnym samouczkiem:

> [!div class="nextstepaction"]
> [Monitorowanie i zarządzanie aplikacją do wykrywania ruchu wideo i obiektów](./tutorial-video-analytics-manage.md)