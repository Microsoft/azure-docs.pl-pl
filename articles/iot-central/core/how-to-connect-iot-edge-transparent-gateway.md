---
title: Łączenie IoT Edge przezroczystej bramy z aplikacją IoT Central platformy Azure
description: Jak podłączyć urządzenia za IoT Edge nieprzezroczystej bramy do aplikacji IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bdfb5f65106f3f8843b4aa52b752f5e563ab03f0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620122"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Jak podłączyć urządzenia za poorednictwem niewidocznej bramy IoT Edge

Urządzenie IoT Edge może działać jako brama, która zapewnia połączenie między innymi urządzeniami w sieci lokalnej i aplikacji IoT Central. Możesz użyć bramy, gdy urządzenie nie może bezpośrednio uzyskać dostępu do aplikacji IoT Central.

IoT Edge obsługuje [wzorce bramy *transparentu* i *translacji*](../../iot-edge/iot-edge-as-gateway.md). W tym artykule opisano sposób implementacji przezroczystego wzorca bramy. W tym wzorcu Brama przekazuje komunikaty z urządzenia podrzędnego do punktu końcowego IoT Hub w aplikacji IoT Central.

Ten artykuł używa maszyn wirtualnych do hostowania urządzenia podrzędnego i bramy. W rzeczywistym scenariuszu urządzenie podrzędne i Brama byłyby uruchomione na urządzeniach fizycznych w sieci lokalnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > aplikacji niestandardowej** .

Aby wykonać kroki opisane w tym artykule, pobierz następujące pliki na komputer:

- [Model urządzenia termostatu](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Niejawny manifest bramy](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Dodawanie szablonów urządzeń

Urządzenia podrzędne i urządzenie bramy wymagają szablonów urządzeń w IoT Central. IoT Central umożliwia modelowanie relacji między urządzeniami podrzędnymi a bramą, dzięki czemu można je przeglądać i zarządzać nimi po połączeniu.

Aby utworzyć szablon urządzenia dla urządzenia podrzędnego, należy utworzyć standardowy szablon urządzenia, który modeluje możliwości urządzenia. W przykładzie przedstawionym w tym artykule jest stosowany model urządzenia z termostatem.

Aby utworzyć szablon urządzenia dla urządzenia podrzędnego:

1. Utwórz szablon urządzenia i wybierz pozycję **urządzenie IoT** jako typ szablonu.

1. Na stronie **Dostosowywanie** kreatora wprowadź nazwę, na przykład *termostat* dla szablonu urządzenia.

1. Po utworzeniu szablonu urządzenia wybierz pozycję **Importuj model**. Wybierz model, taki jak *thermostat-1.jsna* pobranym wcześniej pliku.

1. Aby wygenerować niektóre widoki domyślne dla termostatu, wybierz pozycję widoki, a następnie wybierz pozycję **Generuj widoki domyślne**.

1. Opublikuj szablon urządzenia.

Aby utworzyć szablon urządzenia dla przezroczystej bramy IoT Edge:

1. Utwórz szablon urządzenia i wybierz **Azure IoT Edge** jako typ szablonu.

1. Na stronie **Dostosowywanie** kreatora wprowadź nazwę, taką jak *graniczny Gateway* dla szablonu urządzenia.

1. Na stronie **Dostosowywanie** kreatora sprawdź **urządzenie bramy z urządzeniami podrzędnymi**.

1. Na stronie **Dostosowywanie** kreatora wybierz pozycję **Przeglądaj**. Przekaż *EdgeTransparentGatewayManifest.js* pobrany wcześniej plik.

1. Dodaj wpis w **relacjach** do szablonu urządzenia podrzędnego.

Poniższy zrzut ekranu przedstawia stronę **relacje** dla urządzenia bramy IoT Edge, które **ma urządzenia podrzędne korzystające z tego** szablonu:

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Zrzut ekranu przedstawiający relację szablonu urządzenia bramy IoT Edge z szablonem urządzenia podrzędnego z termostatem.":::

Poprzedni zrzut ekranu przedstawia szablon urządzenia bramy IoT Edge bez zdefiniowanych modułów. Niejawna Brama nie wymaga żadnych modułów, ponieważ środowisko uruchomieniowe IoT Edge przekazuje komunikaty z urządzeń podrzędnych do IoT Central. Jeśli Brama wymaga wysłania telemetrii, zsynchronizowania właściwości lub obsługi poleceń, można zdefiniować te możliwości w składniku domyślnym lub w module.

Dodaj wszystkie wymagane właściwości i widoki chmury przed opublikowaniem szablonu bramy i urządzenia podrzędnego.

## <a name="add-the-devices"></a>Dodawanie urządzeń

Po dodaniu urządzeń do aplikacji IoT Central można zdefiniować relację między urządzeniami podrzędnymi a nieprzezroczystą bramą.

Aby dodać urządzenia:

1. Przejdź do strony urządzenia w aplikacji IoT Central.

1. Dodaj wystąpienie przezroczystej bramy IoT Edge urządzenie. W tym artykule identyfikator urządzenia bramy to `edgegateway` .

1. Dodaj co najmniej jedno wystąpienie urządzenia podrzędnego. W tym artykule urządzenia podrzędne są termostatami z identyfikatorami `thermostat1` i `thermostat2` .

1. Z listy urządzeń wybierz każde urządzenie podrzędne i wybierz pozycję **Dołącz do bramy**.

Poniższy zrzut ekranu przedstawia listę urządzeń podłączonych do bramy na stronie **urządzenia podrzędne** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Zrzut ekranu przedstawiający listę urządzeń podrzędnych podłączonych do przezroczystej bramy.":::

W nieprzezroczystej bramie urządzenia podrzędne nawiązują połączenie z bramą, a nie z modułem niestandardowym hostowanym przez bramę.

Przed wdrożeniem urządzeń należy wykonać następujące:

- **Zakres identyfikatorów** aplikacji IoT Central.
- Wartości **identyfikatora urządzenia** dla bramy i urządzeń podrzędnych.
- Wartości **kluczy podstawowych** dla bramy i urządzeń podrzędnych.

Aby znaleźć te wartości, przejdź do każdego urządzenia na liście urządzeń i wybierz pozycję **Połącz**. Zanotuj te wartości przed kontynuowaniem.

## <a name="deploy-the-gateway-and-devices"></a>Wdrażanie bramy i urządzeń

Aby umożliwić wypróbowanie tego scenariusza, w poniższych krokach pokazano, jak wdrożyć bramę i urządzenia podrzędne na maszynach wirtualnych platformy Azure. W rzeczywistym scenariuszu urządzenie podrzędne i Brama działają na urządzeniach fizycznych w sieci lokalnej.

Aby wypróbować przejrzysty scenariusz bramy, wybierz poniższy przycisk, aby wdrożyć dwie maszyny wirtualne z systemem Linux. Jedna maszyna wirtualna jest przezroczystą bramą IoT Edge, druga jest urządzeniem podrzędnym, które symuluje termostat:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Po wdrożeniu i uruchomieniu dwóch maszyn wirtualnych Sprawdź, czy na maszynie wirtualnej jest uruchomione urządzenie IoT Edge Gateway `edgegateway` :

1. Przejdź do strony **urządzenia** w aplikacji IoT Central. Jeśli urządzenie bramy IoT Edge jest połączone z IoT Central, jego stan jest **zainicjowany**.

1. Otwórz urządzenie bramy IoT Edge i sprawdź stan modułów na stronie **moduły** . Jeśli środowisko uruchomieniowe IoT Edge zostało uruchomione pomyślnie, stan modułów **$edgeAgent** i **$edgeHub** jest **uruchomiony**:

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Zrzut ekranu przedstawiający moduły $edgeAgent i $edgeHub uruchomione na bramie IoT Edge.":::

> [!TIP]
> Może być konieczne poczekanie przez kilka minut podczas uruchamiania maszyny wirtualnej i zainicjowanie obsługi urządzenia w aplikacji IoT Central.

## <a name="configure-the-gateway"></a>Konfigurowanie bramy

Aby urządzenie IoT Edge działało jako przezroczysta brama, potrzebuje pewnych certyfikatów, aby potwierdzić swoją tożsamość na wszystkich urządzeniach podrzędnych. W tym artykule są stosowane certyfikaty demonstracyjne. W środowisku produkcyjnym Użyj certyfikatów z urzędu certyfikacji.

Aby wygenerować certyfikaty demonstracyjne i zainstalować je na urządzeniu bramy:

1. Za pomocą protokołu SSH Połącz się z maszyną wirtualną urządzenia bramy i zaloguj się.

1. Uruchom następujące polecenia, aby sklonować repozytorium IoT Edge i generować certyfikaty demonstracyjne:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Po uruchomieniu poprzednich poleceń następujące pliki są gotowe do użycia w następnych krokach:

    - *~/certs/certs/Azure-IoT-test-Only.root.ca.CERT.pem* — certyfikat głównego urzędu certyfikacji używany do udostępniania wszystkich innych certyfikatów demonstracyjnych na potrzeby testowania scenariusza IoT Edge.
    - *~/certs/certs/IoT-Edge-Device-mycacert-Full-Chain.CERT.pem* — certyfikat urzędu certyfikacji urządzenia, do którego odwołuje się plik *config. YAML* . W scenariuszu bramy ten certyfikat urzędu certyfikacji polega na tym, jak urządzenie IoT Edge weryfikuje jego tożsamość na urządzeniach podrzędnych.
    - *~/certs/Private/IoT-Edge-Device-mycacert.Key.pem* — klucz prywatny skojarzony z certyfikatem urzędu certyfikacji urządzenia.

    Aby dowiedzieć się więcej o tych certyfikatach demonstracyjnych, zobacz [Create demonstracyjne Certificates to test IoT Edge Features](../../iot-edge/how-to-create-test-certificates.md).

1. Otwórz plik *config. YAML* w edytorze tekstów. Na przykład:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Znajdź `Certificate settings` Ustawienia. Usuń komentarz i zmodyfikuj ustawienia certyfikatu w następujący sposób:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    W powyższym przykładzie przyjęto założenie, że użytkownik jest zalogowany jako **AzureUser** i utworzył certyfikat urzędu certyfikacji urządzenia o nazwie "mycacert".

1. Zapisz zmiany i ponownie uruchom środowisko uruchomieniowe IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

Jeśli środowisko uruchomieniowe IoT Edge zostanie uruchomione pomyślnie po wprowadzeniu zmian, stan **$edgeAgent** i **$edgeHub** modułów zostanie zmieniony na **na stronie** **moduły** dla urządzenia bramy w IoT Central.

Jeśli środowisko uruchomieniowe nie zostanie uruchomione, Sprawdź zmiany wprowadzone w *pliku config. YAML* i zobacz [Rozwiązywanie problemów z urządzeniem IoT Edge](../../iot-edge/troubleshoot.md).

Twoja przezroczysta Brama jest teraz skonfigurowana i gotowa do uruchamiania przekazywania danych telemetrycznych z urządzeń podrzędnych.

## <a name="provision-a-downstream-device"></a>Udostępnianie urządzenia podrzędnego

Obecnie IoT Edge nie może automatycznie zainicjować obsługi administracyjnej urządzenia podrzędnego w aplikacji IoT Central. W poniższych krokach pokazano, jak udostępnić `thermostat1` urządzenie. Aby wykonać te kroki, należy zainstalować środowisko z zainstalowanym językiem Python 3,5 (lub nowszym) i połączeniem z Internetem. [Azure Cloud Shell](https://shell.azure.com/) ma wstępnie zainstalowany język Python 3,5:

1. Uruchom następujące polecenie, aby zainstalować `azure.iot.device` moduł:

    ```bash
    pip install azure.iot.device
    ```

1. Uruchom następujące polecenie, aby pobrać skrypt języka Python, który wykonuje aprowizacji:

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Aby udostępnić `thermostat1` urządzenie podrzędne w aplikacji IoT Central, uruchom następujące polecenia, zastępując `{your application id scope}` i `{your device primary key}`  :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

W aplikacji IoT Central upewnij się, że **stan urządzenia** dla urządzenia thermostat1 jest teraz **zainicjowany**. 

## <a name="configure-a-downstream-device"></a>Konfigurowanie urządzenia podrzędnego

W poprzedniej sekcji została skonfigurowana `edgegateway` maszyna wirtualna z certyfikatami demonstracyjnymi, aby umożliwić jej uruchamianie jako bramy. `leafdevice`Maszyna wirtualna jest gotowa do zainstalowania symulatora termostatu korzystającego z bramy w celu nawiązania połączenia z IoT Central.

`leafdevice`Maszyna wirtualna wymaga kopii certyfikatu głównego urzędu certyfikacji utworzonego na `edgegateway` maszynie wirtualnej. Skopiuj plik */Home/AzureUser/certs/certs/Azure-IoT-test-Only.root.ca.CERT.pem* z `edgegateway` maszyny wirtualnej do katalogu macierzystego na `leafdevice` maszynie wirtualnej. Aby skopiować pliki do i z maszyny wirtualnej z systemem Linux, można użyć polecenia **SCP** .

Aby dowiedzieć się, jak sprawdzić połączenie z urządzenia podrzędnego z bramą, zobacz [testowanie połączenia bramy](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Aby uruchomić symulator termostatu na `leafdevice` maszynie wirtualnej:

1. Pobierz przykład języka Python do katalogu macierzystego:

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Zainstaluj moduł Python urządzenia Azure IoT:

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Ustaw zmienne środowiskowe w celu skonfigurowania przykładu. Zamień na `{your device shared key}` klucz podstawowy `thermostat1` zanotowany wcześniej. Te zmienne zakładają, że nazwa maszyny wirtualnej bramy to `edgegateway` i identyfikator urządzenia termostatu `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Zwróć uwagę, jak parametry połączenia wykorzystują nazwę urządzenia bramy, a nie nazwę Centrum IoT.

1. Aby uruchomić kod, użyj następującego polecenia:

    ```bash
    python3 simple_thermostat.py
    ```

    Dane wyjściowe tego polecenia wyglądają następująco:

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Aby wyświetlić dane telemetryczne w IoT Central, przejdź do strony **Przegląd** dla urządzenia **thermostat1** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Zrzut ekranu przedstawiający dane telemetryczne z urządzenia podrzędnego.":::

    Na stronie **informacje** można wyświetlić wartości właściwości wysyłane z urządzenia podrzędnego, a na stronie **polecenia** można wywołać polecenia na urządzeniu podrzędnym.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak skonfigurować przezroczystą bramę z IoT Central, sugerowanym następnym krokiem jest dowiedzenie więcej o [IoT Edge](../../iot-edge/about-iot-edge.md).
