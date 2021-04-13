---
title: Rozwiązywanie problemów — Azure IoT Edge | Microsoft Docs
description: Skorzystaj z tego artykułu, aby poznać standardowe umiejętności diagnostyki dla Azure IoT Edge, takich jak pobieranie stanu i dzienników składników
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fa49af946a1e5fc631eeb1ee9b9c7c99d3adff8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308272"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Rozwiązywanie problemów z urządzeniem IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Jeśli napotykasz problemy z uruchamianiem Azure IoT Edge w środowisku, użyj tego artykułu jako przewodnika dotyczącego rozwiązywania problemów i diagnostyki.

## <a name="run-the-check-command"></a>Uruchom polecenie "Check"

Pierwszy krok podczas rozwiązywania problemów IoT Edge powinien używać `check` polecenia, które uruchamia kolekcję testów konfiguracji i łączności dla typowych problemów. `check`Polecenie jest dostępne w [wersji 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) lub nowszej.

>[!NOTE]
>Narzędzie do rozwiązywania problemów nie może uruchomić sprawdzania łączności, jeśli urządzenie IoT Edge znajduje się za serwerem proxy.

Można uruchomić `check` polecenie w następujący sposób lub dodać `--help` flagę, aby wyświetlić pełną listę opcji:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
W systemie Linux:

```bash
sudo iotedge check
```

W systemie Windows:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

Narzędzie do rozwiązywania problemów uruchamia wiele sprawdzeń, które są sortowane w tych trzech kategoriach:

* *Testy konfiguracji* sprawdzają szczegóły, które mogą uniemożliwić IoT Edge urządzeniom łączenie się z chmurą, w tym problemy z plikiem konfiguracji i aparatem kontenerów.
* *Sprawdź, czy środowisko* uruchomieniowe IoT Edge może uzyskać dostęp do portów na urządzeniu hosta i czy wszystkie składniki IoT Edge mogą łączyć się z IoT Hub. Ten zestaw testów zwraca błędy, jeśli urządzenie IoT Edge znajduje się za serwerem proxy.
* *Kontrole gotowości produkcyjnej* szukają zalecanych najlepszych rozwiązań w zakresie produkcji, takich jak stan certyfikatów urzędu certyfikacji urządzeń i konfiguracji pliku dziennika modułu.

Narzędzie sprawdzania IoT Edge używa kontenera do uruchamiania jego diagnostyki. Obraz kontenera `mcr.microsoft.com/azureiotedge-diagnostics:latest` jest dostępny za pomocą [programu Microsoft Container Registry](https://github.com/microsoft/containerregistry). Jeśli musisz uruchomić kontrolę na urządzeniu bez bezpośredniego dostępu do Internetu, Twoje urządzenia będą potrzebować dostępu do obrazu kontenera.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

W scenariuszu korzystającym z zagnieżdżonych IoT Edge urządzeń można uzyskać dostęp do obrazu diagnostyki na urządzeniach podrzędnych przez kierowanie obrazu za pośrednictwem urządzeń nadrzędnych.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

Aby uzyskać informacje o każdym z testów diagnostycznych wykonywanych przez to narzędzie, w tym o tym, co należy zrobić, jeśli wystąpi błąd lub ostrzeżenie, zobacz [IoT Edge Rozwiązywanie problemów z testami](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Zbierz informacje debugowania za pomocą polecenia "support-Binding"

Gdy zachodzi potrzeba zebrania dzienników z urządzenia IoT Edge, Najwygodniejszym sposobem jest użycie `support-bundle` polecenia. Domyślnie to polecenie zbiera dane modułów, IoT Edge Security Manager oraz dzienników aparatu kontenerów, `iotedge check` danych wyjściowych JSON i inne przydatne informacje debugowania. Kompresuje je do jednego pliku, aby można było je łatwo udostępnić. `support-bundle`Polecenie jest dostępne w [wersji 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) lub nowszej.

Uruchom `support-bundle` polecenie z `--since` flagą, aby określić, jak długo od dawna chcesz pobrać dzienniki. Na przykład `6h` otrzymasz dzienniki od ostatnich sześciu godzin od ostatniego `6d` szóstego dnia od ostatniego 6 `6m` minut i tak dalej. Dołącz `--help` flagę, aby wyświetlić pełną listę opcji.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

W systemie Linux:

```bash
sudo iotedge support-bundle --since 6h
```

W systemie Windows:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

Możesz również użyć wywołania [metody bezpośredniej](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) do urządzenia, aby przekazać dane wyjściowe polecenia support-Binding do usługi Azure Blob Storage.

> [!WARNING]
> Dane wyjściowe `support-bundle` polecenia mogą zawierać nazwy hosta, urządzenia i modułu, informacje zarejestrowane przez moduły itp. Pamiętaj o tym, jeśli udostępnisz dane wyjściowe na forum publicznym.

## <a name="check-your-iot-edge-version"></a>Sprawdź wersję IoT Edge

Jeśli używasz starszej wersji usługi IoT Edge, uaktualnienie może rozwiązać problem. `iotedge check`Narzędzie sprawdza, czy IoT Edge Security DAEMON to Najnowsza wersja, ale nie sprawdza wersji centrów IoT Edge i modułów agentów. Aby sprawdzić wersję modułów środowiska uruchomieniowego na urządzeniu, Użyj poleceń `iotedge logs edgeAgent` i `iotedge logs edgeHub` . Numer wersji jest deklarowany w dziennikach podczas uruchamiania modułu.

Aby uzyskać instrukcje dotyczące sposobu aktualizowania urządzenia, zobacz [Aktualizacja demona i środowisko uruchomieniowe IoT Edge Security](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Weryfikowanie instalacji IoT Edge na urządzeniach

Instalację IoT Edge na urządzeniach można zweryfikować przez [monitorowanie sznurka modułu edgeAgent](./how-to-monitor-module-twins.md).

Aby uzyskać najnowszą edgeAgent modułu, uruchom następujące polecenie w [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

To polecenie spowoduje wyjście wszystkich edgeAgent [raportowanych właściwości](./module-edgeagent-edgehub.md). Poniżej przedstawiono kilka przydatnych elementów monitorowania stanu urządzenia:

* stan środowiska uruchomieniowego
* czas rozpoczęcia środowiska uruchomieniowego
* czas ostatniego zakończenia środowiska uruchomieniowego
* Liczba ponownych uruchomień środowiska uruchomieniowego

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Sprawdź stan programu IoT Edge Security Manager i jego dzienników

[IoT Edge Security Manager](iot-edge-security-manager.md) jest odpowiedzialny za operacje, takie jak inicjowanie systemu IoT Edge przy uruchamianiu i aprowizacji urządzeń. Jeśli IoT Edge nie zostanie uruchomiona, dzienniki Menedżera zabezpieczeń mogą dostarczyć przydatne informacje.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
W systemie Linux:

* Wyświetl stan programu IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Wyświetlanie dzienników programu IoT Edge Security Manager:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Zobacz więcej szczegółowych dzienników IoT Edge Security Manager:

  1. Edytuj ustawienia demona IoT Edge:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Zaktualizuj następujące wiersze:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. Uruchom ponownie demona zabezpieczeń IoT Edge:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

W systemie Windows:

* Wyświetl stan programu IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Wyświetlanie dzienników programu IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Wyświetl tylko 5 ostatnich minut z dzienników IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Zobacz więcej szczegółowych dzienników IoT Edge Security Manager:

  1. Dodaj zmienną środowiskową na poziomie systemu:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Uruchom ponownie demona zabezpieczeń IoT Edge:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Wyświetlanie stanu usług systemu IoT Edge:

   ```bash
   sudo iotedge system status
   ```

* Wyświetlanie dzienników usług systemu IoT Edge:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Włącz dzienniki na poziomie debugowania, aby wyświetlić bardziej szczegółowe dzienniki IoT Edge usług systemowych:

  1. Włącz dzienniki na poziomie debugowania.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Po debugowaniu Przełącz się z powrotem do domyślnych dzienników na poziomie informacji.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>Sprawdź dzienniki kontenerów pod kątem problemów

Po uruchomieniu demona zabezpieczeń IoT Edge należy zapoznać się z dziennikami kontenerów w celu wykrycia problemów. Zacznij od wdrożonych kontenerów, a następnie Przyjrzyj się kontenerom, które składają się na IoT Edge środowisko uruchomieniowe: edgeAgent i edgeHub. Dzienniki agenta IoT Edge zwykle zawierają informacje dotyczące cyklu życia każdego kontenera. Dzienniki Centrum IoT Edge zawierają informacje dotyczące komunikatów i routingu.

```cmd
iotedge logs <container name>
```

Możesz również użyć wywołania [metody bezpośredniej](how-to-retrieve-iot-edge-logs.md#upload-module-logs) do modułu na urządzeniu, aby przekazać dzienniki tego modułu do BLOB Storage platformy Azure.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Wyświetlanie komunikatów przechodzących przez Centrum IoT Edge

<!--1.1 -->
:::moniker range="iotedge-2018-06"

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
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Zamień `env: {}` na:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Pliki YAML nie mogą zawierać tabulatorów jako wcięcia. Zamiast tego należy użyć dwóch spacji. Elementy najwyższego poziomu nie mogą zawierać odstępów wiodących.

Zapisz plik i ponownie uruchom Menedżera zabezpieczeń IoT Edge.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Możesz wyświetlić komunikaty przechodzące przez Centrum IoT Edge i zebrać informacje z pełnych dzienników z kontenerów środowiska uruchomieniowego. Aby włączyć pełne dzienniki w tych kontenerach, należy ustawić `RuntimeLogLevel` zmienną środowiskową w manifeście wdrożenia.

Aby wyświetlić komunikaty przechodzące przez Centrum IoT Edge, należy ustawić `RuntimeLogLevel` zmienną środowiskową na `debug` dla modułu edgeHub.

Moduły edgeHub i edgeAgent mają tę zmienną środowiskową dziennika środowiska uruchomieniowego, z wartością domyślną ustawioną na `info` . Ta zmienna środowiskowa może przyjmować następujące wartości:

* krytyczn
* error
* warning
* informacje o
* debugowanie
* tryb pełny

<!-- end 1.2 -->
:::moniker-end

Możesz również sprawdzić komunikaty wysyłane między IoT Hub a urządzeniami IoT. Te komunikaty są wyświetlane przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Aby uzyskać więcej informacji, zobacz [przydatne narzędzie podczas opracowywania przy użyciu usługi Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Uruchom ponownie kontenery

Po zbadaniu dzienników i komunikatów w celu uzyskania informacji możesz spróbować ponownie uruchomić kontenery:

```cmd
iotedge restart <container name>
```

Uruchom ponownie kontenery środowiska uruchomieniowego IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Sprawdzanie reguł konfiguracji zapory i portów

Azure IoT Edge umożliwia komunikację między serwerem lokalnym a chmurą platformy Azure przy użyciu obsługiwanych protokołów IoT Hub, zobacz [Wybieranie protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md). W celu zwiększenia bezpieczeństwa kanały komunikacyjne między Azure IoT Edge i IoT Hub platformy Azure są zawsze skonfigurowane do wychodzącego. Ta konfiguracja jest oparta na [wzorcu komunikacji z pomocą techniczną](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), która minimalizuje podatność na ataki dla złośliwej jednostki. Komunikacja przychodząca jest wymagana tylko dla konkretnych scenariuszy, w których usługa Azure IoT Hub musi przekazywać komunikaty do urządzenia Azure IoT Edge. Komunikaty z chmury do urządzeń są chronione za pomocą bezpiecznych kanałów TLS i można je zabezpieczyć za pomocą certyfikatów X. 509 oraz modułów urządzeń TPM. Azure IoT Edge Security Manager decyduje o tym, jak można nawiązać tę komunikację, zobacz [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Chociaż IoT Edge zapewnia rozszerzoną konfigurację do zabezpieczania Azure IoT Edge środowiska uruchomieniowego i wdrożonych modułów, nadal zależy od konfiguracji komputera i sieci. Z tego względu należy upewnić się, że skonfigurowano odpowiednie reguły sieci i zapory na potrzeby bezpiecznej krawędzi komunikacji z chmurą. Poniższa tabela może służyć jako wytyczna, gdy reguły zapory konfiguracyjnej dla serwerów źródłowych, na których jest hostowana Azure IoT Edge środowisko uruchomieniowe:

|Protokół|Port|Dane|Przeznaczony|Wskazówki|
|--|--|--|--|--|
|MQTT|8883|ZABLOKOWANE (domyślnie)|ZABLOKOWANE (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) do otwarcia w przypadku używania MQTT jako protokołu komunikacyjnego.<li>1883 dla MQTT nie jest obsługiwane przez IoT Edge. <li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul>|
|AMQP|5671|ZABLOKOWANE (domyślnie)|Otwórz (domyślnie)|<ul> <li>Domyślny protokół komunikacyjny dla IoT Edge. <li> Musi być skonfigurowany do otwierania, jeśli Azure IoT Edge nie jest skonfigurowana dla innych obsługiwanych protokołów lub AMQP jest żądanym protokołem komunikacyjnym.<li>5672 dla AMQP nie jest obsługiwane przez IoT Edge.<li>Blokuj ten port, gdy Azure IoT Edge używa innego obsługiwanego IoT Hub protokołu.<li>Połączenia przychodzące (przychodzące) powinny być blokowane.</ul></ul>|
|HTTPS|443|ZABLOKOWANE (domyślnie)|Otwórz (domyślnie)|<ul> <li>Skonfiguruj wychodzące (wychodzące) do otwarcia w dniu 443 dla IoT Edge aprowizacji. Ta konfiguracja jest wymagana w przypadku używania skryptów ręcznych lub usługi Azure IoT Device Provisioning Service (DPS). <li>Połączenie przychodzące (przychodzące) powinno być otwarte tylko dla konkretnych scenariuszy: <ul> <li>  Jeśli masz niejawną bramę z urządzeniami typu liść, które mogą wysyłać żądania metod. W takim przypadku port 443 nie musi być otwarty dla sieci zewnętrznych, aby połączyć się z usługą IoTHub lub udostępnić usługi IoTHub za poorednictwem Azure IoT Edge. W ten sposób Reguła przychodząca może być ograniczona tylko do otwarcia przychodzącego (przychodzącego) z sieci wewnętrznej. <li> W przypadku scenariuszy klient-urządzenie (C2D).</ul><li>80 dla protokołu HTTP nie jest obsługiwane przez IoT Edge.<li>Jeśli nie można skonfigurować protokołów innych niż HTTP (na przykład AMQP lub MQTT) w przedsiębiorstwie; komunikaty można wysyłać za pośrednictwem obiektów WebSockets. Port 443 będzie używany na potrzeby komunikacji z użyciem protokołu WebSocket.</ul>|

## <a name="next-steps"></a>Następne kroki

Uważasz, że znaleziono usterkę platformy IoT Edge? [Prześlij problem](https://github.com/Azure/iotedge/issues) , aby umożliwić kontynuowanie ulepszania.

Jeśli masz więcej pytań, Utwórz [support Request](https://portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.
