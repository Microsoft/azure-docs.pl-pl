---
title: Typowe błędy — Azure IoT Edge | Microsoft Docs
description: Skorzystaj z tego artykułu, aby rozwiązać typowe problemy występujące podczas wdrażania rozwiązania IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e1605f45dc8a7a1c03b5481ea17478064414df59
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382212"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Typowe problemy z usługą Azure IoT Edge i ich rozwiązania

Skorzystaj z tego artykułu, aby znaleźć procedurę rozwiązywania typowych problemów, które mogą wystąpić podczas wdrażania rozwiązań IoT Edge. Jeśli chcesz dowiedzieć się, jak znaleźć dzienniki i błędy na urządzeniu IoT Edge, zobacz [Rozwiązywanie problemów z urządzeniem IoT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge zatrzymał się po około minucie

**Obserwowane zachowanie:**

Moduł edgeAgent jest uruchamiany i uruchamiany pomyślnie przez około minutę, a następnie zostaje zatrzymany. Dzienniki wskazują, że Agent IoT Edge próbuje nawiązać połączenie z IoT Hub za pośrednictwem AMQP, a następnie próbuje nawiązać połączenie przy użyciu AMQP za pośrednictwem protokołu WebSocket. Gdy to się nie powiedzie, Agent IoT Edge kończy pracę.

Przykładowe dzienniki edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Główna przyczyna:**

Konfiguracja sieci w sieci hosta uniemożliwia agentowi IoT Edge uzyskiwanie dostępu do sieci. Agent najpierw próbuje nawiązać połączenie za pośrednictwem protokołu AMQP (port 5671). Jeśli połączenie nie powiedzie się, zostanie podjęta próba nawiązania połączenia z usługą WebSockets (port 443).

Środowisko uruchomieniowe usługi IoT Edge konfiguruje sieć dla każdego z modułów na potrzeby komunikacji. W systemie Linux ta sieć jest siecią mostka. W systemie Windows jest używany translator adresów sieciowych. Ten problem występuje częściej na urządzeniach z systemem Windows używających kontenerów systemu Windows korzystających z sieci NAT.

**Rozwiązanie:**

Upewnij się, że istnieje trasa do Internetu dla adresów IP przypisanych do tej sieci mostka lub sieci NAT. Czasem konfiguracja sieci VPN na hoście przesłania sieć usługi IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)

**Obserwowane zachowanie:**

Nie można uruchomić kontenera, a w dziennikach edgeAgent jest wyświetlany błąd 403.

**Główna przyczyna:**

Agent IoT Edge nie ma uprawnień dostępu do obrazu modułu.

**Rozwiązanie:**

Upewnij się, że poświadczenia rejestru są poprawnie określone w manifeście wdrożenia.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Moduł agenta brzegowego raportuje "pusty plik konfiguracji" i nie uruchomiono modułów na urządzeniu

**Obserwowane zachowanie:**

Urządzenie ma problemy z uruchamianiem modułów zdefiniowanych we wdrożeniu. Tylko edgeAgent jest uruchomiona, ale ciągle zgłasza "pusty plik konfiguracji"... ".

**Główna przyczyna:**

Domyślnie IoT Edge uruchamia moduły w swojej własnej izolowanej sieci kontenerów. Urządzenie może mieć problemy z rozpoznawaniem nazw DNS w ramach tej sieci prywatnej.

**Rozwiązanie:**

**Opcja 1. Ustawianie serwera DNS w ustawieniach aparatu kontenera**

Określ serwer DNS dla środowiska w ustawieniach aparatu kontenera, które będą stosowane do wszystkich modułów kontenera uruchomionych przez aparat. Utwórz plik o nazwie `daemon.json` określając serwer DNS, który ma być używany. Na przykład:

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

Jeśli lokalizacja zawiera już `daemon.json` plik, Dodaj do niej klucz **DNS** i Zapisz plik.

Aby aktualizacje zaczęły obowiązywać, uruchom ponownie aparat kontenerów.

| Platforma | Polecenie |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (administracyjny program PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opcja 2: Ustaw serwer DNS w IoT Edge wdrożenia na moduł**

Można ustawić serwer DNS dla *opcji* "wszystkie" modułu we wdrożeniu IoT Edge. Na przykład:

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

## <a name="iot-edge-hub-fails-to-start"></a>Nie można uruchomić Centrum IoT Edge

**Obserwowane zachowanie:**

Nie można uruchomić modułu edgeHub. W dziennikach może zostać wyświetlony komunikat podobny do następującego błędu:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Lub

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Główna przyczyna:**

Inny proces na komputerze-hoście ma port, który jest poddany do modułu edgeHub. IoT Edge Hub mapuje porty 443, 5671 i 8883 do użycia w scenariuszach bramy. Nie można uruchomić modułu, jeśli inny proces już powiązano z jednym z tych portów.

**Rozwiązanie:**

Ten problem można rozwiązać na dwa sposoby:

Jeśli urządzenie IoT Edge działa jako urządzenie bramy, należy znaleźć i zatrzymać proces korzystający z portu 443, 5671 lub 8883. Błąd portu 443 zazwyczaj oznacza, że inny proces to serwer sieci Web.

Jeśli nie musisz używać urządzenia IoT Edge jako bramy, możesz usunąć powiązania portów z modułu edgeHub. Możesz zmienić opcje tworzenia w Azure Portal lub bezpośrednio w deployment.jsna pliku.

W witrynie Azure Portal:

1. Przejdź do centrum IoT Hub i wybierz pozycję **IoT Edge**.

2. Wybierz urządzenie IoT Edge, które chcesz zaktualizować.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz pozycję **Ustawienia środowiska uruchomieniowego**.

5. W ustawieniach modułu **centrum brzegowego** Usuń wszystko z pola tekstowego **Utwórz opcje** .

6. Zapisz zmiany i Utwórz wdrożenie.

W deployment.jspliku:

1. Otwórz deployment.jspliku, który został zastosowany do urządzenia IoT Edge.

2. Znajdź `edgeHub` Ustawienia w sekcji edgeAgent żądane właściwości:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Usuń `createOptions` wiersz i końcowe przecinek na końcu `image` wiersza przed nim:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Zapisz plik i ponownie zastosuj go do urządzenia IoT Edge.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge demona zabezpieczeń kończy się niepowodzeniem z nieprawidłową nazwą hosta

**Obserwowane zachowanie:**

Próba [sprawdzenia, czy dzienniki programu IoT Edge Security Manager](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) kończy się niepowodzeniem i drukuje następujący komunikat:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Główna przyczyna:**

Środowisko uruchomieniowe IoT Edge może obsługiwać tylko nazwy hostów krótsze niż 64 znaków. Maszyny fizyczne zazwyczaj nie mają długich nazw hostów, ale problem jest bardziej powszechny w maszynie wirtualnej. W szczególności automatycznie generowane nazwy hostów dla maszyn wirtualnych z systemem Windows hostowanych na platformie Azure.

**Rozwiązanie:**

Gdy ten błąd jest wyświetlany, można go rozwiązać przez skonfigurowanie nazwy DNS maszyny wirtualnej, a następnie ustawienie nazwy DNS jako nazwy hosta w poleceniu instalacji.

1. W Azure Portal przejdź do strony Przegląd swojej maszyny wirtualnej.
2. Wybierz pozycję **Konfiguruj** w obszarze Nazwa DNS. Jeśli maszyna wirtualna ma już skonfigurowaną nazwę DNS, nie trzeba konfigurować nowej.

   ![Skonfiguruj nazwę DNS maszyny wirtualnej](./media/troubleshoot/configure-dns.png)

3. Podaj wartość w obszarze **etykieta nazwy DNS** i wybierz pozycję **Zapisz**.
4. Skopiuj nową nazwę DNS, która powinna mieć format **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.
5. W ramach maszyny wirtualnej Użyj następującego polecenia, aby skonfigurować środowisko uruchomieniowe IoT Edge przy użyciu nazwy DNS:

   * W systemie Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * W systemie Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nie można pobrać dzienników demona IoT Edge w systemie Windows

**Obserwowane zachowanie:**

Podczas korzystania z systemu Windows jest pobierany dziennik zdarzeń `Get-WinEvent` .

**Główna przyczyna:**

`Get-WinEvent`Polecenie programu PowerShell polega na wpisie rejestru, który ma być obecny w celu znalezienia dzienników według określonego `ProviderName` .

**Rozwiązanie:**

Ustaw wpis rejestru dla demona IoT Edge. Utwórz plik **iotedge. reg** o następującej zawartości i zaimportuj go do rejestru systemu Windows, klikając go dwukrotnie lub korzystając z `reg import iotedge.reg` polecenia:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problemy ze stabilnością na mniejszych urządzeniach

**Obserwowane zachowanie:**

W przypadku urządzeń z ograniczonymi zasobami mogą wystąpić problemy z ograniczeniami, takie jak Raspberry Pi, zwłaszcza w przypadku korzystania z bramy. Objawy obejmują wyjątki braku pamięci w module IoT Edge Hub, urządzenia podrzędne, które nie są połączone, lub urządzenie nie wyśle komunikatów telemetrycznych po kilku godzinach.

**Główna przyczyna:**

Centrum IoT Edge, które jest częścią środowiska uruchomieniowego IoT Edge, jest zoptymalizowane pod kątem wydajności domyślnie i próbuje przydzielić duże fragmenty pamięci. Ta optymalizacja nie jest idealna dla urządzeń z ograniczonymi granicami i może spowodować problemy ze stabilnością.

**Rozwiązanie:**

W przypadku Centrum IoT Edge Ustaw dla zmiennej środowiskowej **OptimizeForPerformance** **wartość false**. Istnieją dwa sposoby ustawiania zmiennych środowiskowych:

W witrynie Azure Portal:

W IoT Hub wybierz urządzenie IoT Edge i ze strony Szczegóły urządzenia, a następnie wybierz pozycję **Ustaw**  >  **Ustawienia środowiska uruchomieniowego** moduły. Utwórz zmienną środowiskową dla modułu IoT Edge Hub o nazwie *OptimizeForPerformance* , która ma wartość *false*.

![OptimizeForPerformance ustawiona na wartość false](./media/troubleshoot/optimizeforperformance-false.png)

W manifeście wdrożenia:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>Moduł IoT Edge nie może wysłać komunikatu do edgeHub z błędem 404

**Obserwowane zachowanie:**

Niestandardowa moduł IoT Edge nie może wysłać komunikatu do centrum IoT Edge z `Module not found` błędem 404. Demon IoT Edge przedrukuje następujący komunikat do dzienników:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Główna przyczyna:**

Demon IoT Edge wymusza identyfikację procesu dla wszystkich modułów łączących się z edgeHub ze względów bezpieczeństwa. Sprawdza, czy wszystkie komunikaty wysyłane przez moduł pochodzą z głównego identyfikatora procesu modułu. Jeśli komunikat jest wysyłany przez moduł z innego procesu niż pierwotnie ustanowiony, odrzuci komunikat z komunikatem o błędzie 404.

**Rozwiązanie:**

W przypadku wersji 1.0.7 wszystkie procesy modułów są autoryzowane do nawiązania połączenia. Aby uzyskać więcej informacji, zobacz [Dziennik zmian wersji 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Jeśli uaktualnienie do 1.0.7 nie jest możliwe, wykonaj następujące czynności. Upewnij się, że ten sam identyfikator procesu jest zawsze używany przez moduł IoT Edge niestandardowego do wysyłania komunikatów do edgeHub. Na przykład upewnij się, że `ENTRYPOINT` zamiast `CMD` polecenia w pliku Docker. `CMD`Polecenie prowadzi do jednego identyfikatora procesu dla modułu i innego identyfikatora procesu dla polecenia bash uruchamianego w programie głównym, ale `ENTRYPOINT` prowadzi do jednego identyfikatora procesu.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>Pomyślnie wdrożenia modułów IoT Edge zostaną wyświetlone z urządzenia

**Obserwowane zachowanie:**

Po ustawieniu modułów dla urządzenia IoT Edge moduły zostaną wdrożone pomyślnie, ale po kilku minutach znikną z urządzenia i ze szczegółowych informacji o urządzeniu w Azure Portal. Inne moduły niż zdefiniowane przez siebie mogą być również wyświetlane na urządzeniu.

**Główna przyczyna:**

Jeśli wdrożenie automatyczne jest przeznaczone dla urządzenia, ma wyższy priorytet niż ręczne ustawianie modułów dla jednego urządzenia. Funkcje **Set modules** w Azure Portal lub **Create Deployment dla funkcji pojedynczego urządzenia** w programie Visual Studio Code zaczną obowiązywać przez chwilę. Na urządzeniu są widoczne zdefiniowane moduły. Następnie priorytet wdrożenia automatycznego uruchamia się i zastępuje odpowiednie właściwości urządzenia.

**Rozwiązanie:**

Należy używać tylko jednego typu mechanizmu wdrażania dla każdego urządzenia, wdrożenia automatycznego lub poszczególnych urządzeń. Jeśli masz wiele wdrożeń automatycznych przeznaczonych dla urządzenia, możesz zmienić priorytet lub opisy elementów docelowych, aby upewnić się, że odpowiednie rozwiązanie dotyczy danego urządzenia. Możesz również zaktualizować sznurek urządzenia, aby nie odpowiadał już opisowi docelowemu wdrożenia automatycznego.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge za bramą nie może wykonywać żądań HTTP i uruchamiać modułu edgeAgent

**Obserwowane zachowanie:**

Demon IoT Edge jest aktywny z prawidłowym plikiem konfiguracji, ale nie może uruchomić modułu edgeAgent. Polecenie `iotedge list` zwraca listę pustą. Raport demonów IoT Edge `Could not perform HTTP request` .

**Główna przyczyna:**

IoT Edge urządzeń za bramą Pobierz obrazy modułów z nadrzędnego urządzenia IoT Edge określonego w `parent_hostname` polu pliku config. YAML. `Could not perform HTTP request`Błąd oznacza, że urządzenie podrzędne nie może nawiązać połączenia z urządzeniem nadrzędnym za pośrednictwem protokołu HTTP.

**Rozwiązanie:**

Upewnij się, że nadrzędne urządzenie IoT Edge może odbierać żądania przychodzące z podrzędnego urządzenia IoT Edge. Otwórz ruch sieciowy na portach 443 i 6617 dla żądań pochodzących z urządzenia podrzędnego.

:::moniker-end

## <a name="next-steps"></a>Następne kroki

Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues) , aby umożliwić kontynuowanie ulepszania.

Jeśli masz więcej pytań, Utwórz [support Request](https://portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.
