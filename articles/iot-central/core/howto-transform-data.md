---
title: Przekształcanie danych na Azure IoT Central | Microsoft Docs
description: Urządzenia IoT wysyłają dane w różnych formatach, które mogą wymagać przekształcenia. W tym artykule opisano sposób przekształcania danych zarówno w IoT Central, jak i w drodze. W opisanych scenariuszach są IoT Edge i Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751692"
---
# <a name="transform-data-for-iot-central"></a>Przekształcanie danych na IoT Central

*Ten temat dotyczy konstruktorów rozwiązań.*

Urządzenia IoT wysyłają dane w różnych formatach. Aby użyć danych urządzenia z aplikacją IoT Central, może być konieczne użycie przekształcenia w celu:

- Upewnij się, że format danych jest zgodny z IoT Central aplikacji.
- Konwertowanie jednostek.
- Oblicza nowe metryki.
- Wzbogacanie danych z innych źródeł.

W tym artykule pokazano, jak przekształcić dane urządzenia poza obszarem IoT Central w przypadku danych przychodzących lub wychodzących.

Na poniższym diagramie przedstawiono trzy trasy dla danych, które obejmują przekształcenia:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Podsumowanie tras przekształcania danych przychodzących i wychodzących" border="false":::

W poniższej tabeli przedstawiono trzy przykładowe typy przekształceń:

| Transformacja | Opis | Przykład  | Uwagi |
|------------------------|-------------|----------|-------|
| Format komunikatu         | Konwertowanie komunikatów JSON lub manipulowanie nimi. | Csv do JSON  | W przypadku danych przychodzących. IoT Central akceptuje tylko komunikaty JSON o wartości. Aby dowiedzieć się więcej, zobacz [Telemetria, właściwości i ładunki poleceń](concepts-telemetry-properties-commands.md). |
| Obliczeń           | Funkcje matematyczne, [Azure Functions](../../azure-functions/index.yml) wykonywać. | Konwersja jednostek z Fahrenheita na stopnie Celsjusza.  | Przekształć przy użyciu wzorca wychodzącego, aby korzystać ze skalowalnego przychodzących urządzeń za pośrednictwem bezpośredniego połączenia IoT Central. Przekształcanie danych umożliwia korzystanie z IoT Central takich jak wizualizacje i zadania. |
| Wzbogacanie komunikatów     | Wzbogacenia z zewnętrznych źródeł danych nie znaleziono we właściwościach urządzenia ani telemetrii. Aby dowiedzieć się więcej na temat wewnętrznych wzbogaceń, zobacz [Eksportowanie danych IoT do](howto-export-data.md) miejsc docelowych w chmurze przy użyciu eksportu danych | Dodawanie informacji o pogodzie do komunikatów przy użyciu danych lokalizacji z urządzeń. | Przekształć przy użyciu wzorca wychodzącego, aby korzystać ze skalowalnego przychodzących urządzeń za pośrednictwem bezpośredniego połączenia IoT Central. |

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebujesz aktywnej subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby skonfigurować rozwiązanie, potrzebujesz aplikacji IoT Central aplikacji. Aby dowiedzieć się, jak utworzyć aplikację IoT Central, zobacz [Tworzenie Azure IoT Central aplikacji.](quick-deploy-iot-central.md)

## <a name="data-transformation-at-ingress"></a>Przekształcanie danych przy wejechu

Istnieją dwie opcje przekształcania danych urządzenia w przypadku danych przychodzących:

- **IoT Edge:** użyj modułu IoT Edge, aby przekształcić dane z urządzeń dalszych przed wysłaniem danych do IoT Central aplikacji.

- **IoT Central** urządzenia: mostek [](howto-build-iotc-device-bridge.md) urządzenia IoT Central łączy inne chmury urządzeń IoT, takie jak Sigfox, Particle i The Things Network, z IoT Central. Mostek urządzenia używa funkcji platformy Azure do przekazywania danych i można dostosować funkcję w celu przekształcania danych urządzenia.

### <a name="use-iot-edge-to-transform-device-data"></a>Przekształcanie danych IoT Edge przy użyciu IoT Edge

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Przekształcanie danych w przypadku danych przychodzących przy użyciu IoT Edge" border="false":::

W tym scenariuszu moduł IoT Edge przekształca dane z urządzeń dalszych przed ich przekazywaniem do IoT Central aplikacji. Na wysokim poziomie kroki konfigurowania tego scenariusza są następujące:

1. **Skonfiguruj urządzenie IoT Edge:** zainstaluj i aprowizuj urządzenie IoT Edge jako bramę i połącz bramę z aplikacją IoT Central aplikacji.

1. **Podłącz urządzenie podrzędne do IoT Edge:** Podłącz urządzenia podrzędne do IoT Edge i aprowizuj je w IoT Central aplikacji.

1. **Przekształcanie danych urządzenia w IoT Edge:** Utwórz moduł IoT Edge, aby przekształcić dane. Wdrożenie modułu na urządzeniu bramy IoT Edge, które przekazuje przekształcone dane urządzenia do IoT Central aplikacji.

1. **Sprawdź:** Wyślij dane z urządzenia podrzędnego do bramy i sprawdź, czy przekształcone dane urządzenia docierają do IoT Central aplikacji.

W przykładzie opisanym w poniższych sekcjach urządzenie podrzędne wysyła dane CSV w następującym formacie do IoT Edge bramy:

```csv
"<temperature >, <pressure>, <humidity>"
```

Chcesz użyć modułu IoT Edge, aby przekształcić dane do następującego formatu JSON przed ich wysłaniem do IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

Poniższe kroki pokazują, jak skonfigurować i skonfigurować ten scenariusz:

### <a name="build-the-custom-module"></a>Tworzenie modułu niestandardowego

W tym scenariuszu urządzenie IoT Edge moduł niestandardowy, który przekształca dane z urządzenia podrzędnego. Przed wdrożeniem i skonfigurowaniem IoT Edge urządzenia należy:

- Skompilowanie modułu niestandardowego.
- Dodaj moduł niestandardowy do rejestru kontenerów.

Środowisko IoT Edge pobiera moduły niestandardowe z rejestru kontenerów, takiego jak rejestr kontenerów platformy Azure lub Docker Hub. Moduł [Azure Cloud Shell](../../cloud-shell/overview.md) wszystkie narzędzia potrzebne do utworzenia rejestru kontenerów, skompilowania modułu i przekazania modułu do rejestru:

Aby utworzyć rejestr kontenerów:

1. Otwórz [Azure Cloud Shell](https://shell.azure.com/) i zaloguj się do subskrypcji platformy Azure.

1. Uruchom następujące polecenia, aby utworzyć rejestr kontenerów platformy Azure:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Zanotuj `username` wartości i `password` . Użyj ich później.

Aby skompilować moduł niestandardowy w [Azure Cloud Shell](https://shell.azure.com/):

1. W [Azure Cloud Shell](https://shell.azure.com/)przejdź do odpowiedniego folderu.
1. Aby sklonować repozytorium GitHub zawierające kod źródłowy modułu, uruchom następujące polecenie:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Aby skompilować moduł niestandardowy, uruchom następujące polecenia w Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Uruchomienie poprzednich poleceń może potrwać kilka minut.

### <a name="set-up-an-iot-edge-device"></a>Konfigurowanie IoT Edge urządzenia

W tym scenariuszu IoT Edge do przekształcania danych z dowolnego urządzenia podrzędnego. W tej sekcji opisano sposób tworzenia IoT Central dla bramy i urządzeń niestandardowych w IoT Central aplikacji. IoT Edge urządzenia używają manifestu wdrożenia do konfigurowania swoich modułów.

Aby utworzyć szablon urządzenia dla urządzenia podrzędnego, w tym scenariuszu jest używany prosty model urządzenia termostatowego:

1. Pobierz model [urządzenia termostatu](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) na komputer lokalny.

1. Zaloguj się do aplikacji IoT Central i przejdź do **strony Szablony** urządzeń.

1. Wybierz **pozycję + Nowe,** wybierz **pozycję Urządzenie IoT** i wybierz pozycję **Dalej: Dostosuj.**

1. Wprowadź *termostat* jako nazwę szablonu i wybierz pozycję **Dalej: Przegląd**. Następnie wybierz pozycję **Utwórz**.

1. Wybierz **pozycję Importuj model** i *zaimportujthermostat-2.jswcześniej* pobranego pliku.

1. Wybierz **pozycję Publikuj,** aby opublikować nowy szablon urządzenia.

Aby utworzyć szablon urządzenia dla IoT Edge bramy:

1. Zapisz kopię manifestu wdrożenia na lokalnym komputerze dewelopera:moduledeployment.js[ na stronie](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Otwórz lokalną kopię pliku *moduledeployment.jspliku* manifestu w edytorze tekstów.

1. Znajdź sekcję i zastąp symbole zastępcze wartościami zanotymi przez Ciebie podczas tworzenia `registryCredentials` rejestru kontenerów platformy Azure. Wartość `address` wygląda `<username>.azurecr.io` następująco: .

1. Znajdź `settings` sekcję dla . `transformmodule` Zastąp `<acr or docker repo>` wartość taką `address` samą wartością, jaka jest używana w poprzednim kroku. Zapisz zmiany.

1. W aplikacji IoT Central przejdź do **strony Szablony** urządzeń.

1. Wybierz **pozycję + Nowy,** **wybierz pozycję Azure IoT Edge**, a następnie wybierz pozycję **Dalej: Dostosuj.**

1. Wprowadź *IoT Edge bramy* jako nazwę szablonu urządzenia. Wybierz **pozycję To jest urządzenie bramy.** Wybierz **pozycję Przeglądaj,** aby *przekazaćmoduledeployment.jspliku* manifestu wdrożenia, który był edytowany wcześniej.

1. Po weryfikacji manifestu wdrożenia wybierz pozycję **Dalej: Przegląd**, a następnie wybierz pozycję **Utwórz**.

1. W **obszarze Model** wybierz pozycję **Relacje**. Wybierz **pozycję + Dodaj relację.** Wprowadź *nazwę wyświetlaną Urządzenie* podrzędne, a następnie wybierz pozycję **Termostat** jako element docelowy. Wybierz pozycję **Zapisz**.

1. Wybierz **pozycję Publikuj,** aby opublikować szablon urządzenia.

Masz teraz dwa szablony urządzeń w IoT Central aplikacji. Szablon **IoT Edge urządzenia bramy** i szablon **termostatu** jako urządzenie podrzędne.

Aby zarejestrować urządzenie bramy w IoT Central:

1. W aplikacji IoT Central przejdź do **strony** Urządzenia.

1. Wybierz **IoT Edge bramy i** wybierz pozycję Utwórz **urządzenie.** Wprowadź *IoT Edge urządzenia bramy* jako nazwę urządzenia, wprowadź *gateway-01* jako identyfikator urządzenia, a następnie upewnij się, IoT Edge **jako** szablon urządzenia wybrano urządzenie bramy. Wybierz przycisk **Utwórz**.

1. Na liście urządzeń kliknij urządzenie bramy **IoT Edge** a następnie wybierz pozycję **Połącz.**

1. Zanotuj wartości **zakres identyfikatorów,** **identyfikator urządzenia** i klucz podstawowy dla **IoT Edge bramy.**  Użyj ich później.

Aby zarejestrować urządzenie podrzędne w IoT Central:

1. W aplikacji IoT Central przejdź do **strony** Urządzenia.

1. Wybierz **pozycję Termostat** i wybierz pozycję Utwórz **urządzenie.** Wprowadź *Termostat* jako nazwę urządzenia, wprowadź *wartość downstream-01* jako identyfikator urządzenia i upewnij się, że jako szablon urządzenia wybrano termostat.  Wybierz przycisk **Utwórz**.

1. Na liście urządzeń wybierz pozycję **Termostat,** a następnie wybierz **pozycję Dołącz do bramy**. Wybierz szablon **IoT Edge urządzenia bramy** sieciowej i IoT Edge urządzenia **bramy.** Wybierz pozycję **Dołącz**.

1. Na liście urządzeń kliknij pozycję **Termostat,** a następnie wybierz pozycję **Połącz**.

1. Zanotuj wartości **Zakres identyfikatorów,** **Identyfikator urządzenia** i **Klucz** podstawowy dla **urządzenia Termostat.** Użyj ich później.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Wdrażanie bramy i urządzeń dalszych

Dla wygody w tym artykule maszyny wirtualne platformy Azure są używane do uruchamiania bramy i urządzeń dalszych. Aby utworzyć dwie maszyny wirtualne platformy Azure, wybierz przycisk Deploy to Azure (Wdoń na platformie **Azure)** poniżej i skorzystaj z informacji w poniższej tabeli, aby ukończyć **formularz wdrożenia niestandardowego:**

| Pole | Wartość |
| ----- | ----- |
| Grupa zasobów | `ingress-scenario` |
| Brama prefiksów etykiet DNS | Unikatowa nazwa DNS dla tego komputera, taka jak `<your name>edgegateway` |
| Prefiks etykiety DNS — podrzędne | Unikatowa nazwa DNS dla tej maszyny, taka jak `<your name>downstream` |
| Identyfikator zakresu | Zanotowyny wcześniej zakres identyfikatorów |
| Identyfikator urządzenia IoT Edge Gateway | `gateway-01` |
| Brama kluczy IoT Edge urządzenia | Wartość klucza podstawowego zanotowana wcześniej |
| Typ uwierzytelniania | Hasło |
| Hasło lub klucz administratora | Wybrane hasło dla konta **AzureUser** na obu maszynach wirtualnych. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Wybierz **pozycję Przejrzyj i utwórz,** a następnie pozycję **Utwórz.** Utworzenie maszyn wirtualnych w grupie zasobów w scenariuszu przychodzącym może potrwać **kilka** minut.

Aby sprawdzić, czy IoT Edge działa prawidłowo:

1. Otwórz aplikację IoT Central aplikacji. Następnie przejdź do **IoT Edge Bramy** aplikacji na liście urządzeń na **stronie** Urządzenia.

1. Wybierz **kartę Moduły** i sprawdź stan trzech modułów. Uruchomienie środowiska uruchomieniowego IoT Edge na maszynie wirtualnej trwa kilka minut. Po uruchomieniu stan trzech modułów to **Uruchomione.** Jeśli nie IoT Edge uruchomieniowe, zobacz Troubleshoot your IoT Edge device (Rozwiązywanie problemów [z IoT Edge uruchomieniowym).](../../iot-edge/troubleshoot.md)

Aby urządzenie IoT Edge działało jako brama, musi mieć pewne certyfikaty, aby potwierdzić swoją tożsamość na wszystkich urządzeniach dalszych. W tym artykule są używane certyfikaty demonstracyjne. W środowisku produkcyjnym użyj certyfikatów z urzędu certyfikacji.

Aby wygenerować certyfikaty demonstracyjne i zainstalować je na urządzeniu bramy:

1. Użyj połączenia SSH, aby nawiązać połączenie i zalogować się na maszynie wirtualnej urządzenia bramy. Nazwę DNS tej maszyny wirtualnej można znaleźć w Azure Portal. Przejdź do maszyny **wirtualnej edgegateway** w **grupie zasobów ingress-scenario.**

    > [!TIP]
    > Może być konieczne otwarcie portu 22 na potrzeby dostępu za pomocą połączenia SSH na obu maszynach wirtualnych, zanim będzie można nawiązać połączenie z komputera lokalnego lub Azure Cloud Shell.

1. Uruchom następujące polecenia, aby sklonować repozytorium IoT Edge i wygenerować certyfikaty demonstracyjne:

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

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* — certyfikat głównego urzędu certyfikacji używany do testowania wszystkich innych certyfikatów IoT Edge testowego.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* — certyfikat urzędu certyfikacji urządzenia, do których odwołuje się plik *config.yaml.* W scenariuszu bramy ten certyfikat urzędu certyfikacji to sposób, w jaki urządzenie IoT Edge weryfikuje swoją tożsamość na urządzeniach dalszych.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* — klucz prywatny skojarzony z certyfikatem urzędu certyfikacji urządzenia.

    Aby dowiedzieć się więcej na temat tych certyfikatów demonstracyjnych, zobacz Create demo certificates to test IoT Edge device features (Tworzenie certyfikatów demonstracyjnych [w celu przetestowania IoT Edge urządzeń).](../../iot-edge/how-to-create-test-certificates.md)

1. Otwórz plik *config.yaml* w edytorze tekstów. Na przykład:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Znajdź `Certificate settings` ustawienia. Odłącz i zmodyfikuj ustawienia certyfikatu w następujący sposób:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    W powyższym przykładzie przyjęto założenie, że zalogowano się jako **użytkownik AzureUser** i utworzono certyfikat urzędu certyfikacji urządzenia o nazwie "mycacert".

1. Zapisz zmiany i uruchom ponownie IoT Edge uruchomieniowego:

    ```bash
    sudo systemctl restart iotedge
    ```

Jeśli środowisko IoT Edge uruchomieniowe zostanie uruchomione pomyślnie po  wprowadzeniu zmian, stan modułów $edgeAgent i **$edgeHub** zmieni się na **Uruchomione.** Te wartości stanu są dostępne na stronie **Moduły** dla urządzenia bramy w IoT Central.

Jeśli środowisko uruchomieniowe nie zostanie wykonane, sprawdź zmiany wprowadzone w pliku *config.yaml* i zobacz Rozwiązywanie problemów [IoT Edge urządzeniu](../../iot-edge/troubleshoot.md).

### <a name="connect-downstream-device-to-iot-edge-device"></a>Łączenie urządzenia podrzędnego z IoT Edge urządzeniem

Aby połączyć urządzenie podrzędne z urządzeniem IoT Edge bramy:

1. Użyj połączenia SSH, aby nawiązać połączenie i zalogować się na maszynie wirtualnej urządzenia podrzędnego. Nazwę DNS tej maszyny wirtualnej można znaleźć w Azure Portal. Przejdź do **maszyny wirtualnej leafdevice** w grupie **zasobów ingress-scenario.**

    > [!TIP]
    > Może być konieczne otwarcie portu 22 dla dostępu SSH na obu maszynach wirtualnych, zanim będzie można nawiązać połączenie za pomocą połączenia SSH z komputera lokalnego lub Azure Cloud Shell.

1. Aby sklonować repozytorium GitHub z kodem źródłowym przykładowego urządzenia podrzędnego, uruchom następujące polecenie:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Aby skopiować wymagany certyfikat z urządzenia bramy, uruchom następujące `scp` polecenia. To `scp` polecenie używa nazwy hosta do identyfikowania maszyny `edgegateway` wirtualnej bramy. Zostanie wyświetlony monit o hasło:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Przejdź do folderu *leafdevice* i zainstaluj wymagane pakiety. Następnie uruchom `build` skrypty i `start` , aby aprowizować urządzenie i połączyć je z bramą:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Wprowadź identyfikator urządzenia, identyfikator zakresu i klucz sygnatury dostępu współdzielonego dla utworzonego wcześniej urządzenia podrzędnego. Jako nazwę hosta wprowadź `edgegateway` . Dane wyjściowe polecenia wyglądają następująco:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Weryfikacja

Aby sprawdzić, czy scenariusz jest uruchomiony, przejdź do **IoT Edge bramy w** IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Zrzut ekranu przedstawiający przekształcone dane na stronie urządzeń.":::

- Wybierz **pozycję Moduły.** Sprawdź, czy trzy moduły IoT Edge, **$edgeAgent**, **$edgeHub** **i transformmodule** są uruchomione.
- Wybierz pozycję **Urządzenia podrzędne i** sprawdź, czy urządzenie podrzędne zostało zaaprowizowane.
- Wybierz **pozycję Dane pierwotne.** Dane telemetryczne w kolumnie **Dane niemodelowane** wyglądają następująco:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Ponieważ IoT Edge przekształca dane z urządzenia podrzędnego, telemetria jest skojarzona z urządzeniem bramy w IoT Central. Aby zwizualizować dane telemetryczne, utwórz nową wersję szablonu urządzenia bramy **IoT Edge z** definicjami typów telemetrii.

## <a name="data-transformation-at-egress"></a>Przekształcanie danych na wy wychodzącym

Możesz połączyć urządzenia z usługą IoT Central, wyeksportować dane urządzenia do aparatu obliczeniowego, aby je przekształcić, a następnie wysłać przekształcone dane z powrotem do IoT Central w celu zarządzania urządzeniami i ich analizy. Na przykład:

- Urządzenia wysyłają dane lokalizacji do IoT Central.
- IoT Central eksportuje dane do aparatu obliczeniowego, który rozszerza dane lokalizacji o informacje o pogodzie.
- Aparat obliczeniowy wysyła rozszerzone dane z powrotem do IoT Central.

Możesz użyć mostka [urządzenia IoT Central](https://github.com/Azure/iotc-device-bridge) jako aparatu obliczeniowego do przekształcania danych wyeksportowanych z IoT Central.

Zaletą przekształcania danych na wyjście jest to, że urządzenia łączą się bezpośrednio z usługą IoT Central, co ułatwia wysyłanie poleceń do urządzeń lub aktualizowanie właściwości urządzenia. Jednak w przypadku tej metody można użyć większej liczby komunikatów niż miesięcznych i zwiększyć koszt korzystania z Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Przekształcanie danych IoT Central za pomocą mostka urządzenia

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Przekształcanie danych na ruch wychodzący przy użyciu IoT Edge" border="false":::

W tym scenariuszu aparat obliczeniowy przekształca dane urządzenia wyeksportowane z IoT Central przed wysłaniem ich z powrotem do IoT Central aplikacji. Na wysokim poziomie kroki konfigurowania tego scenariusza są następujące:

1. **Konfigurowanie aparatu obliczeniowego:** Utwórz mostek IoT Central, który będzie działać jako aparat obliczeniowy do przekształcania danych.

1. **Przekształć dane urządzenia w mostku urządzenia:** Przekształć dane w mostku urządzenia, modyfikując kod funkcji mostka urządzenia na potrzeby przypadku użycia przekształcania danych.

1. **Włącz przepływ danych z IoT Central do mostka urządzenia:** Wyeksportuj dane z IoT Central do mostka urządzenia w celu przekształcenia. Następnie przekaż przekształcone dane z powrotem do IoT Central. Podczas tworzenia eksportu danych użyj filtrów właściwości komunikatów, aby wyeksportować tylko nieprzekształcone dane.

1. **Sprawdź:** Połącz urządzenie z aplikacją IoT Central i sprawdź, czy w aplikacji są zarówno nieprzetworzone dane urządzenia, jak i przekształcone IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
W przykładzie opisanym w poniższych sekcjach urządzenie wysyła dane CSV w następującym formacie do IoT Edge bramy:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Mostek urządzenia umożliwia przekształcanie danych urządzenia przez:

- Zmiana jednostki temperatury z centygrade na fahrenheit.
- Wzbogacanie danych urządzenia o dane pogodowe ściągnięte z usługi [Open Weather](https://openweathermap.org/) dla wartości szerokości i długości geograficznej.

Następnie mostek urządzenia wysyła przekształcone dane do IoT Central w następującym formacie:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

Poniższe kroki pokazują, jak skonfigurować i skonfigurować ten scenariusz:

### <a name="retrieve-your-iot-central-connection-settings"></a>Pobieranie ustawień IoT Central połączenia sieciowego

Przed skonfigurowaniem tego scenariusza należy pobrać niektóre ustawienia połączenia z aplikacji IoT Central aplikacji:

1. Zaloguj się do aplikacji IoT Central aplikacji.

1. Przejdź do **menu Administracja > połączenie urządzenia.**

1. Zanotuj **zakres identyfikatorów**. Ta wartość zostanie później użycia.

1. Wybierz **grupę rejestracji SaS-IoT-Devices.** Zanotuj klucz podstawowy sygnatury dostępu współdzielonych. Ta wartość zostanie później użycia.

### <a name="set-up-a-compute-engine"></a>Konfigurowanie aparatu obliczeniowego

W tym scenariuszu jest używane Azure Functions wdrożenia co IoT Central mostka urządzenia. Aby wdrożyć mostek urządzeń, wybierz przycisk Wdoń na platformie **Azure** poniżej i skorzystaj z informacji w poniższej tabeli, aby ukończyć formularz **wdrożenie niestandardowe:**

| Pole | Wartość |
| ----- | ----- |
| Grupa zasobów | Utwórz nową grupę zasobów o nazwie `egress-scenario` |
| Region (Region) | Wybierz region znajdujący się najbliżej Ciebie. |
| Identyfikator zakresu | Użyj **zanotowego** wcześniej zakresu identyfikatorów. |
| IoT Central SAS Key | Użyj klucza podstawowego sygnatury dostępu współdzielonych dla grupy rejestracji **SaS-IoT-Devices.** Ta wartość została zanotowana wcześniej. |

[ ![ Wdrażanie na platformie Azure.](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Wybierz **pozycję Przeglądanie + tworzenie,** a następnie pozycję **Utwórz.** Utworzenie funkcji platformy Azure i powiązanych zasobów w grupie zasobów scenariusza wychodzącego może potrwać **kilka** minut.

### <a name="transform-device-data-in-the-device-bridge"></a>Przekształcanie danych urządzenia w mostku urządzenia

Aby skonfigurować mostek urządzenia do przekształcania wyeksportowanych danych urządzenia:

1. Uzyskaj klucz interfejsu API aplikacji z okna usługa pogodowa. Konto jest bezpłatne z ograniczonym użyciem usługi. Aby utworzyć klucz interfejsu API aplikacji, utwórz konto w witrynie [Open usługa pogodowa Portal](https://openweathermap.org/) i postępuj zgodnie z instrukcjami. Klucz interfejsu OPEN Weather API będzie można użyć później.

1. W Azure Portal przejdź do aplikacji funkcji w grupie zasobów **egress-scenario.**

1. W lewym okienku nawigacji w **narzędziu Development Tools** wybierz **pozycję Edytor usługi App Service (wersja zapoznawcza).**

1. Wybierz **&rarr; pozycję Przejdź,** aby **otworzyć Edytor usługi App Service** strony. Wprowadź następujące zmiany:

    1. Otwórz plik *wwwroot/IoTCIntegration/index.js* plik . Zastąp cały kod w tym pliku kodem w pliku [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. W nowej *index.js* zaktualizuj plik zmiennej przy użyciu uzyskanego wcześniej klucza `openWeatherAppId` interfejsu API Open Weather.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Dodaj właściwość komunikatu do danych wysyłanych przez funkcję do IoT Central. IoT Central używa tej właściwości, aby uniemożliwić eksportowanie przekształcone dane. Aby wprowadzić tę zmianę, otwórz *plik wwwroot/IoTCIntegration/lib/engine.js* plik . Znajdź następujący kod:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Dodaj następujący kod tuż po kodzie z poprzedniego fragmentu kodu:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Aby uzyskać informacje, możesz wyświetlić ukończony przykład [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) pliku.

1. W **okienku Edytor usługi App Service** pozycję **Konsola w** lewym panelu nawigacyjnym. Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

1. Wróć do strony **Przegląd funkcji platformy Azure** i uruchom ponownie funkcję:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Uruchom ponownie funkcję":::

1. Wybierz **pozycję Funkcje** w lewym panelu nawigacyjnym. Następnie wybierz **pozycję IoTCIntegration.** Wybierz **pozycję Kod i przetestuj**.

1. Zanotuj adres URL funkcji. Ta wartość będzie potrzebna później:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Uzyskiwanie adresu URL funkcji":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Włączanie przepływu danych z IoT Central do mostka urządzenia

W tej sekcji opisano sposób Azure IoT Central aplikacji.

Najpierw zapisz plik [modelu urządzenia](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) na komputerze lokalnym.

Aby dodać szablon urządzenia do aplikacji IoT Central, przejdź do IoT Central aplikacji, a następnie:

1. Zaloguj się do aplikacji IoT Central i przejdź do **strony Szablony** urządzeń.

1. Wybierz **pozycję + Nowe,** wybierz pozycję **Urządzenie IoT,** wybierz pozycję **Dalej: Dostosuj**, wprowadź nazwę szablonu *Model* obliczeniowy. Wybierz opcję **Dalej: Review** (Dalej: przegląd). Następnie wybierz pozycję **Utwórz**.

1. Wybierz **pozycję Importuj model** i przejdź domodel.js *wcześniej* pobranego pliku.

1. Po zaimportowaniu modelu wybierz  pozycję Publikuj, aby opublikować **szablon urządzenia modelu** obliczeniowego.

Aby skonfigurować eksport danych w celu wysyłania danych do mostka urządzenia:

1. W aplikacji IoT Central wybierz pozycję **Eksportuj dane.**

1. Wybierz **pozycję + Nowe miejsce** docelowe, aby utworzyć miejsce docelowe do użycia z mostkiem urządzenia. Wywołaj *docelową funkcję obliczeniową*, w **przypadku opcji Typ docelowy** wybierz pozycję **Webhook**. W polu Adres URL wywołania zwrotnego wybierz opcję wklej adres URL funkcji zanotuj wcześniej. Pozostaw **uwierzytelnianie** bez **uwierzytelniania.**

1. Zapisz zmiany.

1. Wybierz pozycję **+ Nowy eksport i** utwórz eksport danych o nazwie Eksport *obliczeń.*

1. Dodaj filtr, aby wyeksportować tylko dane urządzenia dla szablonu urządzenia, z których korzystasz. Wybierz **pozycję + Filtr,** wybierz element **Szablon urządzenia,** wybierz operator **Równa** się i wybierz właśnie utworzony **szablon** urządzenia Model obliczeniowy.

1. Dodaj filtr komunikatów, aby rozróżnić przekształcone i nieprzekształcone dane. Ten filtr uniemożliwia wysyłanie przekształceń wartości z powrotem do mostka urządzenia. Wybierz **pozycję + Filtr właściwości komunikatu** i wprowadź obliczoną *wartość* nazwy, a następnie wybierz operator **Nie istnieje.** Ciąg jest `computed` używany jako słowo kluczowe w przykładowym kodzie mostka urządzenia.

1. Jako miejsce docelowe wybierz utworzone wcześniej **miejsce** docelowe funkcji obliczeniowej.

1. Zapisz zmiany. Po około minucie stan **Eksportuj będzie następujący:** W dobrej **kondycji.**

### <a name="verify"></a>Weryfikacja

Przykładowe urządzenie służące do testowania scenariusza jest napisane w Node.js. Upewnij się, że Node.js i npm zainstalowane na komputerze lokalnym. Jeśli nie chcesz instalować tych wymagań wstępnych, użyj[Azure Cloud Shell,](https://shell.azure.com/) która ma je wstępnie zainstalowane.

Aby uruchomić przykładowe urządzenie, które testuje scenariusz:

1. Sklonuj repozytorium GitHub zawierające przykładowy kod, uruchom następujące polecenie:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Aby połączyć przykładowe urządzenie z aplikacją IoT Central, edytuj ustawienia połączenia w pliku *iot-central-compute/device/device.js.* Zastąp identyfikator zakresu i klucz sygnatury dostępu współdzielonego grupy wcześniej zanotymi wartościami:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Zapisz zmiany.

1. Użyj następujących poleceń, aby zainstalować wymagane pakiety i uruchomić urządzenie:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Wynik tego polecenia wygląda podobnie do następujących danych wyjściowych:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. W aplikacji IoT Central przejdź do urządzenia o nazwie **computeDevice.** W widoku **Dane pierwotne** znajdują się dwa różne strumienie danych telemetrycznych, które są wyświetlane mniej więcej co pięć sekund. Strumień z nie modelowanych danych to oryginalna telemetria, a strumień z modelowanych danych to dane przekształcone przez funkcję:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Zrzut ekranu przedstawiający oryginalne i przekształcone dane pierwotne.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów platformy Azure utworzonych podczas pracy z krokami w tym przewodniku, usuń grupy zasobów w Azure Portal [.](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)

Dwie grupy zasobów używane w tym przewodniku to **scenariusz przychodzący i** **wychodzący.**

## <a name="next-steps"></a>Następne kroki

W tym artykule o różne opcje przekształcania danych urządzenia na IoT Central przychodzących i wychodzących. Artykuł zawierał wskazówki dotyczące dwóch określonych scenariuszy:

- Użyj modułu IoT Edge, aby przekształcić dane z urządzeń dalszych przed ich wysłaniem do IoT Central aplikacji.
- Użyj Azure Functions do przekształcania danych poza IoT Central. W tym scenariuszu IoT Central eksportu danych do wysyłania danych przychodzących do funkcji platformy Azure, która ma zostać przekształcona. Funkcja wysyła przekształcone dane z powrotem do IoT Central aplikacji.

Teraz, gdy już wiesz, jak przekształcać dane urządzenia poza aplikację usługi Azure IoT Central, możesz dowiedzieć się, jak używać analizy do analizowania danych urządzeń w IoT Central [.](howto-create-analytics.md)
