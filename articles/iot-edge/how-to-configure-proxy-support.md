---
title: Konfigurowanie urządzeń dla serwerów proxy sieciowych — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Jak skonfigurować środowisko uruchomieniowe usługi Azure IoT Edge i wszystkie moduły usługi IoT Edge z dostępem do Internetu do komunikowania się za pośrednictwem serwera proxy.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 270e6a0173ed0088ff5d37c989947f5272634200
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687199"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configure an IoT Edge device to communicate through a proxy server (Konfigurowanie urządzenia usługi IoT Edge pod kątem komunikacji za pośrednictwem serwera proxy)

Urządzenia usługi IoT Edge wysyłają żądania HTTPS do komunikowania się z Centrum IoT Hub. Jeśli urządzenie jest połączone z siecią korzystającą z serwera proxy, należy skonfigurować środowisko wykonawcze usługi IoT Edge do komunikowania się za pośrednictwem serwera. Serwery proxy mogą również wpływać na poszczególne moduły usługi IoT Edge, jeśli żądań HTTP lub HTTPS, które nie są kierowane za pośrednictwem centrum usługi IoT Edge.

W tym artykule przedstawiono następujące cztery kroki, aby skonfigurować, a następnie zarządzać urządzeniem usługi IoT Edge za serwerem proxy:

1. **Zainstaluj środowisko wykonawcze usługi IoT Edge na urządzeniu.**

   Skrypty instalacyjne usługi IoT Edge ściągają pakiety i pliki z Internetu, więc urządzenie musi komunikować się za pośrednictwem serwera proxy, aby te żądania. Aby uzyskać szczegółowe kroki, zobacz [instalowanie środowiska wykonawczego za pośrednictwem](#install-the-runtime-through-a-proxy) serwera proxy sekcji tego artykułu. W przypadku urządzeń z systemem Windows skrypt instalacyjny udostępnia również opcję [instalacji w trybie offline.](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

   Ten krok jest procesem jednorazowym wykonywanym na urządzeniu IoT Edge przy pierwszym skonfigurowaniu. Te same połączenia są również wymagane podczas aktualizowania środowiska wykonawczego usługi IoT Edge.

2. **Skonfiguruj demona platformy Docker i demona IoT Edge na urządzeniu.**

   Usługa IoT Edge używa dwóch demonów na urządzeniu, z których oba muszą składać żądania sieci web za pośrednictwem serwera proxy. Demon usługi IoT Edge jest odpowiedzialny za komunikację z Centrum IoT. Demon Moby jest odpowiedzialny za zarządzanie kontenerami, więc komunikuje się z rejestrami kontenerów. Aby uzyskać szczegółowe kroki, zobacz [Konfigurowanie demonów](#configure-the-daemons) sekcji tego artykułu.

   Ten krok jest procesem jednorazowym wykonywanym na urządzeniu IoT Edge przy pierwszym skonfigurowaniu.

3. **Skonfiguruj właściwości agenta usługi IoT Edge w pliku config.yaml na urządzeniu.**

   Demon usługi IoT Edge uruchamia moduł edgeAgent początkowo, ale następnie moduł edgeAgent jest odpowiedzialny za pobieranie manifestu wdrażania z usługi IoT Hub i uruchamianie wszystkich innych modułów. Aby agent usługi IoT Edge nawiązał początkowe połączenie z centrum IoT Hub, należy ręcznie skonfigurować zmienne środowiskowe modułu edgeAgent na samym urządzeniu. Po połączeniu początkowym można zdalnie skonfigurować moduł edgeAgent. Aby uzyskać szczegółowe kroki, zobacz [konfigurowanie agenta IoT Edge](#configure-the-iot-edge-agent) sekcji tego artykułu.

   Ten krok jest procesem jednorazowym wykonywanym na urządzeniu IoT Edge przy pierwszym skonfigurowaniu.

4. **W przypadku wszystkich przyszłych wdrożeń modułów należy ustawić zmienne środowiskowe dla dowolnego modułu komunikującego się za pośrednictwem serwera proxy.**

   Po skonfigurowaniu urządzenia Usługi IoT Edge i podłączeniu go do usługi IoT Hub za pośrednictwem serwera proxy należy zachować połączenie we wszystkich przyszłych wdrożeniach modułów. Aby uzyskać szczegółowe kroki, zobacz [konfigurowanie manifestów wdrażania](#configure-deployment-manifests) sekcji tego artykułu.

   Ten krok jest procesem ciągłym wykonywanym zdalnie, dzięki czemu każdy nowy moduł lub aktualizacja wdrożenia zachowuje zdolność urządzenia do komunikowania się za pośrednictwem serwera proxy.

## <a name="know-your-proxy-url"></a>Poznaj swój adres URL serwera proxy

Przed rozpoczęciem któregokolwiek z kroków w tym artykule, należy znać adres URL serwera proxy.

Adresy URL serwera proxy przyjmują następujący format: **protocol**://**proxy_host**:**proxy_port**.

* **Protokół** jest HTTP lub HTTPS. Demon platformy Docker może używać albo protokołu, w zależności od ustawień rejestru kontenera, ale demon IoT Edge i kontenery środowiska wykonawczego powinny zawsze używać protokołu HTTP do łączenia się z serwerem proxy.

* **proxy_host** jest adresem serwera proxy. Jeśli serwer proxy wymaga uwierzytelniania, można podać poświadczenia jako część hosta serwera proxy w następującym formacie: **użytkownik:****hasło**\@**proxy_host**.

* **proxy_port** jest portem sieciowym, na którym serwer proxy reaguje na ruch sieciowy.

## <a name="install-the-runtime-through-a-proxy"></a>Instalowanie środowiska wykonawczego za pośrednictwem serwera proxy

Niezależnie od tego, czy urządzenie Usługi IoT Edge działa w systemie Windows czy Linux, musisz uzyskać dostęp do pakietów instalacyjnych za pośrednictwem serwera proxy. W zależności od systemu operacyjnego wykonaj kroki, aby zainstalować środowisko uruchomieniowe usługi IoT Edge za pośrednictwem serwera proxy.

### <a name="linux-devices"></a>Urządzenia z systemem Linux

Jeśli instalujesz środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Linux, skonfiguruj menedżera pakietów, aby przechodził przez serwer proxy, aby uzyskać dostęp do pakietu instalacyjnego. Na przykład [konfigurowanie apt-get do korzystania z http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po skonfigurowaniu menedżera pakietów postępuj zgodnie z instrukcjami w [install Azure IoT Edge runtime w systemie Linux](how-to-install-iot-edge-linux.md) w zwykły sposób.

### <a name="windows-devices"></a>Urządzenia z systemem Windows

Jeśli instalujesz środowisko wykonawcze usługi IoT Edge na urządzeniu z systemem Windows, musisz dwukrotnie przejść przez serwer proxy. Pierwsze połączenie pobiera plik skryptu instalatora, a drugie jest podczas instalacji, aby pobrać niezbędne składniki. Informacje o serwerze proxy można skonfigurować w ustawieniach systemu Windows lub uwzględnić informacje o serwerze proxy bezpośrednio w poleceniach programu PowerShell.

W poniższych krokach przedstawiono przykład `-proxy` instalacji systemu Windows przy użyciu argumentu:

1. Polecenie Invoke-WebRequest potrzebuje informacji proxy, aby uzyskać dostęp do skryptu instalatora. Następnie polecenie Deploy-IoTEdge potrzebuje informacji proxy, aby pobrać pliki instalacyjne.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Polecenie Initialize-IoTEdge nie musi przechodzić przez serwer proxy, więc drugi krok wymaga tylko informacji proxy dla invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Jeśli masz skomplikowane poświadczenia serwera proxy, których nie można uwzględnić w `-ProxyCredential` adresie URL, użyj tego parametru w programie `-InvokeWebRequestParameters`. Na przykład:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Aby uzyskać więcej informacji na temat parametrów serwera proxy, zobacz [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Aby uzyskać więcej informacji na temat opcji instalacji systemu Windows, w tym instalacji w trybie offline, zobacz [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurowanie demonów

IoT Edge opiera się na dwóch demonach uruchomionych na urządzeniu IoT Edge. Demon Moby sprawia, że żądania sieci web do ciągnięcia obrazów kontenerów z rejestrów kontenerów. Demon usługi IoT Edge umożliwia komunikację z centrum IoT Hub.

Demony Moby i IoT Edge muszą być skonfigurowane do używania serwera proxy do bieżącej funkcjonalności urządzenia. Ten krok odbywa się na urządzeniu Usługi IoT Edge podczas wstępnej konfiguracji urządzenia.

### <a name="moby-daemon"></a>Demon Moby

Ponieważ moby jest zbudowany na platformy Docker, zapoznaj się z dokumentacją platformy Docker, aby skonfigurować demona Moby ze zmiennymi środowiskowymi. Większość rejestrów kontenerów (w tym DockerHub i Azure Container Registries) obsługuje żądania HTTPS, więc parametr, który należy ustawić, jest **HTTPS_PROXY.** Jeśli pobierasz obrazy z rejestru, który nie obsługuje zabezpieczeń warstwy transportu (TLS), należy ustawić **parametr HTTP_PROXY.**

Wybierz artykuł, który ma zastosowanie do systemu operacyjnego urządzenia IoT Edge:

* [Konfigurowanie demona platformy Docker w systemie Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Demon Moby na urządzeniach z systemem Linux zachowuje nazwę Docker.
* [Konfigurowanie demona platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Demon Moby na urządzeniach z systemem Windows nazywa się iotedge-moby. Nazwy są różne, ponieważ można uruchomić zarówno Pulpit platformy Docker, jak i Moby równolegle na urządzeniu z systemem Windows.

### <a name="iot-edge-daemon"></a>Demon IoT Edge

Demon IoT Edge jest skonfigurowany w podobny sposób jak demon Moby. Poniższe kroki można wykonać, aby ustawić zmienną środowiskową dla usługi na podstawie systemu operacyjnego.

Demon IoT Edge zawsze używa protokołu HTTPS do wysyłania żądań do centrum IoT Hub.

#### <a name="linux"></a>Linux

Otwórz edytor w terminalu, aby skonfigurować demona IoT Edge.

```bash
sudo systemctl edit iotedge
```

Wprowadź następujący tekst, ** \<** zastępując adres URL serwera proxy>adresem serwera proxy i portem. Następnie zapisz i wyjdź.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Odśwież menedżera usług, aby odebrać nową konfigurację usługi IoT Edge.

```bash
sudo systemctl daemon-reload
```

Uruchom ponownie usługę IoT Edge, aby zmiany zostały wprowadzone.

```bash
sudo systemctl restart iotedge
```

Sprawdź, czy zmienna środowiskowa została utworzona i nowa konfiguracja została załadowana.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Otwórz okno programu PowerShell jako administrator i uruchom następujące polecenie, aby edytować rejestr za pomocą nowej zmiennej środowiskowej. Zastąp ** \<adres URL serwera proxy>** adresem serwera proxy i portem.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Uruchom ponownie usługę IoT Edge, aby zmiany zostały wprowadzone.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurowanie agenta IoT Edge

Agent IoT Edge jest pierwszym modułem, który uruchamia się na dowolnym urządzeniu IoT Edge. Jest uruchamiany po raz pierwszy na podstawie informacji w pliku config.yaml usługi IoT Edge. Agent usługi IoT Edge następnie łączy się z Centrum IoT Hub, aby pobrać manifesty wdrażania, które deklarują, jakie inne moduły powinny być wdrażane na urządzeniu.

Ten krok odbywa się raz na urządzeniu Usługi IoT Edge podczas początkowej konfiguracji urządzenia.

1. Otwórz plik config.yaml na urządzeniu Usługi IoT Edge. W systemach Linux ten plik znajduje się pod adresem **/etc/iotedge/config.yaml**. W systemach Windows ten plik znajduje się pod adresem **C:\ProgramData\iotedge\config.yaml**. Plik konfiguracyjny jest chroniony, więc aby uzyskać do niego dostęp, potrzebne są uprawnienia administracyjne. W systemach Linux `sudo` użyj polecenia przed otwarciem pliku w preferowanym edytorze tekstu. W systemie Windows otwórz edytor tekstu, taki jak Notatnik jako administrator, a następnie otwórz plik.

2. W pliku config.yaml znajdź sekcję **specyfikacji modułu agenta usługi Edge.** Definicja agenta IoT Edge zawiera **env** parametr, w którym można dodawać zmienne środowiskowe.

3. Usuń nawiasy klamrowe, które są symbolami zastępczymi parametru env, i dodaj nową zmienną w nowym wierszu. Należy pamiętać, że wcięcie w YAML to dwie spacje.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Środowisko uruchomieniowe usługi IoT Edge domyślnie używa usługi AMQP do rozmów z centrum IoT Hub. Niektóre serwery proxy blokują porty AMQP. Jeśli tak jest, to trzeba również skonfigurować edgeAgent do korzystania z AMQP przez WebSocket. Dodaj drugą zmienną środowiskową.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definicja edgeAgent ze zmiennymi środowiskowymi](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Zapisz zmiany na config.yaml i zamknij edytor. Uruchom ponownie usługę IoT Edge, aby zmiany zostały wprowadzone.

   * W systemie Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * W systemie Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurowanie manifestów wdrażania  

Po skonfigurowaniu urządzenia usługi IoT Edge do pracy z serwerem proxy należy kontynuować deklarowanie zmiennej środowiskowej HTTPS_PROXY w przyszłych manifestach wdrażania. Manifesty wdrażania można edytować za pomocą kreatora portalu Azure lub edytując plik JSON manifestu wdrożenia.

Zawsze skonfiguruj dwa moduły środowiska wykonawczego, edgeAgent i edgeHub, aby komunikować się za pośrednictwem serwera proxy, aby mogły utrzymywać połączenie z centrum IoT Hub. Jeśli usuniesz informacje proxy z modułu edgeAgent, jedynym sposobem ponownego nawiązania połączenia jest edycja pliku config.yaml na urządzeniu, zgodnie z opisem w poprzedniej sekcji.

Oprócz edgeAgent i edgeHub modułów, inne moduły mogą wymagać konfiguracji serwera proxy. Są to moduły, które muszą uzyskać dostęp do zasobów platformy Azure oprócz usługi IoT Hub, takich jak magazyn obiektów blob i muszą mieć HTTPS_PROXY zmienną określoną dla tego modułu w pliku manifestu wdrożenia.

Poniższa procedura ma zastosowanie przez cały okres użytkowania urządzenia IoT Edge.

### <a name="azure-portal"></a>Azure Portal

Korzystając z kreatora **Set modules** do tworzenia wdrożeń dla urządzeń usługi IoT Edge, każdy moduł ma sekcję **Zmienne środowiskowe,** której można użyć do skonfigurowania połączeń serwera proxy.

Aby skonfigurować agenta usługi IoT Edge i moduły koncentratora usługi IoT Edge, wybierz **pozycję Ustawienia środowiska wykonawczego** w pierwszym kroku kreatora.

![Konfigurowanie zaawansowanych ustawień środowiska wykonawczego aplikacji Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Dodaj **https_proxy** zmienną środowiskową zarówno do definicji modułów agenta usługi IoT Edge, jak i do modułów koncentratora usługi IoT Edge. Jeśli zmienna środowiskowa **UpstreamProtocol** została uwzględniona w pliku config.yaml na urządzeniu Usługi IoT Edge, dodaj to również do definicji modułu agenta usługi IoT Edge.

![Ustawianie zmiennej środowiskowej https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Wszystkie inne moduły, które można dodać do manifestu wdrożenia wykonaj ten sam wzorzec. Na stronie, na której ustawiono nazwę i obraz modułu, znajduje się sekcja zmiennych środowiskowych.

### <a name="json-deployment-manifest-files"></a>Pliki manifestu wdrażania JSON

Jeśli tworzysz wdrożenia dla urządzeń usługi IoT Edge przy użyciu szablonów w programie Visual Studio Code lub ręcznie tworząc pliki JSON, można dodać zmienne środowiskowe bezpośrednio do każdej definicji modułu.

Użyj następującego formatu JSON:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Z uwzględnionych zmiennych środowiskowych definicja modułu powinna wyglądać następująco na przykładzie edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Jeśli zmienna środowiskowa **UpstreamProtocol** została uwzględniona w pliku confige.yaml na urządzeniu Usługi IoT Edge, dodaj to również do definicji modułu agenta usługi IoT Edge.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rolach [środowiska wykonawczego IoT Edge](iot-edge-runtime.md).

Rozwiązywanie problemów z błędami instalacji i konfiguracji za pomocą [typowych problemów i rozwiązań usługi Azure IoT Edge](troubleshoot.md)
