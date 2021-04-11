---
title: Konfigurowanie urządzeń dla serwerów proxy sieci — Azure IoT Edge | Microsoft Docs
description: Jak skonfigurować środowisko uruchomieniowe Azure IoT Edge i wszystkie moduły IoT Edge dostępne z Internetu w celu komunikowania się za pomocą serwera proxy.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 9f2ca089a6d885227bd61940d71ec7bb7960fbd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043159"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configure an IoT Edge device to communicate through a proxy server (Konfigurowanie urządzenia usługi IoT Edge pod kątem komunikacji za pośrednictwem serwera proxy)

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge urządzenia wysyłają żądania HTTPS w celu komunikowania się z IoT Hub. Jeśli urządzenie jest podłączone do sieci korzystającej z serwera proxy, należy skonfigurować środowisko uruchomieniowe IoT Edge, aby komunikować się za pośrednictwem serwera. Serwery proxy mogą również wpływać na poszczególne moduły IoT Edge, jeśli wysyłają żądania HTTP lub HTTPS, które nie są kierowane przez Centrum IoT Edge.

W tym artykule omówiono następujące cztery kroki konfigurowania urządzenia IoT Edge za serwerem proxy oraz zarządzania nim:

1. [**Zainstaluj środowisko uruchomieniowe IoT Edge na urządzeniu**](#install-iot-edge-through-a-proxy)

   Program IoT Edge tworzy skrypty ściągania pakietów i plików z Internetu, dzięki czemu urządzenie musi się komunikować za pośrednictwem serwera proxy, aby wykonać te żądania. W przypadku urządzeń z systemem Windows skrypt instalacji udostępnia również opcję instalacji w trybie offline.

   Ten krok to jednorazowy proces konfigurowania IoT Edge urządzenia podczas jego pierwszego skonfigurowania. Te same połączenia są również wymagane w przypadku aktualizowania środowiska uruchomieniowego IoT Edge.

2. [**Konfigurowanie IoT Edge i środowiska uruchomieniowego kontenera na urządzeniu**](#configure-iot-edge-and-moby)

   IoT Edge jest odpowiedzialna za komunikację z IoT Hub. Środowisko uruchomieniowe kontenera jest odpowiedzialne za zarządzanie kontenerami, dlatego komunikuje się z rejestrami kontenerów. Oba te składniki muszą udostępniać żądania sieci Web za pomocą serwera proxy.

   Ten krok to jednorazowy proces konfigurowania IoT Edge urządzenia podczas jego pierwszego skonfigurowania.

3. [**Skonfiguruj właściwości agenta IoT Edge w pliku konfiguracji na urządzeniu**](#configure-the-iot-edge-agent)

   Demon IoT Edge początkowo uruchamia moduł edgeAgent. Następnie moduł edgeAgent pobiera manifest wdrożenia z IoT Hub i uruchamia wszystkie inne moduły. Aby Agent IoT Edge mógł nawiązać początkowe połączenie IoT Hub, ręcznie skonfiguruj zmienne środowiskowe modułu edgeAgent na samym urządzeniu. Po początkowym połączeniu można skonfigurować moduł edgeAgent zdalnie.

   Ten krok to jednorazowy proces konfigurowania IoT Edge urządzenia podczas jego pierwszego skonfigurowania.

4. [**Dla wszystkich przyszłych wdrożeń modułu należy ustawić zmienne środowiskowe dla dowolnego modułu, który komunikuje się za pomocą serwera proxy**](#configure-deployment-manifests)

   Gdy urządzenie IoT Edge zostanie skonfigurowane i połączone z IoT Hub za pomocą serwera proxy, należy zachować połączenie we wszystkich przyszłych wdrożeniach modułów.

   Ten krok to proces wykonywany zdalnie w taki sposób, że każdy nowy moduł lub aktualizacja wdrożenia utrzymuje zdolność urządzenia do komunikowania się za pomocą serwera proxy.

## <a name="know-your-proxy-url"></a>Znać adres URL serwera proxy

Przed rozpoczęciem któregokolwiek z kroków opisanych w tym artykule musisz znać adres URL serwera proxy.

Adresy URL serwera proxy mają następujący format: **Protokół**://**proxy_host**:**proxy_port**.

* **Protokół** to http lub https. Demon platformy Docker może używać dowolnego protokołu, w zależności od ustawień rejestru kontenerów, ale IoT Edge w celu nawiązania połączenia z serwerem proxy należy używać protokołu HTTP.

* **Proxy_host** jest adresem serwera proxy. Jeśli serwer proxy wymaga uwierzytelnienia, możesz podać swoje poświadczenia jako część hosta proxy w następującym formacie: **użytkownik**:**hasło** \@ **proxy_host**.

* **Proxy_port** jest portem sieciowym, w którym serwer proxy reaguje na ruch sieciowy.

## <a name="install-iot-edge-through-a-proxy"></a>Instalowanie IoT Edge za pomocą serwera proxy

Niezależnie od tego, czy urządzenie IoT Edge działa w systemie Windows, czy Linux, musisz uzyskać dostęp do pakietów instalacyjnych za pomocą serwera proxy. W zależności od używanego systemu operacyjnego postępuj zgodnie z instrukcjami, aby zainstalować środowisko uruchomieniowe IoT Edge za pomocą serwera proxy.

### <a name="linux-devices"></a>Urządzenia z systemem Linux

Jeśli instalujesz środowisko uruchomieniowe IoT Edge na urządzeniu z systemem Linux, Skonfiguruj Menedżera pakietów, aby przeszedł serwer proxy w celu uzyskania dostępu do pakietu instalacyjnego. Na przykład [Skonfiguruj apt-get do korzystania z protokołu HTTP-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po skonfigurowaniu Menedżera pakietów postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime](how-to-install-iot-edge.md) w zwykły sposób.

### <a name="windows-devices-using-iot-edge-for-linux-on-windows"></a>Urządzenia z systemem Windows, które używają IoT Edge dla systemu Linux w systemie Windows

Jeśli instalujesz środowisko uruchomieniowe IoT Edge przy użyciu IoT Edge dla systemu Linux w systemie Windows, IoT Edge jest instalowany domyślnie na maszynie wirtualnej z systemem Linux. Nie są wymagane żadne dodatkowe kroki instalacji ani aktualizacji.

### <a name="windows-devices-using-windows-containers"></a>Urządzenia z systemem Windows korzystające z kontenerów systemu Windows

Jeśli instalujesz środowisko uruchomieniowe IoT Edge na urządzeniu z systemem Windows, musisz dwa razy przejść przez serwer proxy. Pierwsze połączenie pobiera plik skryptu Instalatora, a drugie podczas instalacji, aby pobrać niezbędne składniki. Informacje o serwerze proxy można skonfigurować w ustawieniach systemu Windows lub dołączyć informacje o serwerze proxy bezpośrednio w poleceniach programu PowerShell.

Poniższe kroki przedstawiają przykład instalacji systemu Windows przy użyciu `-proxy` argumentu:

1. Polecenie Invoke-WebRequest wymaga informacji o serwerze proxy w celu uzyskania dostępu do skryptu Instalatora. Następnie Deploy-IoTEdge polecenie wymaga informacji o serwerze proxy do pobrania plików instalacyjnych.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Polecenie Initialize-IoTEdge nie musi przechodzić przez serwer proxy, więc drugi krok wymaga tylko informacji o serwerze proxy dla elementu Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Jeśli masz skomplikowane poświadczenia serwera proxy, którego nie można uwzględnić w adresie URL, użyj `-ProxyCredential` parametru w `-InvokeWebRequestParameters` . Na przykład

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Aby uzyskać więcej informacji na temat parametrów serwera proxy, zobacz [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Aby uzyskać więcej informacji na temat parametrów instalacji systemu Windows, zobacz [skrypty programu PowerShell dla IoT Edge w systemie Windows](reference-windows-scripts.md).

## <a name="configure-iot-edge-and-moby"></a>Konfigurowanie IoT Edge i Moby

IoT Edge opiera się na dwóch demonach uruchomionych na urządzeniu IoT Edge. Demon Moby wykonuje żądania sieci Web w celu ściągania obrazów kontenerów z rejestrów kontenerów. Demon IoT Edge sprawia, że żądania sieci Web komunikują się z IoT Hub.

Zarówno Moby, jak i demon IoT Edge muszą być skonfigurowane do korzystania z serwera proxy na potrzeby ciągłej funkcjonalności urządzenia. Ten krok odbywa się na urządzeniu IoT Edge podczas początkowego konfigurowania urządzenia.

### <a name="moby-daemon"></a>Demon Moby

Ponieważ Moby jest oparty na platformie Docker, zapoznaj się z dokumentacją platformy Docker, aby skonfigurować demona Moby ze zmiennymi środowiskowymi. Większość rejestrów kontenerów (w tym DockerHub i rejestrów kontenerów platformy Azure) obsługuje żądania HTTPS, więc parametr, który należy ustawić, jest **HTTPS_PROXY**. W przypadku ściągania obrazów z rejestru, który nie obsługuje protokołu TLS (Transport Layer Security), należy ustawić parametr **HTTP_PROXY** .

Wybierz artykuł dotyczący systemu operacyjnego urządzenia IoT Edge:

* [Konfigurowanie demona platformy Docker w systemie Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Demon Moby na urządzeniach z systemem Linux utrzymuje nazwę Docker.
* [Konfigurowanie demona platformy Docker w systemie Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Demon Moby na urządzeniach z systemem Windows ma nazwę iotedge-Moby. Nazwy różnią się, ponieważ możliwe jest uruchamianie na urządzeniu z systemem Windows zarówno oprogramowania Docker, jak i Moby.

### <a name="iot-edge-daemon"></a>Demon IoT Edge

Demon IoT Edge jest konfigurowany w podobny sposób do demona Moby. Wykonaj następujące kroki, aby ustawić zmienną środowiskową dla usługi w oparciu o system operacyjny.

Demon IoT Edge zawsze wysyła żądania do IoT Hub przy użyciu protokołu HTTPS.

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Otwórz Edytor w terminalu, aby skonfigurować demona IoT Edge.

```bash
sudo systemctl edit iotedge
```

Wprowadź następujący tekst, zastępując **\<proxy URL>** go adresem serwera proxy i portem. Następnie Zapisz i Zakończ.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Odśwież Menedżera usług, aby pobrać nową konfigurację dla IoT Edge.

```bash
sudo systemctl daemon-reload
```

Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

```bash
sudo systemctl restart iotedge
```

Sprawdź, czy zmienna środowiskowa została utworzona i czy nowa konfiguracja została załadowana.

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Otwórz Edytor w terminalu, aby skonfigurować demona IoT Edge.

```bash
sudo systemctl edit aziot-edged
```

Wprowadź następujący tekst, zastępując **\<proxy URL>** go adresem serwera proxy i portem. Następnie Zapisz i Zakończ.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Począwszy od wersji 1,2, IoT Edge używa usługi tożsamości IoT do obsługi aprowizacji urządzeń za pomocą IoT Hub lub IoT Hub Device Provisioning Service. Otwórz Edytor w terminalu, aby skonfigurować demona usługi IoT Identity.

```bash
sudo systemctl edit aziot-identityd
```

Wprowadź następujący tekst, zastępując **\<proxy URL>** go adresem serwera proxy i portem. Następnie Zapisz i Zakończ.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Odśwież Menedżera usług, aby pobrać nowe konfiguracje.

```bash
sudo systemctl daemon-reload
```

Aby zmiany w obu demonach zaczęły obowiązywać, należy ponownie uruchomić usługi systemu IoT Edge.

```bash
sudo iotedge system restart
```

Sprawdź, czy zostały utworzone zmienne środowiskowe i czy nowa konfiguracja została załadowana.

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows-using-iot-edge-for-linux-on-windows"></a>Windows przy użyciu IoT Edge dla systemu Linux w systemie Windows

Zaloguj się do IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows:

```azurepowershell-interactive
Ssh-EflowVm
```

Wykonaj te same kroki co sekcja systemu Linux, aby skonfigurować demona IoT Edge.

#### <a name="windows-using-windows-containers"></a>Windows przy użyciu kontenerów systemu Windows

Otwórz okno programu PowerShell jako administrator i uruchom następujące polecenie, aby edytować rejestr przy użyciu nowej zmiennej środowiskowej. Zastąp ciąg **\<proxy url>** adresem i portem serwera proxy.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurowanie agenta IoT Edge

Agent IoT Edge to pierwszy moduł do uruchomienia na dowolnym IoT Edge urządzeniu. Jest on uruchamiany po raz pierwszy na podstawie informacji w pliku konfiguracji IoT Edge. Agent IoT Edge następnie łączy się z IoT Hub, aby pobrać manifesty wdrożenia, które deklarują, jakie inne moduły należy wdrożyć na urządzeniu.

Ten krok odbywa się raz na urządzeniu IoT Edge podczas początkowego konfigurowania urządzenia.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Otwórz plik config. YAML na urządzeniu IoT Edge. W systemach Linux ten plik znajduje się w lokalizacji **/etc/iotedge/config.YAML**. W systemach Windows ten plik znajduje się w lokalizacji **C:\ProgramData\iotedge\config.YAML**. Plik konfiguracji jest chroniony, więc musisz mieć uprawnienia administracyjne, aby uzyskać do niego dostęp. W systemach Linux Użyj `sudo` polecenia przed otwarciem pliku w preferowanym edytorze tekstu. W systemie Windows otwórz Edytor tekstu, na przykład Notepad jako administrator, a następnie otwórz plik.

2. W pliku config. YAML Znajdź sekcję **specyfikacji modułu agenta brzegowego** . Definicja agenta IoT Edge zawiera parametr **ENV** , w którym można dodać zmienne środowiskowe.

3. Usuń nawiasy klamrowe, które są symbolami zastępczymi parametru ENV, i Dodaj nową zmienną w nowym wierszu. Należy pamiętać, że wcięcia w YAML to dwie spacje.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Środowisko uruchomieniowe IoT Edge domyślnie używa AMQP, aby komunikować się z IoT Hub. Niektóre serwery proxy blokują porty AMQP. W takim przypadku należy również skonfigurować edgeAgent, aby używać AMQP za pośrednictwem protokołu WebSocket. Dodaj drugą zmienną środowiskową.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![Definicja edgeAgent ze zmiennymi środowiskowymi](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Zapisz zmiany w pliku config. YAML i Zamknij Edytor. Uruchom ponownie IoT Edge, aby zmiany zaczęły obowiązywać.

   * Linux i IoT Edge dla systemu Linux w systemie Windows:

      ```bash
      sudo systemctl restart iotedge
      ```

   * System Windows korzystający z kontenerów systemu Windows:

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Otwórz plik konfiguracji na urządzeniu IoT Edge: `/etc/aziot/config.toml` . Plik konfiguracji jest chroniony, więc musisz mieć uprawnienia administracyjne, aby uzyskać do niego dostęp. W systemach Linux Użyj `sudo` polecenia przed otwarciem pliku w preferowanym edytorze tekstu.

2. W pliku config Znajdź `[agent]` sekcję, która zawiera wszystkie informacje o konfiguracji modułu edgeAgent, które mają być używane podczas uruchamiania. Definicja agenta IoT Edge zawiera `[agent.env]` podsekcję, w której można dodać zmienne środowiskowe.

3. Dodaj parametr **HTTPS_PROXY** do sekcji zmienne środowiskowe i jako wartość Ustaw adres URL serwera proxy.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. Środowisko uruchomieniowe IoT Edge domyślnie używa AMQP, aby komunikować się z IoT Hub. Niektóre serwery proxy blokują porty AMQP. W takim przypadku należy również skonfigurować edgeAgent, aby używać AMQP za pośrednictwem protokołu WebSocket. Usuń komentarz z `UpstreamProtocol` parametru.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. Zapisz zmiany i Zamknij Edytor. Zastosuj Twoje najnowsze zmiany.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>Konfigurowanie manifestów wdrożenia  

Po skonfigurowaniu urządzenia IoT Edge do pracy z serwerem proxy należy nadal zadeklarować zmienną środowiskową HTTPS_PROXY w przyszłych manifestach wdrożenia. Można edytować manifesty wdrożenia za pomocą Kreatora Azure Portal lub edytując plik JSON manifestu wdrożenia.

Należy zawsze skonfigurować dwa moduły środowiska uruchomieniowego, edgeAgent i edgeHub, aby komunikować się za pomocą serwera proxy, aby mogli obsługiwać połączenie z IoT Hub. W przypadku usunięcia informacji o serwerze proxy z modułu edgeAgent jedynym sposobem ponownego nawiązania połączenia jest edytowanie pliku konfiguracji na urządzeniu zgodnie z opisem w poprzedniej sekcji.

Oprócz modułów edgeAgent i edgeHub, inne moduły mogą potrzebować konfiguracji serwera proxy. Moduły, które muszą uzyskać dostęp do zasobów platformy Azure oprócz IoT Hub, takich jak BLOB Storage, muszą mieć zmienną HTTPS_PROXY określoną w pliku manifestu wdrożenia.

Poniższa procedura dotyczy całego okresu istnienia IoT Edge urządzenia.

### <a name="azure-portal"></a>Azure Portal

W przypadku tworzenia wdrożeń dla IoT Edge urządzeń za pomocą kreatora **ustawiania modułów** każdy moduł zawiera sekcję **zmienne środowiskowe** , w której można skonfigurować połączenia z serwerem proxy.

Aby skonfigurować agenta IoT Edge i moduły IoT Edge Hub, wybierz pozycję **Ustawienia środowiska uruchomieniowego** w pierwszym kroku kreatora.

![Skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Dodaj zmienną środowiskową **HTTPS_PROXY** do definicji modułu IoT Edge agent i IoT Edge. Jeśli w pliku konfiguracyjnym na urządzeniu IoT Edge uwzględniona jest zmienna środowiskowa **UpstreamProtocol** , należy dodać ją do definicji modułu IoT Edge agenta.

![Ustaw zmienną środowiskową https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Wszystkie inne moduły dodawane do manifestu wdrożenia są zgodne z tym samym wzorcem.

### <a name="json-deployment-manifest-files"></a>Pliki manifestu wdrożenia JSON

Jeśli tworzysz wdrożenia dla IoT Edge urządzeń przy użyciu szablonów w Visual Studio Code lub ręcznie tworząc pliki JSON, możesz dodać zmienne środowiskowe bezpośrednio do każdej definicji modułu.

Użyj następującego formatu JSON:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Wraz z uwzględnieniem zmiennych środowiskowych definicja modułu powinna wyglądać podobnie do następującego przykładu edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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

Jeśli w pliku config. YAML na urządzeniu IoT Edge została uwzględniona zmienna środowiskowa **UpstreamProtocol** , należy dodać ją do definicji modułu IoT Edge agenta.

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

## <a name="working-with-traffic-inspecting-proxies"></a>Praca ze sprawdzaniem serwerów proxy w ruchu

Jeśli serwer proxy, którego próbujesz użyć, wykonuje inspekcję ruchu na połączeniach zabezpieczonych protokołem TLS, należy pamiętać, że uwierzytelnianie za pomocą certyfikatów X. 509 nie działa. IoT Edge ustanawia zaszyfrowanego kanału TLS z użyciem podanego certyfikatu i klucza. Jeśli ten kanał jest uszkodzony w przypadku inspekcji ruchu, serwer proxy nie może ponownie nawiązać tego kanału z odpowiednimi poświadczeniami, a IoT Hub i usługa IoT Hub Device Provisioning zwróciła `Unauthorized` błąd.

Aby użyć serwera proxy, który przeprowadza inspekcję ruchu, należy użyć uwierzytelniania sygnatury dostępu współdzielonego lub mieć IoT Hub, a usługa IoT Hub Device Provisioning została dodana do listy dozwolonych, aby uniknąć inspekcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rolach [środowiska uruchomieniowego IoT Edge](iot-edge-runtime.md).

Rozwiązywanie problemów z błędami instalacji i konfiguracji wraz z [typowymi problemami i rozwiązaniami dotyczącymi Azure IoT Edge](troubleshoot.md)
