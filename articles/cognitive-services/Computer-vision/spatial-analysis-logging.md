---
title: Telemetria i rejestrowanie dla kontenerów analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Analiza przestrzenna zapewnia każdemu kontenerowi typowe szczegółowe informacje o platformie konfiguracji, rejestrowanie i ustawienia zabezpieczeń.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589503"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria i rozwiązywanie problemów

Analiza przestrzenna obejmuje zestaw funkcji do monitorowania kondycji systemu i pomocy w diagnozowaniu problemów.

## <a name="enable-visualizations"></a>Włączanie wizualizacji

Aby włączyć wizualizację AI Insights zdarzeń w ramce wideo, należy użyć wersji operacji analizy przestrzennej `.debug` na komputerze [](spatial-analysis-operations.md) stacjonarnym. Wizualizacja nie jest możliwa na Azure Stack Edge urządzeniach. Dostępne są cztery operacje debugowania.

Jeśli urządzenie nie jest urządzeniem Azure Stack Edge, edytuj plik [](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) manifestu wdrożenia dla komputerów stacjonarnych, aby użyć poprawnej wartości zmiennej `DISPLAY` środowiskowej. Musi ona być `$DISPLAY` dopasowana do zmiennej na komputerze hosta. Po zaktualizowaniu manifestu wdrożenia ponownie wdychaj kontener.

Po zakończeniu wdrażania może być konieczne skopiowanie pliku z komputera-hosta do kontenera `.Xauthority` i ponowne uruchomienie go. W poniższym przykładzie `peopleanalytics` jest nazwą kontenera na komputerze-hoście.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Zbieranie danych telemetrycznych kondycji systemu

Telegraf to obraz open source, który współpracuje z analizą przestrzenną i jest dostępny w Microsoft Container Registry. Pobiera następujące dane wejściowe i wysyła je do Azure Monitor. Moduł telegraf można skontworzyć przy użyciu żądanych niestandardowych danych wejściowych i wyjściowych. Konfiguracja modułu telegrafu w analizie przestrzennej jest częścią manifestu wdrożenia (link powyżej). Ten moduł jest opcjonalny i można go usunąć z manifestu, jeśli nie jest potrzebny. 

Wejścia: 
1. Metryki analizy przestrzennej
2. Metryki dysku
3. Metryki procesora CPU
4. Metryki platformy Docker
5. Metryki procesora GPU

Wyjść:
1. Azure Monitor

Dostarczony moduł telegrafu analizy przestrzennej opublikuje wszystkie dane telemetryczne emitowane przez kontener analizy przestrzennej do Azure Monitor. Zobacz [Azure Monitor,](../../azure-monitor/overview.md) aby uzyskać informacje na temat Azure Monitor subskrypcji.

Po skonfigurowaniu Azure Monitor należy utworzyć poświadczenia umożliwiające modułowi wysyłanie danych telemetrycznych. Aby utworzyć nową jednostkę Azure Portal, możesz użyć interfejsu wiersza polecenia platformy Azure lub poniższego polecenia interfejsu wiersza polecenia platformy Azure.

> [!NOTE] 
> To polecenie wymaga uprawnień właściciela subskrypcji. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

W manifeście wdrożenia dla urządzenia [](https://go.microsoft.com/fwlink/?linkid=2152270) [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)komputera stacjonarnego lub maszyny wirtualnej platformy Azure z procesorem [GPU](https://go.microsoft.com/fwlink/?linkid=2152189)odszukaj moduł *telegrafu,* a następnie zastąp następujące wartości informacjami jednostki usługi z poprzedniego kroku i przewłasz.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Po wdrożeniu modułu telegraf można uzyskać dostęp do zgłoszonych metryk za pośrednictwem usługi  Azure Monitor lub wybierając pozycję Monitorowanie w IoT Hub na Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetrii":::

### <a name="system-health-events"></a>Zdarzenia dotyczące kondycji systemu

| Nazwa zdarzenia                  | Opis    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Wysyłane, gdy użytkownik zmieni stan modułu Analizy przestrzennej *z uruchomionego na* *zatrzymany.*  |
| archon_error                | Wysyłane, gdy dowolny z procesów wewnątrz kontenera ulega awarii. Jest to błąd krytyczny.      |
| InputRate                   | Szybkość, z jaką graf przetwarza dane wejściowe wideo. Raporty co 5 minut.              |
| OutputRate                  | Szybkość, z jaką graf wyprowadza szczegółowe informacje dotyczące AI. Raporty co 5 minut.                |
| archon_allGraphsStarted     | Wysyłane po zakończeniu uruchamiania wszystkich grafów.                                           |
| archon_configchange         | Wysyłane po zmianie konfiguracji grafu.                                              |
| archon_graphCreationFailed  | Wysyłane, gdy nie można uruchomić `graphId` grafu z raportem.                           |
| archon_graphCreationSuccess | Wysyłane, gdy graf z raportem `graphId` zostanie uruchomiony pomyślnie.                      |
| archon_graphCleanup         | Wysyłane, gdy graf z raportem `graphId` czyści i kończy działanie.                      |
| archon_graphHeartbeat       | Puls jest wysyłany co minutę dla każdego grafu umiejętności.                                   |
| archon_apiKeyAuthFail       | Wysyłane, gdy przetwarzanie obrazów klucza zasobu nie może uwierzytelnić kontenera przez więcej niż 24 godziny z następujących powodów: Poza limitem przydziału, Nieprawidłowy, Offline. |
| VideoIngkonkurencyjnAfertbeat      | Wysyłane co godzinę, aby wskazać, że wideo jest przesyłane strumieniowo ze źródła wideo, z liczbą błędów w ciągu tej godziny. Raporty dla każdego grafu. |
| VideoIngująceState          | Raporty *zatrzymane lub* *uruchomione dla* przesyłania strumieniowego wideo. Raporty dla każdego grafu.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Rozwiązywanie problemów z IoT Edge sieciowym

Możesz użyć narzędzia `iotedge` wiersza polecenia, aby sprawdzić stan i dzienniki uruchomionych modułów. Na przykład:
* `iotedge list`: raportuje listę uruchomionych modułów. 
  Możesz dodatkowo sprawdzić, czy występują błędy, za pomocą . `iotedge logs edgeAgent` Jeśli `iotedge` problem zostanie zablokowany, możesz spróbować uruchomić go ponownie za pomocą `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` aby ponownie uruchomić określony moduł 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Zbieranie plików dziennika za pomocą kontenera diagnostyki

Analiza przestrzenna generuje dzienniki debugowania platformy Docker, których można użyć do diagnozowania problemów ze środowiskiem uruchomieniowym lub dołączyć je do biletów pomocy technicznej. Moduł diagnostyki analizy przestrzennej jest dostępny w Microsoft Container Registry do pobrania. W pliku wdrożenia manifestu dla urządzenia [](https://go.microsoft.com/fwlink/?linkid=2152270) [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)komputera stacjonarnego lub maszyny wirtualnej platformy Azure z [procesorem GPU](https://go.microsoft.com/fwlink/?linkid=2152189) poszukaj *modułu diagnostyki.*

W sekcji "env" dodaj następującą konfigurację:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Aby zoptymalizować dzienniki przekazywane do zdalnego punktu końcowego, na przykład Azure Blob Storage, zalecamy utrzymywanie niewielkiego rozmiaru pliku. Zapoznaj się z poniższym przykładem, aby uzyskać zalecaną konfigurację dzienników platformy Docker.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Konfigurowanie poziomu dziennika

Konfiguracja na poziomie dziennika umożliwia kontrolowanie szczegółowości wygenerowanych dzienników. Obsługiwane poziomy dziennika to: `none` , , , i `verbose` `info` `warning` `error` . Domyślny poziom szczegółowości dziennika dla węzłów i platformy to `info` . 

Poziomy dziennika można modyfikować globalnie, ustawiając `ARCHON_LOG_LEVEL` zmienną środowiskową na jedną z dozwolonych wartości.
Można go również ustawić za pomocą dokumentu bliźniaczej reprezentacji modułu IoT Edge globalnie, dla wszystkich wdrożonych umiejętności lub dla każdej konkretnej umiejętności, ustawiając wartości dla i , jak pokazano `platformLogLevel` `nodesLogLevel` poniżej.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Zbieranie dzienników

> [!NOTE]
> Moduł nie ma wpływu na zawartość rejestrowania, tylko pomaga w zbieraniu, filtrowaniu i `diagnostics` przekazywaniu istniejących dzienników.
> Aby korzystać z tego modułu, musisz mieć interfejs API platformy Docker w wersji 1.40 lub wyższej.

Przykładowy plik manifestu wdrożenia dla urządzenia [](https://go.microsoft.com/fwlink/?linkid=2152270) [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)komputera stacjonarnego lub maszyny wirtualnej platformy [Azure](https://go.microsoft.com/fwlink/?linkid=2152189) z procesorem GPU zawiera moduł o nazwie , który zbiera i `diagnostics` przesyła dzienniki. Ten moduł jest domyślnie wyłączony i powinien być włączony za pośrednictwem IoT Edge konfiguracji modułu, gdy musisz uzyskać dostęp do dzienników. 

Kolekcja jest na żądanie i kontrolowana za pośrednictwem IoT Edge metody bezpośredniej i może wysyłać dzienniki do `diagnostics` Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Konfigurowanie celów przekazywania diagnostyki

W IoT Edge portal wybierz urządzenie, a następnie moduł **diagnostyki.** W przykładowym pliku manifestu wdrożenia dla [](https://go.microsoft.com/fwlink/?linkid=2152270)urządzenia [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)komputerów stacjonarnych  lub maszyny wirtualnej platformy [Azure](https://go.microsoft.com/fwlink/?linkid=2152189) z procesorem GPU poszukaj sekcji Zmienne środowiskowe dla diagnostyki o nazwie i dodaj `env` następujące informacje:

**Konfigurowanie przekazywania do Azure Blob Storage**

1. Utwórz własne konto Azure Blob Storage, jeśli jeszcze tego nie zostało.
2. Pobierz **wartości parametrów połączenia** dla konta magazynu z Azure Portal. Będzie on znajdował się w **kluczu dostępu**.
3. Dzienniki analizy przestrzennej zostaną automatycznie przekazane do kontenera Blob Storage o nazwie *rtcvlogs* w następującym formacie nazwy pliku: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Przekazywanie dzienników analizy przestrzennej

Dzienniki są przekazywane na żądanie za pomocą `getRTCVLogs` IoT Edge metody w module `diagnostics` . 


1. Przejdź do strony IoT Hub portal, wybierz pozycję **Urządzenia brzegowe,** a następnie wybierz urządzenie i moduł diagnostyki. 
2. Przejdź do strony szczegółów modułu i kliknij ***kartę metody*** bezpośredniej.
3. Wpisz `getRTCVLogs` nazwę metody i ciąg formatu JSON w ładunku. Możesz wprowadzić `{}` wartość , która jest pustym ładunkiem. 
4. Ustaw limity czasu połączenia i metody, a następnie kliknij pozycję **Wywołaj metodę**.
5. Wybierz kontener docelowy i skompilować ciąg JSON ładunku przy użyciu parametrów opisanych w sekcji **Składnia** rejestrowania. Kliknij **pozycję Wywołaj metodę,** aby wykonać żądanie.

>[!NOTE]
> Wywołania metody `getRTCVLogs` z pustym ładunkiem spowoduje zwrócenie listy wszystkich kontenerów wdrożonych na urządzeniu. W nazwie metody jest zróżnicowaana wielkość liter. Jeśli zostanie podana nieprawidłowa nazwa metody, zostanie wyświetlany błąd 501.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Wywołania metody getRTCVLogs ":::
![Strona metody bezpośredniej getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Składnia rejestrowania

W poniższej tabeli wymieniono parametry, których można używać podczas wykonywania zapytań w dziennikach.

| Słowo kluczowe | Description | Wartość domyślna |
|--|--|--|
| StartTime | Żądany czas rozpoczęcia dzienników, w milisekundach czasu UTC. | `-1`, początek środowiska uruchomieniowego kontenera. Gdy `[-1.-1]` jest używany jako zakres czasu, interfejs API zwraca dzienniki z ostatniej godziny.|
| EndTime | Żądana godzina zakończenia dzienników, w milisekundach czasu UTC. | `-1`, bieżąca godzina. Gdy `[-1.-1]` jest używany zakres czasu, interfejs API zwraca dzienniki z ostatniej godziny. |
| ContainerId | Kontener docelowy do pobierania dzienników.| `null`, jeśli nie ma identyfikatora kontenera. Interfejs API zwraca wszystkie dostępne informacje o kontenerach z identyfikatorami.|
| DoPost | Wykonaj operację przekazywania. Jeśli ta wartość jest ustawiona na , wykonuje żądaną operację i zwraca rozmiar przekazywania `false` bez przeprowadzania przekazywania. W przypadku ustawienia `true` na wartość spowoduje to zainicjowanie asynchronicznego przekazywania wybranych dzienników | `false`, nie należy przekazywać.|
| Ograniczenie | Wskazuje, ile wierszy dzienników należy przekazać na partię | `1000`, użyj tego parametru, aby dostosować szybkość post. |
| Filtry | Filtry dzienników do przesłania | `null`Filtry można określić jako pary klucz-wartość na podstawie struktury dzienników analizy przestrzennej: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Na przykład: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

W poniższej tabeli wymieniono atrybuty w odpowiedzi na zapytanie.

| Słowo kluczowe | Opis|
|--|--|
|DoPost| Wartość *true lub* *false*. Wskazuje, czy dzienniki zostały przekazane, czy nie. Jeśli nie chcesz przekazywać dzienników, interfejs API zwraca informacje ***synchronicznie** _. Po wybraniu przekazywania dzienników interfejs API zwraca wartość 200, jeśli żądanie jest prawidłowe, i rozpoczyna przekazywanie dzienników _*_asynchronicznie_**.|
|TimeFilter| Filtr czasu zastosowany do dzienników.|
|ValueFilters (Filtrowanie wartości)| Słowa kluczowe są filtrami zastosowanymi do dzienników. |
|Sygnatury czasowej| Czas rozpoczęcia wykonywania metody. |
|ContainerId| Identyfikator kontenera docelowego. |
|FetchCounter| Łączna liczba wierszy dziennika. |
|FetchSizeInByte| Całkowita ilość danych dziennika w bajtach. |
|MatchCounter| Prawidłowa liczba wierszy dziennika. |
|MatchSizeInByte| Prawidłowa ilość danych dziennika w bajtach. |
|FilterCount| Łączna liczba wierszy dziennika po zastosowaniu filtru. |
|FilterSizeInByte| Całkowita ilość danych dziennika w bajtach po zastosowaniu filtru. |
|FetchLogsDurationInMiliSec| Czas trwania operacji pobierania. |
|PaseLogsDurationInMiliSec| Czas trwania operacji filtrowania. |
|PostLogsDurationInMiliSec| Czas trwania operacji po operacji. |

#### <a name="example-request"></a>Przykładowe żądanie 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Sprawdź wiersze, godziny i rozmiary dziennika pobierania, jeśli te ustawienia wyglądają dobrze, zastąp pole ***DoPost*** lokalizacją i spowoduje to wypchanie dzienników z tymi samymi filtrami `true` do miejsc docelowych. 

Dzienniki można eksportować z Azure Blob Storage podczas rozwiązywania problemów. 

## <a name="common-issues"></a>Typowe problemy

Jeśli w dziennikach modułu zostanie wyświetlony następujący komunikat, może to oznaczać, że subskrypcja platformy Azure musi zostać zatwierdzona: 

"Kontener nie jest w prawidłowym stanie. Weryfikacja subskrypcji nie powiodła się ze stanem "Niezgodność". Klucz interfejsu API nie jest przeznaczony dla danego typu kontenera".

Aby uzyskać więcej informacji, zobacz [Request approval to run the container (Żądanie zatwierdzenia w celu uruchomienia kontenera).](spatial-analysis-container.md#request-approval-to-run-the-container)

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Rozwiązywanie problemów z Azure Stack Edge urządzeniem

W poniższej sekcji przedstawiono pomoc w debugowaniu i weryfikacji stanu Azure Stack Edge urządzenia.

### <a name="access-the-kubernetes-api-endpoint"></a>Uzyskaj dostęp do punktu końcowego interfejsu API platformy Kubernetes. 

1. W lokalnym interfejsie użytkownika urządzenia przejdź do **strony** Urządzenia. 
2. W **obszarze Punkty końcowe urządzenia** skopiuj punkt końcowy usługi interfejsu API Kubernetes. Ten punkt końcowy jest ciągiem w następującym formacie: `https://compute..[device-IP-address]` .
3. Zapisz ciąg punktu końcowego. Użyjesz go później podczas konfigurowania dostępu `kubectl` do klastra Kubernetes.

### <a name="connect-to-powershell-interface"></a>Nawiązywanie połączenia z interfejsem programu PowerShell

Połącz się zdalnie z klienta systemu Windows. Po utworzeniu klastra Kubernetes można zarządzać aplikacjami za pośrednictwem tego klastra. Musisz nawiązać połączenie z interfejsem programu PowerShell urządzenia. W zależności od systemu operacyjnego klienta procedury zdalnego nawiązywania połączenia z urządzeniem mogą się różnić. Poniższe kroki są dla klienta z systemem Windows z uruchomionym programem PowerShell.

> [!TIP]
> * Przed rozpoczęciem upewnij się, że klient systemu Windows jest uruchomiony Windows PowerShell 5.0 lub nowszy.
> * Program PowerShell jest również [dostępny w systemie Linux.](/powershell/scripting/install/installing-powershell-core-on-linux)

1. Uruchom sesję Windows PowerShell jako administrator. 
    1. Upewnij się, że Windows Remote Management jest uruchomiona na kliencie. W wierszu polecenia wpisz `winrm quickconfig` .

2. Przypisz zmienną dla adresu IP urządzenia. Na przykład `$ip = "<device-ip-address>"`.

3. Użyj następującego polecenia, aby dodać adres IP urządzenia do listy zaufanych hostów klienta. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Uruchom Windows PowerShell na urządzeniu. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Po wyświetleniu monitu podaj hasło. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu internetowego. Domyślne hasło lokalnego interfejsu internetowego to `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Uzyskiwanie dostępu do klastra Kubernetes

Po utworzeniu klastra Kubernetes możesz użyć narzędzia wiersza polecenia, aby `kubectl` uzyskać dostęp do klastra.

1. Utwórz nową przestrzeń nazw. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Utwórz użytkownika i pobierz plik konfiguracji. To polecenie wyprowadza informacje o konfiguracji klastra Kubernetes. Skopiuj te informacje i zapisz je w pliku o nazwie *config*. Nie zapisuj pliku jako rozszerzenia pliku.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Dodaj plik *konfiguracji* do folderu *kube* w profilu użytkownika na komputerze lokalnym.    

4. Skojarz przestrzeń nazw z utworzonym użytkownikiem.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Zainstaluj `kubectl` na kliencie systemu Windows przy użyciu następującego polecenia:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Dodaj wpis DNS do pliku hosts w systemie. 
    1. Uruchom Notatnik jako administrator i otwórz plik *hosts* znajdujący się `C:\windows\system32\drivers\etc\hosts` pod . 
    2. Utwórz wpis w pliku hosts z adresem IP urządzenia i domeną DNS na stronie **Urządzenie** w lokalnym interfejsie użytkownika. Punkt końcowy, który należy użyć, będzie wyglądać podobnie do: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Sprawdź, czy możesz nawiązać połączenie z zasobnikami kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Aby pobrać dzienniki kontenerów, uruchom następujące polecenie:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Przydatne polecenia

|Polecenie  |Opis  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Generuje plik konfiguracji kubernetes. Podczas korzystania z polecenia skopiuj informacje do pliku o nazwie *config*. Nie zapisuj pliku z rozszerzeniem pliku.        |
| `Get-HcsApplianceInfo` | Zwraca informacje o urządzeniu. |
| `Enable-HcsSupportAccess` | Generuje poświadczenia dostępu, aby rozpocząć sesję pomocy technicznej. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Jak złożyć bilet pomocy technicznej dla analizy przestrzennej 

Jeśli potrzebujesz większej pomocy w znalezieniu rozwiązania problemu z kontenerem analizy przestrzennej, wykonaj następujące kroki, aby wypełnić i przesłać bilet pomocy technicznej. Nasz zespół wróci do Ciebie z dodatkowymi wskazówkami. 

### <a name="fill-out-the-basics"></a>Wypełnij podstawowe informacje 
Utwórz nowy bilet pomocy technicznej na stronie [Nowy wniosek o pomoc](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) techniczną. Postępuj zgodnie z monitami, aby wypełnić następujące parametry:

![Podstawowe informacje o pomocy technicznej](./media/support-ticket-page-1-final.png)

1. Ustaw **typ problemu na** `Technical` .
2. Wybierz subskrypcję, z których korzystasz do wdrażania kontenera analizy przestrzennej.
3. Wybierz `My services` `Cognitive Services` usługę i wybierz ją.
4. Wybierz zasób, który jest wdrażany w kontenerze analizy przestrzennej.
5. Napisz krótki opis, szczegółowo opisując problem, który występuje. 
6. Wybierz `Spatial Analysis` typ problemu.
7. Wybierz odpowiedni podtyp z listy rozwijanej.
8. Wybierz **pozycję Dalej: Rozwiązania,** aby przejść do następnej strony.

### <a name="recommended-solutions"></a>Zalecane rozwiązania
Następny etap będzie oferować zalecane rozwiązania dla wybranego typu problemu. Te rozwiązania rozwiążą typowe problemy, ale jeśli nie są przydatne dla Twojego rozwiązania, wybierz pozycję **Dalej:** Szczegóły, aby przejść do następnego kroku.

### <a name="details"></a>Szczegóły
Na tej stronie dodaj dodatkowe szczegóły dotyczące problemu, który występuje. Pamiętaj, aby uwzględnić jak najwięcej szczegółów, ponieważ pomoże to naszym inżynierom lepiej zawęzić problem. Uwzględnij preferowaną metodę kontaktu i ważność problemu, abyśmy w odpowiedni sposób się z Tobą skontaktowaliśmy, a następnie wybierz pozycję **Dalej:** Przeglądanie + tworzenie, aby przejść do następnego kroku. 

### <a name="review-and-create"></a>Przegląd i tworzenie 
Przejrzyj szczegóły wniosku o pomoc techniczną, aby upewnić się, że wszystko jest dokładne i efektywnie reprezentuje problem. Gdy wszystko będzie gotowe, wybierz **pozycję Utwórz,** aby wysłać bilet do naszego zespołu. Po otrzymaniu biletu otrzymasz wiadomość e-mail z potwierdzeniem, a nasz zespół pomoże Ci jak najszybciej wrócić do Ciebie. Stan biletu można wyświetlić na stronie Azure Portal.

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie aplikacji internetowej zliczanych przez osoby](spatial-analysis-web-app.md)
* [Konfigurowanie operacji analizy przestrzennej](./spatial-analysis-operations.md)
* [Przewodnik umieszczania aparatu](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
