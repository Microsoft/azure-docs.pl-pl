---
title: Rozwiązywanie problemów — Azure IoT Edge | Microsoft Docs
description: Skorzystaj z tego artykułu, aby poznać standardowe umiejętności diagnostyki Azure IoT Edge, takie jak pobieranie stanu składników i dzienników oraz rozwiązywanie typowych problemów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2e15dffac73b4a50b1ef9288feaeb6073dea91e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086525"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów.

## <a name="run-the-iotedge-check-command"></a>Uruchom polecenie "Check" iotedge "

Pierwszy krok podczas rozwiązywania problemów IoT Edge powinien używać `check` polecenia, które uruchamia kolekcję testów konfiguracji i łączności dla typowych problemów. `check` Polecenie jest dostępne w [wersji 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) lub nowszej.

Można uruchomić `check` polecenie w następujący sposób lub dodać `--help` flagę, aby wyświetlić pełną listę opcji:

* W systemie Linux:

  ```bash
  sudo iotedge check
  ```

* W systemie Windows:

  ```powershell
  iotedge check
  ```

Narzędzie do rozwiązywania problemów uruchamia wiele sprawdzeń, które są sortowane w tych trzech kategoriach:

* Sprawdzanie konfiguracji: sprawdza szczegóły, które mogą uniemożliwić nawiązywanie połączenia z chmurą przez urządzenia brzegowe, w tym problemy z *konfiguracją. YAML* i aparatem kontenerów.
* Sprawdzanie połączeń: sprawdza, czy środowisko uruchomieniowe IoT Edge może uzyskać dostęp do portów na urządzeniu hosta, a wszystkie składniki IoT Edge mogą łączyć się z IoT Hub.
* Kontrole gotowości produkcyjnej: szuka zalecanych najlepszych rozwiązań w zakresie produkcji, takich jak stan certyfikatów urzędu certyfikacji urządzeń i konfiguracji pliku dziennika modułu.

Aby uzyskać informacje o każdym z testów diagnostycznych wykonywanych przez to narzędzie, w tym o tym, co należy zrobić, jeśli wystąpi błąd lub ostrzeżenie, zobacz [IoT Edge Rozwiązywanie problemów z testami](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Zbierz informacje debugowania za pomocą polecenia "support-Binding" iotedge

Gdy zachodzi potrzeba zebrania dzienników z urządzenia IoT Edge, Najwygodniejszym sposobem jest użycie `support-bundle` polecenia. Domyślnie to polecenie zbiera moduły, IoT Edge Security Manager i dzienników aparatu kontenerów, dane wyjściowe JSON "iotedge Check" i inne przydatne informacje debugowania. Kompresuje je do jednego pliku, aby można było je łatwo udostępnić. `support-bundle` Polecenie jest dostępne w [wersji 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) lub nowszej.

Uruchom `support-bundle` polecenie z flagą `--since` , aby określić, jak długo od dawna chcesz pobrać dzienniki. Na przykład `6h` otrzymasz dzienniki od ostatnich 6 godzin `6d` od ostatniego 6 dni `6m` od ostatniego 6 minut i tak dalej. Dołącz `--help` flagę, aby wyświetlić pełną listę opcji.

* W systemie Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* W systemie Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Dane wyjściowe `support-bundle` polecenia mogą zawierać nazwy hosta, urządzenia i modułu, informacje zarejestrowane przez moduły itp. Pamiętaj o tym, jeśli udostępnisz dane wyjściowe na forum publicznym.

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki

Jeśli wystąpi problem, możesz dowiedzieć się więcej o stanie urządzenia IoT Edge, przeglądając dzienniki kontenerów i komunikaty przekazywane do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan programu IoT Edge Security Manager i jego dzienników

W systemie Linux:

* Aby wyświetlić stan programu IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Aby wyświetlić dzienniki programu IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Aby wyświetlić bardziej szczegółowe dzienniki programu IoT Edge Security Manager:

  * Edytuj ustawienia demona iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Zaktualizuj następujące wiersze:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Uruchom ponownie demona zabezpieczeń IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

W systemie Windows:

* Aby wyświetlić stan programu IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Aby wyświetlić dzienniki programu IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Jeśli Menedżer zabezpieczeń IoT Edge nie jest uruchomiony, zweryfikuj plik konfiguracji YAML

> [!WARNING]
> Pliki YAML nie mogą zawierać tabulatorów jako wcięcia. Zamiast tego należy użyć dwóch spacji. Elementy najwyższego poziomu nie powinny zawierać spacji wiodących.

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenerów pod kątem problemów

Po uruchomieniu demona zabezpieczeń IoT Edge należy zapoznać się z dziennikami kontenerów w celu wykrycia problemów. Zacznij od wdrożonych kontenerów, a następnie Przyjrzyj się kontenerom, które składają się na IoT Edge środowisko uruchomieniowe: edgeAgent i edgeHub. Dzienniki agenta IoT Edge zwykle zawierają informacje dotyczące cyklu życia każdego kontenera. Dzienniki Centrum IoT Edge zawierają informacje dotyczące komunikatów i routingu.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Wyświetlanie komunikatów przechodzących przez Centrum IoT Edge

Możesz wyświetlić komunikaty przechodzące przez Centrum IoT Edge i zebrać informacje z pełnych dzienników z kontenerów środowiska uruchomieniowego. Aby włączyć pełne dzienniki w tych kontenerach, ustaw `RuntimeLogLevel` w pliku konfiguracji YAML. Aby otworzyć plik:

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Domyślnie `agent` element będzie wyglądać podobnie do poniższego przykładu:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Zamień `env: {}` na:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Pliki YAML nie mogą zawierać kart jako identation. Zamiast tego należy użyć dwóch spacji. Elementy najwyższego poziomu nie mogą zawierać odstępów wiodących.

Zapisz plik i ponownie uruchom Menedżera zabezpieczeń IoT Edge.

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Te komunikaty są wyświetlane przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Aby uzyskać więcej informacji, zobacz [przydatne narzędzie podczas opracowywania przy użyciu usługi Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Uruchom ponownie kontenery

Po zbadaniu dzienników i komunikatów w celu uzyskania informacji możesz spróbować ponownie uruchomić kontenery:

```cmd
iotedge restart <container name>
```

Uruchom ponownie kontenery środowiska uruchomieniowego IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Uruchom ponownie usługę IoT Edge Security Manager

Jeśli problem nadal występuje, możesz spróbować ponownie uruchomić Menedżera zabezpieczeń IoT Edge.

W systemie Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

W systemie Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge zatrzymał się po około minucie

Moduł edgeAgent jest uruchamiany i uruchamiany pomyślnie przez około minutę, a następnie zostaje zatrzymany. Dzienniki wskazują, że Agent IoT Edge próbuje nawiązać połączenie z IoT Hub za pośrednictwem AMQP, a następnie próbuje nawiązać połączenie przy użyciu AMQP za pośrednictwem protokołu WebSocket. Gdy to się nie powiedzie, Agent IoT Edge kończy pracę.

Przykładowe dzienniki edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Główna przyczyna**

Konfiguracja sieci w sieci hosta uniemożliwia agentowi IoT Edge uzyskiwanie dostępu do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). Jeśli połączenie nie powiedzie się, zostanie podjęta próba nawiązania połączenia z usługą WebSockets (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT.

**Rozwiązanie**

Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Nie można uruchomić Centrum IoT Edge

Nie można uruchomić modułu edgeHub i do dzienników zostanie wyświetlony następujący komunikat:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Główna przyczyna**

Jakiś inny proces na komputerze hosta korzysta z portu 443. IoT Edge Hub mapuje porty 5671 i 443 do użycia w scenariuszach bramy. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443.

**Rozwiązanie**

Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)

Nie można uruchomić kontenera, a w dziennikach edgeAgent jest wyświetlany błąd 403.

**Główna przyczyna**

Agent IoT Edge nie ma uprawnień dostępu do obrazu modułu.

**Rozwiązanie**

Upewnij się, że poświadczenia rejestru są poprawnie określone w manifeście wdrożenia

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge demona zabezpieczeń kończy się niepowodzeniem z nieprawidłową nazwą hosta

Polecenie `sudo journalctl -u iotedge` kończy się niepowodzeniem i drukuje następujący komunikat:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Główna przyczyna**

Środowisko uruchomieniowe IoT Edge może obsługiwać tylko nazwy hostów krótsze niż 64 znaków. Maszyny fizyczne zazwyczaj nie mają długich nazw hostów, ale problem jest bardziej powszechny w maszynie wirtualnej. W szczególności automatycznie generowane nazwy hostów dla maszyn wirtualnych z systemem Windows hostowanych na platformie Azure.

**Rozwiązanie**

Gdy ten błąd jest wyświetlany, można go rozwiązać przez skonfigurowanie nazwy DNS maszyny wirtualnej, a następnie ustawienie nazwy DNS jako nazwy hosta w poleceniu instalacji.

1. W Azure Portal przejdź do strony Przegląd swojej maszyny wirtualnej.
2. Wybierz pozycję **Konfiguruj** w obszarze Nazwa DNS. Jeśli maszyna wirtualna ma już skonfigurowaną nazwę DNS, nie trzeba konfigurować nowej.

   ![Skonfiguruj nazwę DNS maszyny wirtualnej](./media/troubleshoot/configure-dns.png)

3. Podaj wartość w obszarze **etykieta nazwy DNS** i wybierz pozycję **Zapisz**.
4. Skopiuj nową nazwę DNS, która powinna mieć format ** \<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com**.
5. W ramach maszyny wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe IoT Edge przy użyciu nazwy DNS:

   * W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemy ze stabilnością dotyczące urządzeń z ograniczeniami zasobów

Problemy ze stabilnością mogą wystąpić na ograniczonych urządzeniach, takich jak Raspberry Pi, zwłaszcza w przypadku korzystania z bramy. Objawy obejmują wyjątki braku pamięci w module centrum brzegowego, urządzenia podrzędne nie mogą się łączyć lub urządzenie nie może wysyłać komunikatów telemetrycznych po kilku godzinach.

**Główna przyczyna**

Centrum IoT Edge, które jest częścią środowiska uruchomieniowego IoT Edge, jest zoptymalizowane pod kątem wydajności domyślnie i próbuje przydzielić duże fragmenty pamięci. Ta optymalizacja nie jest idealna dla urządzeń z ograniczonymi granicami i może spowodować problemy ze stabilnością.

**Rozwiązanie**

W przypadku Centrum IoT Edge Ustaw dla zmiennej środowiskowej **OptimizeForPerformance** **wartość false**. Istnieją dwa sposoby ustawiania zmiennych środowiskowych:

W witrynie Azure Portal:

W IoT Hub wybierz urządzenie IoT Edge i ze strony Szczegóły urządzenia, a następnie wybierz pozycję **Ustaw** > **Ustawienia środowiska uruchomieniowego**moduły. Utwórz zmienną środowiskową dla modułu centrum brzegowego o nazwie *OptimizeForPerformance* , która ma wartość *false*.

![OptimizeForPerformance ustawiona na wartość false](./media/troubleshoot/optimizeforperformance-false.png)

**ORAZ**

W manifeście wdrożenia:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nie można pobrać dzienników demona IoT Edge w systemie Windows

Jeśli podczas korzystania `Get-WinEvent` z systemu Windows zostanie wyświetlony komunikat EventLogException, sprawdź wpisy rejestru.

**Główna przyczyna**

Polecenie `Get-WinEvent` programu PowerShell polega na wpisie rejestru, który ma być obecny w celu znalezienia dzienników według `ProviderName`określonego.

**Rozwiązanie**

Ustaw wpis rejestru dla demona IoT Edge. Utwórz plik **iotedge. reg** o następującej zawartości i zaimportuj go do rejestru systemu Windows, klikając go dwukrotnie lub korzystając z `reg import iotedge.reg` polecenia:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Moduł IoT Edge nie może wysłać komunikatu do edgeHub z błędem 404

Niestandardowa moduł IoT Edge nie może wysłać komunikatu do edgeHub z błędem 404 `Module not found` . Demon IoT Edge przedrukuje następujący komunikat do dzienników:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Główna przyczyna**

Demon IoT Edge wymusza identyfikację procesu dla wszystkich modułów łączących się z edgeHub ze względów bezpieczeństwa. Sprawdza, czy wszystkie komunikaty wysyłane przez moduł pochodzą z głównego identyfikatora procesu modułu. Jeśli komunikat jest wysyłany przez moduł z innego procesu niż pierwotnie ustanowiony, odrzuci komunikat z komunikatem o błędzie 404.

**Rozwiązanie**

W przypadku wersji 1.0.7 wszystkie procesy modułów są autoryzowane do nawiązania połączenia. Jeśli uaktualnienie do 1.0.7 nie jest możliwe, wykonaj następujące czynności. Aby uzyskać więcej informacji, zobacz [Dziennik zmian wersji 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez moduł IoT Edge niestandardowego do wysyłania komunikatów do edgeHub. Na przykład upewnij się, że `ENTRYPOINT` zamiast `CMD` polecenia w pliku platformy Docker jest używane polecenie `CMD` , ponieważ spowoduje to przetworzenie identyfikatora dla modułu i innego identyfikatora procesu dla polecenia bash, w którym działa główny program `ENTRYPOINT` , a następnie będzie prowadzić do jednego identyfikatora procesu.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Reguły konfiguracji zapory i portów dla wdrożenia IoT Edge

Azure IoT Edge umożliwia komunikację między serwerem lokalnym a chmurą platformy Azure przy użyciu obsługiwanych protokołów IoT Hub, zobacz [Wybieranie protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md). W celu zwiększenia bezpieczeństwa kanały komunikacyjne między Azure IoT Edge i IoT Hub platformy Azure są zawsze skonfigurowane do wychodzącego. Ta konfiguracja jest oparta na [wzorcu komunikacji z pomocą techniczną](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), która minimalizuje podatność na ataki dla złośliwej jednostki. Komunikacja przychodząca jest wymagana tylko dla konkretnych scenariuszy, w których usługa Azure IoT Hub musi przekazywać komunikaty do urządzenia Azure IoT Edge. Komunikaty z chmury do urządzeń są chronione za pomocą bezpiecznych kanałów TLS i można je zabezpieczyć za pomocą certyfikatów X. 509 oraz modułów urządzeń TPM. Azure IoT Edge Security Manager decyduje o tym, jak można nawiązać tę komunikację, zobacz [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Chociaż IoT Edge zapewnia rozszerzoną konfigurację do zabezpieczania Azure IoT Edge środowiska uruchomieniowego i wdrożonych modułów, nadal zależy od konfiguracji komputera i sieci. Z tego względu należy upewnić się, że skonfigurowano odpowiednie reguły sieci i zapory na potrzeby bezpiecznej krawędzi komunikacji z chmurą. Poniższa tabela może służyć jako wytyczna, gdy reguły zapory konfiguracyjnej dla serwerów źródłowych, na których jest hostowana Azure IoT Edge środowisko uruchomieniowe:

|Protocol (Protokół)|Port|Dane|Przeznaczony|Wskazówki|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (domyślnie)|ZABLOKOWANE (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) do otwarcia w przypadku używania MQTT jako protokołu komunikacyjnego.<li>1883 dla MQTT nie jest obsługiwane przez IoT Edge. <li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul>|
|AMQP|5671|ZABLOKOWANE (domyślnie)|Otwórz (domyślnie)|<ul> <li>Domyślny protokół komunikacyjny dla IoT Edge. <li> Musi być skonfigurowany do otwierania, jeśli Azure IoT Edge nie jest skonfigurowana dla innych obsługiwanych protokołów lub AMQP jest żądanym protokołem komunikacyjnym.<li>5672 dla AMQP nie jest obsługiwane przez IoT Edge.<li>Blokuj ten port, gdy Azure IoT Edge używa innego obsługiwanego IoT Hub protokołu.<li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (domyślnie)|Otwórz (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) do otwarcia w dniu 443 dla IoT Edge aprowizacji. Ta konfiguracja jest wymagana w przypadku używania skryptów ręcznych lub usługi Azure IoT Device Provisioning Service (DPS). <li>Połączenie przychodzące (przychodzące) powinno być otwarte tylko dla konkretnych scenariuszy: <ul> <li>  Jeśli masz niejawną bramę z urządzeniami typu liść, które mogą wysyłać żądania metod. W takim przypadku port 443 nie musi być otwarty dla sieci zewnętrznych, aby połączyć się z usługą IoTHub lub udostępnić usługi IoTHub za poorednictwem Azure IoT Edge. W ten sposób Reguła przychodząca może być ograniczona tylko do otwarcia przychodzącego (przychodzącego) z sieci wewnętrznej. <li> W przypadku scenariuszy klient-urządzenie (C2D).</ul><li>80 dla protokołu HTTP nie jest obsługiwane przez IoT Edge.<li>Jeśli nie można skonfigurować protokołów innych niż HTTP (na przykład AMQP lub MQTT) w przedsiębiorstwie; komunikaty można wysyłać za pośrednictwem obiektów WebSockets. Port 443 będzie używany na potrzeby komunikacji z użyciem protokołu WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Moduł agenta programu Edge ciągle raportuje pusty plik konfiguracji i nie uruchamia żadnych modułów na urządzeniu.

Urządzenie ma problemy z uruchamianiem modułów zdefiniowanych we wdrożeniu. Tylko edgeAgent jest uruchomiona, ale ciągle zgłasza "pusty plik konfiguracji"... ".

**Główna przyczyna**

Domyślnie IoT Edge uruchamia moduły w swojej własnej izolowanej sieci kontenerów. Urządzenie może mieć problemy z rozpoznawaniem nazw DNS w ramach tej sieci prywatnej.

**Rozwiązanie**

**Opcja 1. Ustawianie serwera DNS w ustawieniach aparatu kontenera**

Określ serwer DNS dla środowiska w ustawieniach aparatu kontenera, które będą stosowane do wszystkich modułów kontenera uruchomionych przez aparat. Utwórz plik o nazwie `daemon.json` OKREŚLAJĄC serwer DNS, który ma być używany. Przykład:

```json
{
    "dns": ["1.1.1.1"]
}
```

W powyższym przykładzie serwer DNS jest ustawiany publicznie dostępną usługę DNS. Jeśli urządzenie brzegowe nie może uzyskać dostępu do tego adresu IP ze środowiska, zastąp go adresem serwera DNS, który jest dostępny.

Umieść `daemon.json` w odpowiedniej lokalizacji dla Twojej platformy:

| Platforma | Lokalizacja |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host z systemem Windows z kontenerami systemu Windows | `C:\ProgramData\iotedge-moby\config` |

Jeśli lokalizacja zawiera `daemon.json` już plik, Dodaj do niej klucz **DNS** i Zapisz plik.

Aby aktualizacje zaczęły obowiązywać, uruchom ponownie aparat kontenerów.

| Platforma | Polecenie |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (administracyjny program PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opcja 2: Ustaw serwer DNS w IoT Edge wdrożenia na moduł**

Można ustawić serwer DNS dla *opcji* "wszystkie" modułu we wdrożeniu IoT Edge. Przykład:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Należy pamiętać, aby skonfigurować również tę konfigurację dla modułów *edgeAgent* i *edgeHub* .

## <a name="next-steps"></a>Następne kroki

Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues) , aby umożliwić kontynuowanie ulepszania.

Jeśli masz więcej pytań, Utwórz [support Request](https://portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.
