---
title: Rozwiązywanie problemów — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Ten artykuł służy do uczenia się standardowych umiejętności diagnostycznych dla usługi Azure IoT Edge, takich jak pobieranie stanu składnika i dzienników, oraz rozwiązywanie typowych problemów
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
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086525"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Jeśli wystąpią problemy z uruchamianiem usługi Azure IoT Edge w danym środowisku, użyj tego artykułu jako przewodnika ułatwiającego rozwiązywanie problemów.

## <a name="run-the-iotedge-check-command"></a>Uruchom polecenie "check" iotedge

Pierwszym krokiem podczas rozwiązywania problemów z usługą IoT Edge powinno być użycie `check` polecenia, które uruchamia kolekcję testów konfiguracji i łączności w przypadku typowych problemów. Polecenie `check` jest dostępne w [wersji 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) i nowszej.

Można uruchomić `check` polecenie w następujący sposób `--help` lub dołączyć flagę, aby wyświetlić pełną listę opcji:

* W systemie Linux:

  ```bash
  sudo iotedge check
  ```

* W systemie Windows:

  ```powershell
  iotedge check
  ```

Narzędzie do rozwiązywania problemów uruchamia wiele kontroli, które są sortowane w tych trzech kategoriach:

* Sprawdzanie konfiguracji: Sprawdza szczegóły, które mogą uniemożliwić urządzeniom usługi Edge łączenie się z chmurą, w tym problemy z *config.yaml* i aparat kontenera.
* Sprawdzanie połączenia: Weryfikuje środowisko wykonawcze usługi IoT Edge może uzyskać dostęp do portów na urządzeniu-hoście, a wszystkie składniki usługi IoT Edge mogą łączyć się z centrum IoT Hub.
* Sprawdzanie gotowości produkcyjnej: wyszukuje zalecane najlepsze rozwiązania dotyczące produkcji, takie jak stan certyfikatów urzędu certyfikacji urządzenia (CA) i konfiguracja pliku dziennika modułu.

Aby uzyskać informacje o każdej z kontroli diagnostycznych, które uruchamia to narzędzie, w tym o tym, co zrobić w przypadku wystąpienia błędu lub ostrzeżenia, zobacz [Sprawdzanie rozwiązywania problemów z uzębienia aplikacji IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Zbieranie informacji debugowania za pomocą polecenia "support-bundle" iotedge

Gdy trzeba zbierać dzienniki z urządzenia usługi IoT Edge, `support-bundle` najwygodniejszym sposobem jest użycie polecenia. Domyślnie to polecenie zbiera moduł, Menedżera zabezpieczeń usługi IoT Edge i dzienniki aparatu kontenera, dane wyjściowe JSON i inne przydatne informacje debugowania. Kompresuje je do jednego pliku w celu łatwego udostępniania. Polecenie `support-bundle` jest dostępne w [wersji 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) i nowszej.

Uruchom `support-bundle` polecenie z `--since` flagą, aby określić, jak długo od przeszłości chcesz uzyskać dzienniki. Na `6h` przykład otrzyma dzienniki od ostatnich `6d` 6 godzin, od `6m` ostatnich 6 dni, od ostatnich 6 minut i tak dalej. Dołącz `--help` flagę, aby wyświetlić pełną listę opcji.

* W systemie Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* W systemie Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Wyjście z `support-bundle` polecenia może zawierać nazwy hosta, urządzenia i modułu, informacje rejestrowane przez moduły itp. Należy pamiętać o tym, jeśli udostępnianie danych wyjściowych na forum publicznym.

## <a name="standard-diagnostic-steps"></a>Standardowe kroki diagnostyki

Jeśli wystąpi problem, możesz dowiedzieć się więcej o stanie urządzenia usługi IoT Edge, przeglądając dzienniki kontenerów i komunikaty, które przechodzą do i z urządzenia. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan menedżera zabezpieczeń usługi IoT Edge i jego dzienników

W systemie Linux:

* Aby wyświetlić stan Menedżera zabezpieczeń usług IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Aby wyświetlić dzienniki Menedżera zabezpieczeń usługi IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Aby wyświetlić bardziej szczegółowe dzienniki menedżera zabezpieczeń usługi IoT Edge:

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

* Aby wyświetlić stan Menedżera zabezpieczeń usług IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Aby wyświetlić dzienniki Menedżera zabezpieczeń usługi IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Jeśli menedżer zabezpieczeń usługi IoT Edge nie jest uruchomiony, sprawdź plik konfiguracyjny yaml

> [!WARNING]
> Pliki YAML nie mogą zawierać kart jako wcięcie. Zamiast tego użyj 2 spacji. Elementy najwyższego poziomu nie powinny mieć spacji wiodących.

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenerów pod kątem problemów

Po uruchomieniu demona zabezpieczeń usługi IoT Edge, spójrz na dzienniki kontenerów, aby wykryć problemy. Rozpocznij od wdrożonych kontenerów, a następnie spójrz na kontenery, które tworzą środowisko uruchomieniowe usługi IoT Edge: edgeAgent i edgeHub. Dzienniki agenta usługi IoT Edge zazwyczaj zawierają informacje o cyklu życia każdego kontenera. Dzienniki usługi IoT Edge hub zawierają informacje o wiadomościach i routingu.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Wyświetlanie komunikatów przechodzących przez centrum usługi IoT Edge

Można wyświetlić komunikaty przechodzące przez centrum usługi IoT Edge i zbierać szczegółowe informacje z pełnych dzienników z kontenerów środowiska wykonawczego. Aby włączyć pełne dzienniki w tych kontenerach, ustaw `RuntimeLogLevel` w pliku konfiguracji yaml. Aby otworzyć plik:

W systemie Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

W systemie Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Domyślnie `agent` element będzie wyglądać następująco:

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
   > Pliki YAML nie mogą zawierać kart jako identyfikatora. Zamiast tego użyj 2 spacji. Elementy najwyższego poziomu nie mogą mieć interwózów.

Zapisz plik i uruchom ponownie menedżera zabezpieczeń IoT Edge.

Możesz także sprawdzić komunikaty przesyłane między usługą IoT Hub i urządzeniami usługi IoT Edge. Wyświetl te komunikaty przy użyciu [rozszerzenia Usługi Azure IoT Hub dla programu Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) Aby uzyskać więcej informacji, zobacz [Przydatne narzędzie podczas tworzenia za pomocą usługi Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Ponowne uruchamianie kontenerów

Po zbadaniu dzienników i komunikatów w celu uzyskania informacji, można spróbować ponownego uruchomienia kontenerów:

```cmd
iotedge restart <container name>
```

Uruchom ponownie kontenery środowiska uruchomieniowego IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Ponowne uruchamianie menedżera zabezpieczeń ioT Edge

Jeśli problem nadal występuje, możesz spróbować ponownie uruchomić menedżera zabezpieczeń IoT Edge.

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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge zatrzymuje się po około minucie

Moduł edgeAgent uruchamia się i działa pomyślnie przez około minutę, a następnie zatrzymuje się. Dzienniki wskazują, że agent usługi IoT Edge próbuje połączyć się z Centrum IoT za pośrednictwem usługi AMQP, a następnie próbuje połączyć się za pomocą usługi AMQP za pośrednictwem websocket. Gdy to się nie powiedzie, agent IoT Edge kończy działanie.

Przykładowy nabrzeżnylogi:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Główna przyczyna**

Konfiguracja sieci w sieci hosta uniemożliwia agentowi usługi IoT Edge dotarcie do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). Jeśli połączenie nie powiedzie się, próbuje WebSockets (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT.

**Rozdzielczość**

Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Nie można uruchomić koncentratora usługi IoT Edge

Uruchomienie modułu edgeHub nie powiedzie się i drukuje następujący komunikat w dziennikach:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Główna przyczyna**

Jakiś inny proces na komputerze hosta korzysta z portu 443. Koncentrator usługi IoT Edge mapuje porty 5671 i 443 do użycia w scenariuszach bramy. To mapowanie portów kończy się niepowodzeniem, jeśli inny proces jest już powiązany z portem 443.

**Rozdzielczość**

Znajdź i zatrzymaj proces używający portu 443. Ten proces to zwykle serwer internetowy.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)

Kontener nie może uruchomić, a dzienniki programu edgeAgent pokazują błąd 403.

**Główna przyczyna**

Agent IoT Edge nie ma uprawnień dostępu do obrazu modułu.

**Rozdzielczość**

Upewnij się, że poświadczenia rejestru są poprawnie określone w manifeście wdrażania

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Demon zabezpieczeń IoT Edge kończy się niepowodzeniem z nieprawidłową nazwa hosta

Polecenie `sudo journalctl -u iotedge` kończy się niepowodzeniem i drukuje następujący komunikat:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Główna przyczyna**

Środowisko uruchomieniowe IoT Edge może obsługiwać tylko nazwy hostów, które są krótsze niż 64 znaki. Komputery fizyczne zwykle nie mają długich nazwy hostów, ale problem jest bardziej powszechny na maszynie wirtualnej. Automatycznie generowane nazwy hostów dla maszyn wirtualnych systemu Windows hostowanych na platformie Azure, w szczególności, wydają się być długie.

**Rozdzielczość**

Po wyświetleniu tego błędu można go rozwiązać, konfigurując nazwę DNS maszyny wirtualnej, a następnie ustawiając nazwę DNS jako nazwę hosta w poleceniu konfiguracji.

1. W witrynie Azure portal przejdź do strony przeglądu maszyny wirtualnej.
2. Wybierz **pozycję Konfiguruj** pod nazwą DNS. Jeśli maszyna wirtualna ma już skonfigurowaną nazwę DNS, nie trzeba konfigurować nowej.

   ![Konfigurowanie nazwy dns maszyny wirtualnej](./media/troubleshoot/configure-dns.png)

3. Podaj wartość **etykiety nazwy DNS** i wybierz pozycję **Zapisz**.
4. Skopiuj nową nazwę DNS, która powinna być w formacie ** \<\>DNSnamelabel .\< vmlocation\>.cloudapp.azure.com**.
5. Wewnątrz maszyny wirtualnej użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe usługi IoT Edge o nazwie DNS:

   * W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemy ze stabilnością urządzeń z ograniczeniami zasobów

Problemy ze stabilnością mogą wystąpić na ograniczonych urządzeniach, takich jak Raspberry Pi, zwłaszcza gdy są używane jako brama. Symptomy obejmują wyjątki poza pamięcią w module koncentratora krawędzi, urządzenia podrzędne nie mogą się połączyć lub urządzenie przestaje wysyłać komunikaty telemetryczne po kilku godzinach.

**Główna przyczyna**

Centrum usługi IoT Edge, które jest częścią środowiska wykonawczego usługi IoT Edge, jest domyślnie zoptymalizowane pod kątem wydajności i próbuje przydzielić duże fragmenty pamięci. Ta optymalizacja nie jest idealna dla urządzeń o ograniczonej krawędzi i może powodować problemy ze stabilnością.

**Rozdzielczość**

W przypadku centrum Usługi IoT Edge ustaw zmienną środowiskową **OptimizeForPerformance** na **false**. Istnieją dwa sposoby ustawiania zmiennych środowiskowych:

W witrynie Azure Portal:

W centrum IoT Hub wybierz urządzenie Usługi IoT Edge oraz stronę szczegółów urządzenia i wybierz pozycję Ustaw**ustawienia środowiska wykonawczego** **modułów** > . Utwórz zmienną środowiskową dla modułu Koncentratora krawędzi o nazwie *OptimizeForPerformance,* która jest ustawiona na *false*.

![OptimizeForPerformance ustawiona na false](./media/troubleshoot/optimizeforperformance-false.png)

**Lub**

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nie można uzyskać dzienników demona IoT Edge w systemie Windows

Jeśli podczas korzystania `Get-WinEvent` z systemu Windows zostanie świadom eventlogekceptacji, sprawdź wpisy rejestru.

**Główna przyczyna**

Polecenie `Get-WinEvent` programu PowerShell opiera się na wpisie rejestru, `ProviderName`który ma być obecny w celu znalezienia dzienników przez określony plik .

**Rozdzielczość**

Ustaw wpis rejestru dla demona IoT Edge. Utwórz plik **iotedge.reg** z następującą zawartością i zaimportuj go `reg import iotedge.reg` do rejestru systemu Windows, klikając go dwukrotnie lub używając polecenia:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Moduł IoT Edge nie może wysłać wiadomości do edgeHub z błędem 404

Niestandardowy moduł usługi IoT Edge nie może wysłać wiadomości `Module not found` do edgeHub z błędem 404. Demon IoT Edge drukuje następujący komunikat do dzienników:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Główna przyczyna**

Demon usługi IoT Edge wymusza identyfikację procesu dla wszystkich modułów łączących się z edgeHub ze względów bezpieczeństwa. Sprawdza, czy wszystkie wiadomości wysyłane przez moduł pochodzą z głównego identyfikatora procesu modułu. Jeśli wiadomość jest wysyłana przez moduł z identyfikatora procesu innego niż początkowo ustalone, odrzuci wiadomość z komunikatem o błędzie 404.

**Rozdzielczość**

Od wersji 1.0.7 wszystkie procesy modułów są autoryzowane do łączenia. Jeśli uaktualnienie do standardu 1.0.7 nie jest możliwe, wykonaj następujące kroki. Aby uzyskać więcej informacji, zobacz [dziennik zmian wersji 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez niestandardowy moduł usługi IoT Edge do wysyłania wiadomości do edgeHub. Na przykład upewnij `ENTRYPOINT` się, `CMD` że zamiast polecenia w `CMD` pliku platformy Docker, ponieważ doprowadzi do jednego identyfikatora procesu dla modułu `ENTRYPOINT` i innego identyfikatora procesu dla polecenia bash z uruchomionym głównym programem, podczas gdy doprowadzi do pojedynczego identyfikatora procesu.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Reguły konfiguracji zapory i portu dla wdrożenia usługi IoT Edge

Usługa Azure IoT Edge umożliwia komunikację z serwera lokalnego do chmury platformy Azure przy użyciu obsługiwanych protokołów Usługi IoT Hub, zobacz [wybieranie protokołu komunikacyjnego.](../iot-hub/iot-hub-devguide-protocols.md) Aby zwiększyć bezpieczeństwo, kanały komunikacji między usługą Azure IoT Edge i usługi Azure IoT Hub są zawsze skonfigurowane do ruchu wychodzącego. Ta konfiguracja jest oparta na [wzorcu komunikacji wspomaganej usług,](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)który minimalizuje powierzchnię ataku dla złośliwej jednostki do zbadania. Komunikacja przychodząca jest wymagana tylko w określonych scenariuszach, w których usługa Azure IoT Hub musi wypychać wiadomości na urządzenie usługi Azure IoT Edge. Komunikaty z chmury do urządzenia są chronione za pomocą bezpiecznych kanałów TLS i mogą być dodatkowo zabezpieczone za pomocą certyfikatów X.509 i modułów urządzeń TPM. Usługa Azure IoT Edge Security Manager reguluje sposób ustanawiania tej komunikacji, zobacz [Menedżer zabezpieczeń usługi IoT Edge](../iot-edge/iot-edge-security-manager.md).

Usługa IoT Edge zapewnia ulepszoną konfigurację do zabezpieczania środowiska wykonawczego usługi Azure IoT Edge i wdrożonych modułów, ale nadal jest zależna od podstawowej konfiguracji komputera i sieci. W związku z tym konieczne jest, aby zapewnić prawidłowe reguły sieci i zapory są skonfigurowane dla bezpiecznej komunikacji od krawędzi do chmury. Poniższa tabela może służyć jako wytyczne, gdy reguły zapory konfiguracji dla serwerów źródłowych, na których hostowane jest środowisko uruchomieniowe usługi Azure IoT Edge:

|Protocol (Protokół)|Port|Dane|Wychodzących|Wskazówki|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (domyślnie)|ZABLOKOWANE (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) jako otwarte, gdy używasz protokołu MQTT jako protokołu komunikacyjnego.<li>1883 dla MQTT nie jest obsługiwany przez IoT Edge. <li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul>|
|AMQP|5671|ZABLOKOWANE (domyślnie)|OTWÓRZ (domyślnie)|<ul> <li>Domyślny protokół komunikacyjny dla usługi IoT Edge. <li> Należy skonfigurować jako Otwarty, jeśli usługa Azure IoT Edge nie jest skonfigurowana dla innych obsługiwanych protokołów lub protokół AMQP jest żądanym protokołem komunikacyjnym.<li>5672 dla AMQP nie jest obsługiwany przez IoT Edge.<li>Zablokuj ten port, gdy usługa Azure IoT Edge używa innego obsługiwanego protokołu usługi IoT Hub.<li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (domyślnie)|OTWÓRZ (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) jako otwarte w 443 dla inicjowania obsługi administracyjnej ioT Edge. Ta konfiguracja jest wymagana podczas korzystania ze skryptów ręcznych lub usługi inicjowania obsługi administracyjnej urządzeń azure (DPS). <li>Połączenie przychodzące (przychodzące) powinno być otwarte tylko dla określonych scenariuszy: <ul> <li>  Jeśli masz przezroczystą bramę z urządzeniami typu liść, które mogą wysyłać żądania metod. W takim przypadku port 443 nie musi być otwarty dla sieci zewnętrznych, aby połączyć się z usługą IoTHub lub zapewnić usługi IoTHub za pośrednictwem usługi Azure IoT Edge. W związku z tym reguła przychodząca może być ograniczona tylko do otwierania przychodzącego (przychodzącego) z sieci wewnętrznej. <li> W przypadku scenariuszy Z klientem do urządzenia (C2D).</ul><li>80 dla PROTOKOŁU HTTP nie jest obsługiwany przez ioT Edge.<li>Jeśli protokołów innych niż HTTP (na przykład protokołu AMQP lub MQTT) nie można skonfigurować w przedsiębiorstwie; wiadomości mogą być wysyłane przez WebSockets. Port 443 będzie używany do komunikacji WebSocket w takim przypadku.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Moduł Agenta Brzegowego stale raportuje "pusty plik konfiguracyjny" i nie uruchamia się żadnych modułów na urządzeniu

Urządzenie ma problemy z uruchamianiem modułów zdefiniowanych we wdrożeniu. Działa tylko agent edgeAgent, ale ciągle zgłasza 'pusty plik konfiguracyjny...'.

**Główna przyczyna**

Domyślnie usługa IoT Edge uruchamia moduły we własnej sieci kontenerów izolowanych. Urządzenie może mieć problemy z rozpoznawaniem nazw DNS w tej sieci prywatnej.

**Rozdzielczość**

**Opcja 1: Ustawianie serwera DNS w ustawieniach aparatu kontenera**

Określ serwer DNS dla środowiska w ustawieniach aparatu kontenera, który będzie stosowany do wszystkich modułów kontenerów uruchomionych przez aparat. Utwórz plik `daemon.json` o nazwie określający serwer DNS, który ma być używany. Przykład:

```json
{
    "dns": ["1.1.1.1"]
}
```

Powyższy przykład ustawia serwer DNS na publicznie dostępną usługę DNS. Jeśli urządzenie brzegowe nie może uzyskać dostępu do tego adresu IP ze swojego środowiska, zastąp go adresem serwera DNS, który jest dostępny.

Umieść `daemon.json` w odpowiednim miejscu dla swojej platformy:

| Platforma | Lokalizacja |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host systemu Windows z kontenerami systemu Windows | `C:\ProgramData\iotedge-moby\config` |

Jeśli lokalizacja zawiera `daemon.json` już plik, dodaj do niego klucz **dns** i zapisz plik.

Uruchom ponownie aparat kontenera, aby aktualizacje zostały zastosowane.

| Platforma | Polecenie |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Administrator Powershell) | `Restart-Service iotedge-moby -Force` |

**Opcja 2: Ustawianie serwera DNS we wdrożeniu usługi IoT Edge na moduł**

Serwer DNS można ustawić dla każdego modułu *createOptions* we wdrożeniu usługi IoT Edge. Przykład:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Należy ustawić tę konfigurację dla *edgeAgent* i *edgeHub* modułów, jak również.

## <a name="next-steps"></a>Następne kroki

Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem,](https://github.com/Azure/iotedge/issues) abyśmy mogli nadal ulepszać.

Jeśli masz więcej pytań, utwórz [prośbę o pomoc do pomocy.](https://portal.azure.com/#create/Microsoft.Support)
