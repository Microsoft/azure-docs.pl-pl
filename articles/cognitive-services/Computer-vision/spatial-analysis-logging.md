---
title: Dane telemetryczne i rejestrowanie dla kontenerów analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Analiza przestrzenna zapewnia każdy kontener ze wspólną konfiguracją, rejestrowaniem i ustawieniami zabezpieczeń platformy Configuration Framework.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 8154ef7a90011da8c15f52870eebb6c80ebaebca
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496101"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetrię i rozwiązywanie problemów

Analiza przestrzenna obejmuje zestaw funkcji służących do monitorowania kondycji systemu i pomocy w rozwiązywaniu problemów.

## <a name="enable-visualizations"></a>Włącz wizualizacje

Aby włączyć wizualizację zdarzeń usługi AI Insights w ramce wideo, należy użyć `.debug` wersji [operacji analizy przestrzennej](spatial-analysis-operations.md) na komputerze stacjonarnym. Wizualizacja nie jest możliwa na Azure Stack urządzeniach brzegowych. Dostępne są cztery operacje debugowania.

Jeśli urządzenie nie jest Azure Stack urządzeniem brzegowym, edytuj plik manifestu wdrożenia dla [maszyn stacjonarnych](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) , aby użyć poprawnej wartości `DISPLAY` zmiennej środowiskowej. Musi on pasować do `$DISPLAY` zmiennej na komputerze-hoście. Po zaktualizowaniu manifestu wdrożenia ponownie Wdróż kontener.

Po zakończeniu wdrożenia może być konieczne skopiowanie `.Xauthority` pliku z komputera hosta do kontenera i jego ponowne uruchomienie. W poniższym przykładzie `peopleanalytics` jest nazwą kontenera na komputerze hosta.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Zbieraj dane telemetryczne dotyczące kondycji systemu

Telegraf to obraz Open Source, który współdziała z analizą przestrzenną i jest dostępny w Container Registry firmy Microsoft. Pobiera następujące dane wejściowe i wysyła je do Azure Monitor. Moduł telegraf można skompilować przy użyciu żądanych niestandardowych danych wejściowych i wyjściowych. Konfiguracja modułu telegraf w analizie przestrzennej jest częścią manifestu wdrożenia (połączona powyżej). Ten moduł jest opcjonalny i może zostać usunięty z manifestu, jeśli nie jest potrzebny. 

Danych wejściowych 
1. Metryki analizy przestrzennej
2. Metryki dysku
3. Metryki procesora
4. Metryki platformy Docker
5. Metryki procesora GPU

Wydajności
1. Azure Monitor

Dostarczony moduł telegraf analizy przestrzennej opublikuje wszystkie dane telemetryczne emitowane przez kontener analizy przestrzennej do Azure Monitor. Zobacz [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) , aby uzyskać informacje na temat dodawania Azure monitor do subskrypcji.

Po skonfigurowaniu Azure Monitor należy utworzyć poświadczenia, które umożliwiają modułowi wysyłanie danych telemetrycznych. Możesz użyć Azure Portal, aby utworzyć nową nazwę główną usługi, lub użyj poniższego polecenia platformy Azure, aby go utworzyć.

> [!NOTE] 
> To polecenie wymaga posiadania uprawnień właściciela w ramach subskrypcji. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

W manifeście wdrożenia dla [Azure Stack urządzenia brzegowego](https://go.microsoft.com/fwlink/?linkid=2142179) lub innej [maszyny stacjonarnej](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)poszukaj modułu *telegraf* i Zastąp poniższe wartości informacjami o jednostce usługi z poprzedniego kroku i Wdróż ponownie.

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

Po wdrożeniu modułu telegraf do raportowanych metryk można uzyskać dostęp za pomocą usługi Azure Monitor lub wybierając pozycję **monitorowanie** w IoT Hub na Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Raport telemetrii Azure Monitor":::

### <a name="system-health-events"></a>Zdarzenia kondycji systemu

| Nazwa zdarzenia | Opis|
|------|---------|
|archon_exit    |Wysyłany, gdy użytkownik zmieni stan modułu analizy przestrzennej z *uruchomiony* na *zatrzymany*.  |
|archon_error   |Wysyłany, gdy którykolwiek z procesów wewnątrz kontenera ulega awarii. Jest to błąd krytyczny.  |
|InputRate  |Szybkość, z jaką Graf przetwarza dane wejściowe wideo. Raportowane co 5 minut. | 
|OutputRate     |Szybkość, z jaką Graf wyprowadza informacje o AI. Raportowane co 5 minut. |
|archon_allGraphsStarted | Wysyłany po zakończeniu uruchamiania wszystkich wykresów. |
|archon_configchange    | Wysyłany po zmianie konfiguracji grafu. |
|archon_graphCreationFailed     |Wysyłany, gdy nie można uruchomić grafu z zgłoszonym błędem `graphId` . |
|archon_graphCreationSuccess    |Wysyłany, gdy wykres z raportowaniem `graphId` zostanie uruchomiony pomyślnie. |
|archon_graphCleanup    | Wysyłany, gdy wykres z zgłoszonym `graphId` czyszczeniem i zakończeniem. |
|archon_graphHeartbeat  |Puls wysyłany co minutę dla każdego grafu umiejętności. |
|archon_apiKeyAuthFail |Wysyłany, gdy klucz zasobu przetwarzanie obrazów nie może uwierzytelnić kontenera przez więcej niż 24 godziny, z następujących powodów: poza limitem przydziału, nieprawidłowy, w trybie offline. |
|VideoIngesterHeartbeat     |Wysyłana co godzinę, aby wskazać, że wideo jest przesyłane strumieniowo ze źródła wideo, wraz z liczbą błędów w tej godzinie. Zgłoszone dla każdego grafu. |
|VideoIngesterState | Raporty zostały *zatrzymane* lub *uruchomione* na potrzeby przesyłania strumieniowego wideo. Zgłoszone dla każdego grafu. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Rozwiązywanie problemów z urządzeniem IoT Edge

Za pomocą `iotedge` narzędzia wiersza polecenia można sprawdzić stan i dzienniki uruchomionych modułów. Przykład:
* `iotedge list`: Raportuje listę uruchomionych modułów. 
  Możesz również sprawdzić pod kątem błędów `iotedge logs edgeAgent` . Jeśli `iotedge` jest zablokowany, możesz spróbować ponownie uruchomić go za pomocą `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` Aby ponownie uruchomić określony moduł 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Zbierz pliki dzienników przy użyciu kontenera diagnostyki

Analiza przestrzenna generuje Dzienniki debugowania platformy Docker, których można użyć do diagnozowania problemów dotyczących środowiska uruchomieniowego lub dołączania do biletów pomocy technicznej. Moduł diagnostyki analizy przestrzennej jest dostępny w Container Registry firmy Microsoft do pobrania. W pliku wdrożenia manifestu [Azure Stack urządzenia brzegowego](https://go.microsoft.com/fwlink/?linkid=2142179) lub innej [maszyny stacjonarnej](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)poszukaj modułu *diagnostycznego* .

W sekcji "ENV" Dodaj następującą konfigurację:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Aby zoptymalizować dzienniki przekazane do zdalnego punktu końcowego, takiego jak Azure Blob Storage, zalecamy utrzymywanie małego rozmiaru pliku. Zapoznaj się z poniższym przykładem konfiguracji zalecanych dzienników platformy Docker.

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

Konfiguracja poziomu dziennika umożliwia kontrolowanie szczegółowości wygenerowanych dzienników. Obsługiwane poziomy dzienników to: `none` , `verbose` , `info` , `warning` i `error` . Domyślny poziom szczegółowości dziennika dla węzłów i platform to `info` . 

Poziomy dziennika można modyfikować globalnie, ustawiając `ARCHON_LOG_LEVEL` zmienną środowiskową na jedną z dozwolonych wartości.
Można to również ustawić za pomocą dokumentu wieloosiowego modułu IoT Edge w całości, dla wszystkich wdrożonych umiejętności lub dla każdej konkretnej umiejętności, ustawiając wartości dla `platformLogLevel` i `nodeLogLevel` , jak pokazano poniżej.

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
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Zbieranie dzienników

> [!NOTE]
> `diagnostics`Moduł nie ma wpływu na zawartość rejestrowania, ale jest pomocny tylko w gromadzeniu, filtrowaniu i przekazywaniu istniejących dzienników.
> Aby można było korzystać z tego modułu, musisz mieć interfejs API platformy Docker w wersji 1,40 lub nowszej.

Przykładowy plik manifestu wdrożenia dla [urządzenia brzegowego Azure Stack](https://go.microsoft.com/fwlink/?linkid=2142179) lub innej [maszyny stacjonarnej](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)  zawiera moduł o nazwie `diagnostics` , który zbiera i przekazuje dzienniki. Ten moduł jest domyślnie wyłączony i powinien być włączany za pomocą konfiguracji modułu IoT Edge, gdy trzeba uzyskać dostęp do dzienników. 

`diagnostics`Kolekcja jest na żądanie i kontrolowana za pomocą metody IoT Edge Direct i może wysyłać dzienniki do BLOB Storage platformy Azure.

### <a name="configure-diagnostics-upload-targets"></a>Skonfiguruj cele przekazywania diagnostyki

W portalu IoT Edge wybierz urządzenie, a następnie moduł **diagnostyki** . W przykładowym pliku manifestu wdrażania dla [Azure Stack urządzenia brzegowego](https://go.microsoft.com/fwlink/?linkid=2142179) lub innych [komputerów stacjonarnych](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)poszukaj sekcji **zmienne środowiskowe** dla celów diagnostycznych o nazwie `env` i Dodaj następujące informacje:

**Konfigurowanie przekazywania do usługi Azure Blob Storage**

1. Utwórz własne konto usługi Azure Blob Storage, jeśli jeszcze tego nie zrobiono.
2. Pobierz **Parametry połączenia** dla konta magazynu z Azure Portal. Zostanie ona umieszczona w **kluczach dostępu**.
3. Dzienniki analizy przestrzennej zostaną automatycznie przekazane do kontenera Blob Storage o nazwie *rtcvlogs* z następującym formatem nazwy pliku: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Przekazywanie dzienników analizy przestrzennej

Dzienniki są przekazywane na żądanie za pomocą `getRTCVLogs` metody IoT Edge w `diagnostics` module. 


1. Przejdź do strony portalu IoT Hub, wybierz pozycję **urządzenia brzegowe**, a następnie wybierz urządzenie i moduł diagnostyczny. 
2. Przejdź do strony szczegółów modułu i kliknij kartę **_Metoda bezpośrednia_*_.
3. Wpisz `getRTCVLogs` nazwę metody i ciąg formatu JSON w ładunku. Możesz wprowadzić `{}` , który jest pustym ładunkiem. 
4. Ustaw limity czasu połączenia i metody, a następnie kliknij _ * Invoke metoda * *.
5. Wybierz kontener docelowy i skompiluj ciąg JSON ładunku przy użyciu parametrów opisanych w sekcji **składnia rejestrowania** . Kliknij pozycję **Wywołaj metodę** , aby wykonać żądanie.

>[!NOTE]
> Wywołanie `getRTCVLogs` metody z pustym ładunkiem spowoduje zwrócenie listy wszystkich kontenerów wdrożonych na urządzeniu. Nazwa metody uwzględnia wielkość liter. Jeśli podano niepoprawną nazwę metody, zostanie wyświetlony błąd 501.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Raport telemetrii Azure Monitor":::
![Strona metody bezpośredniej getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Składnia rejestrowania

Poniższa tabela zawiera listę parametrów, których można użyć podczas wykonywania zapytania dotyczącego dzienników.

| Słowo kluczowe | Description | Wartość domyślna |
|--|--|--|
| StartTime | Czas rozpoczęcia żądanych dzienników (w milisekundach UTC). | `-1`, początek środowiska uruchomieniowego kontenera. Gdy `[-1.-1]` jest używany jako zakres czasu, interfejs API zwraca dzienniki z ostatniej godziny.|
| EndTime | Czas zakończenia żądanych dzienników (w milisekundach UTC). | `-1`, bieżąca godzina. Gdy `[-1.-1]` jest używany zakres czasu, interfejs API zwraca dzienniki z ostatniej godziny. |
| ContainerId | Kontener docelowy pobierania dzienników.| `null`, jeśli nie ma identyfikatora kontenera. Interfejs API zwraca wszystkie informacje o dostępnych kontenerach o identyfikatorach.|
| DoPost | Wykonaj operację przekazywania. Gdy ta wartość jest ustawiona na `false` , wykonuje żądaną operację i zwraca rozmiar przekazywania bez przeładowania. Po ustawieniu na `true` zostanie zainicjowane asynchroniczne przekazywanie wybranych dzienników | `false`, nie przekazuj.|
| Ograniczenie | Określ liczbę wierszy dzienników do przekazania na partię | `1000`Użyj tego parametru, aby dopasować szybkość post. |
| Filtry | Filtruje dzienniki do przekazania | `null`można określić filtry jako pary klucz wartość na podstawie struktury dzienników analizy przestrzennej: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Na przykład: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

W poniższej tabeli wymieniono atrybuty w odpowiedzi na zapytanie.

| Słowo kluczowe | Opis|
|--|--|
|DoPost| *Wartość true* lub *false*. Wskazuje, czy dzienniki zostały przekazane, czy nie. W przypadku wybrania opcji nie przekazuj dzienników interfejs API zwraca informacje ***synchronicznie**_. Po wybraniu przekazania dzienników interfejs API zwraca 200, jeśli żądanie jest prawidłowe i zaczyna przekazywania dzienników _*_asynchronicznie_*_.|
|TimeFilter| Filtr czasu zastosowany do dzienników.|
|ValueFilters| Filtry słów kluczowych zastosowane do dzienników. |
|Znacznik czasu| Godzina rozpoczęcia wykonywania metody. |
|ContainerId| Identyfikator kontenera docelowego. |
|FetchCounter| Łączna liczba wierszy dziennika. |
|FetchSizeInByte| Całkowita ilość danych dziennika w bajtach. |
|MatchCounter| Prawidłowa liczba wierszy dziennika. |
|MatchSizeInByte| Prawidłowa ilość danych dziennika w bajtach. |
|FilterCount| Łączna liczba wierszy dziennika po zastosowaniu filtru. |
|FilterSizeInByte| Całkowita ilość danych dziennika w bajtach po zastosowaniu filtru. |
|FetchLogsDurationInMiliSec| Czas trwania operacji pobierania. |
|PaseLogsDurationInMiliSec| Czas trwania operacji filtrowania. |
|PostLogsDurationInMiliSec| Czas trwania operacji post. |

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

Sprawdź wiersze, czasy i rozmiary dziennika pobierania, jeśli te ustawienia wyglądają na _*_DoPost_*_ `true` i które wypychają dzienniki z tymi samymi filtrami do miejsc docelowych. 

Podczas rozwiązywania problemów można eksportować dzienniki z Blob Storage platformy Azure. 

## <a name="common-issues"></a>Typowe problemy

Jeśli w dziennikach modułów zostanie wyświetlony następujący komunikat, może to oznaczać, że Twoja subskrypcja platformy Azure wymaga zatwierdzenia: 

"Kontener nie jest w prawidłowym stanie. Sprawdzanie poprawności subskrypcji nie powiodło się ze stanem "niezgodność". Klucz interfejsu API nie jest przeznaczony dla danego typu kontenera. "

Aby uzyskać więcej informacji, zobacz [żądanie zatwierdzenia w celu uruchomienia kontenera](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Rozwiązywanie problemów z urządzeniem Azure Stack Edge

Poniższa sekcja zawiera pomoc dotyczącą debugowania i weryfikacji stanu urządzenia brzegowego Azure Stack.

### <a name="access-the-kubernetes-api-endpoint"></a>Dostęp do punktu końcowego interfejsu API Kubernetes. 

1. W lokalnym interfejsie użytkownika urządzenia przejdź na stronę _*Devices**. 
2. W obszarze **punkty końcowe urządzenia**Skopiuj punkt końcowy usługi interfejsu API Kubernetes. Ten punkt końcowy jest ciągiem w następującym formacie: `https://compute..[device-IP-address]` .
3. Zapisz ciąg punktu końcowego. Będzie on używany później podczas konfigurowania programu `kubectl` w celu uzyskania dostępu do klastra Kubernetes.

### <a name="connect-to-powershell-interface"></a>Połącz z interfejsem programu PowerShell

Zdalnie, Połącz się z klienta systemu Windows. Po utworzeniu klastra Kubernetes można zarządzać aplikacjami za pośrednictwem tego klastra. Należy nawiązać połączenie z interfejsem programu PowerShell urządzenia. W zależności od systemu operacyjnego klienta procedury umożliwiające zdalne łączenie się z urządzeniem mogą się różnić. Poniższe kroki dotyczą klienta systemu Windows z uruchomionym programem PowerShell.

> [!TIP]
> * Przed rozpoczęciem upewnij się, że na kliencie systemu Windows jest uruchomiony program Windows PowerShell 5,0 lub nowszy.
> * Program PowerShell jest również [dostępny w systemie Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux).

1. Uruchom sesję programu Windows PowerShell jako administrator. 
    1. Upewnij się, że na kliencie jest uruchomiona usługa Windows Remote Management. W wierszu polecenia wpisz polecenie `winrm quickconfig` .

2. Przypisz zmienną dla adresu IP urządzenia. Na przykład `$ip = "<device-ip-address>"`.

3. Użyj poniższego polecenia, aby dodać adres IP urządzenia do listy zaufanych hostów klienta. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Uruchom sesję programu Windows PowerShell na urządzeniu. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Podaj hasło po wyświetleniu monitu. Użyj tego samego hasła, które jest używane do logowania się do lokalnego interfejsu sieci Web. Domyślnym hasłem lokalnego interfejsu internetowego jest `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Dostęp do klastra Kubernetes

Po utworzeniu klastra Kubernetes można użyć `kubectl` narzędzia wiersza polecenia, aby uzyskać dostęp do klastra.

1. Utwórz nową przestrzeń nazw. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Utwórz użytkownika i Pobierz plik konfiguracji. To polecenie spowoduje wyjście z informacji o konfiguracji dla klastra Kubernetes. Skopiuj te informacje i Zapisz je w pliku o nazwie *config*. Nie zapisuj pliku jako rozszerzenia pliku.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Dodaj plik *konfiguracji* do folderu *polecenia* w profilu użytkownika na komputerze lokalnym.   

4. Skojarz przestrzeń nazw z utworzonym użytkownikiem.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Zainstaluj program `kubectl` na kliencie systemu Windows przy użyciu następującego polecenia:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Dodaj wpis DNS do pliku hosts w systemie. 
    1. Uruchom program Notepad jako administrator i Otwórz plik *hosts* znajdujący się w lokalizacji `C:\windows\system32\drivers\etc\hosts` . 
    2. Utwórz wpis w pliku Hosts z adresem IP urządzenia i domeną DNS uzyskaną ze strony **urządzenia** w lokalnym interfejsie użytkownika. Punkt końcowy, który powinien być używany, będzie wyglądać podobnie do: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Sprawdź, czy można nawiązać połączenie z Kubernetesmi.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Aby uzyskać dzienniki kontenerów, uruchom następujące polecenie:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Przydatne polecenia

|Polecenie  |Opis  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Generuje plik konfiguracji Kubernetes. Korzystając z polecenia, skopiuj informacje do pliku o nazwie *config*. Nie zapisuj pliku z rozszerzeniem pliku.        |
| `Get-HcsApplianceInfo` | Zwraca informacje o urządzeniu. |
| `Enable-HcsSupportAccess` | Generuje poświadczenia dostępu w celu uruchomienia sesji obsługi. |

## <a name="next-steps"></a>Następne kroki

* [Wdróż aplikację sieci Web zliczania osób](spatial-analysis-web-app.md)
* [Konfigurowanie operacji analizy przestrzennej](./spatial-analysis-operations.md)
* [Przewodnik umieszczania w aparacie](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
