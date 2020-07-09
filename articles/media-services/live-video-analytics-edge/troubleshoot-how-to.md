---
title: Rozwiązywanie problemów z analizą filmów wideo na żywo na IoT Edge — Azure
description: W tym artykule opisano kroki rozwiązywania problemów z analizą filmów wideo na żywo na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045584"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Rozwiązywanie problemów z analizą filmów wideo na żywo na IoT Edge

W tym artykule opisano kroki rozwiązywania problemów z analizą filmów wideo na żywo na IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

### <a name="diagnostics"></a>Diagnostyka

W ramach wdrażania usługi Live Video Analytics zostaną skonfigurowane zasoby platformy Azure, takie jak IoT Hub i IoT Edge. Pierwszym krokiem do diagnozowania problemów jest zawsze zagwarantowanie, że krawędzie są prawidłowo skonfigurowane, wykonując następujące instrukcje:

1. [Uruchom polecenie "Check"](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Sprawdź wersję IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Sprawdź stan programu IoT Edge Security Manager i jego dzienników](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Wyświetlanie komunikatów przechodzących przez Centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Uruchom ponownie kontenery](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Sprawdzanie reguł konfiguracji zapory i portów](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problemy przed wdrożeniem

Jeśli infrastruktura brzegowa jest dobra, można wyszukać problemy z plikiem manifestu wdrożenia. W celu wdrożenia usługi Analiza filmów wideo na żywo w module IoT Edge na urządzeniu brzegowym wraz z innymi modułami IoT zostanie użyty manifest wdrożenia zawierający centrum brzegowe, agenta brzegowego i inne moduły z ich właściwościami. Jeśli kod JSON nie jest poprawnie sformułowany, można uzyskać błąd w następujący sposób: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nie można przeanalizować danych JSON z pliku: " <deployment manifest.json> " dla argumentu "Content", wyjątek: "ekstra dane: wiersz 101 kolumna 1 (char 5325)"

Jeśli ten błąd wystąpi, zaleca się sprawdzenie pliku JSON pod kątem brakujących nawiasów lub innych problemów ze strukturą pliku. Możesz użyć klienta, takiego jak [Notatnik + + z dodatkiem przeglądarki JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) lub narzędzia online, na przykład https://jsonformatter.curiousconcept.com/ w celu zweryfikowania struktury plików.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Wdrażanie — diagnozowanie przy użyciu metod bezpośrednich w programie Media Graph 

Po poprawnym wdrożeniu modułu Microsoft Video Analytics na IoT Edge module na urządzeniu brzegowym można utworzyć i uruchomić Graf multimedialny, wywołując [metody bezpośrednie](direct-methods.md). Za pomocą portalu można uruchamiać diagnostykę grafu multimediów za pomocą metod bezpośrednich:

1. Za pośrednictwem portalu przejdź do IoT Hub, który jest połączony z urządzeniem brzegowym.
    1. Raz w bloku IoT Hub Wyszukaj pozycję Automatyczne zarządzanie urządzeniami — >IoT Edge.
    1. Kliknięcie IoT Edge powinno spowodować wyświetlenie listy urządzeń brzegowych. Wybierz urządzenie, które chcesz zdiagnozować.
         
        ![Urządzenia brzegowe](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Sprawdź, czy kod odpowiedzi to 200-OK. Istnieją różne kody odpowiedzi dla [środowiska uruchomieniowego IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , takie jak:
        1. 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
        1. 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
        1. 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
        1. 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
        1. 500 — Wystąpił błąd w czasie wykonywania IoT Edge.
    1. Kliknięcie urządzenia powinno również zawierać listę oczekiwanych modułów IoT Edge, które zostały wdrożone i ich stan
    1. Jeśli kolumny "określone we wdrożeniu" i "zgłoszone przez urządzenie" wskazują wartość "tak", można wywołać metody bezpośrednie na żywo analizy filmów wideo w module IoT Edge. Kliknij moduł i przejdziesz do ekranu, na którym możesz sprawdzić odpowiednie i zgłoszone właściwości i można wywołać metody bezpośrednie. 
        1. Sprawdzanie raportowanych i żądanych właściwości pomaga zrozumieć, czy właściwości modułu zostały zsynchronizowane ze wdrożeniem. Jeśli nie, możesz ponownie uruchomić brzeg 
        1. Skorzystaj z przewodnika po [metodach bezpośrednich](direct-methods.md) , aby wywołać kilka metod, szczególnie prostych, takich jak GraphTopologyList. Przewodnik określa również oczekiwane ładunki żądań i odpowiedzi oraz kody błędów. Gdy proste metody bezpośrednie zakończą się pomyślnie, można upewnić się, że moduł Edge usługi Live Video Analytics jest funkcjonalnie prawidłowy.
        
        ![Metoda bezpośrednia](./media/troubleshoot-how-to/direct-method.png) 
1. Jeśli zostanie wyświetlony kod stanu 501, sprawdź, czy nazwa metody bezpośredniej jest dokładna. Jeśli nazwa metody i ładunek żądania są dokładne, należy uzyskać wyniki z powrotem wraz z kodem sukcesu = 200. Jeśli ładunek żądania jest niedokładny, otrzymasz stan = 400 i ładunek odpowiedzi wskazujący na kod błędu i komunikat, który powinien pomóc w diagnozowaniu problemu z wywołaniem metody bezpośredniej. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Po wdrożeniu — diagnozowanie dzienników pod kątem problemów podczas przebiegu 

Dzienniki kontenerów dla naszego modułu brzegowego powinny mieć wartość Diagnostic<!--<todo:add link to diagnostics doc>--> informacje, które powinny ułatwić debugowanie Twoich problemów podczas środowiska uruchomieniowego modułu. Jeśli wszystkie powyższe sprawdzenia zostały wykonane i nadal występują problemy, można [sprawdzić dzienniki kontenerów pod kątem problemów](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) i samodzielnej diagnostyki, a następnie zebrać dzienniki z urządzenia IoT Edge [z użyciem polecenia "pakiet pomocy technicznej"](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) , które można analizować w dalszym ciągu przez zespół platformy Azure. Możesz [skontaktować](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) się z nami, aby uzyskać pomoc techniczną i przesłać zebrane dzienniki.

## <a name="common-error-resolutions"></a>Rozwiązania typowych błędów

Usługa Analiza filmów wideo na żywo jest wdrażana jako moduł IoT Edge na urządzeniu brzegowym i współpracuje z modułami IoT Edge agenta i centrum. Niektóre typowe błędy, które należy wykonać w przypadku wdrożenia analizy wideo na żywo, będą spowodowane problemami związanymi z podstawową infrastrukturą IoT. Oto niektóre typowe błędy, które mogą mieć IoT Edge agenci i centrum:

1. [Agent IoT Edge przestaje działać po około minucie](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [W module agenta brzegowego są raportowane puste pliki konfiguracji i nie uruchomiono żadnych modułów na urządzeniu](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Nie można uruchomić centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IoT Edge zabezpieczeń nie powiodło się z użyciem nieprawidłowej nazwy hosta](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Nie można wysłać komunikatu do centrum brzegowego przy 404 użyciu usługi Live Video Analytics lub innego niestandardowego modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [Pomyślnie wdrożenia modułów IoT Edge zostaną wyświetlone z urządzenia](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Problemy ze skryptami konfigurowania krawędzi

W ramach naszej dokumentacji podano [skrypt skonfigurowany](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) do wdrażania zasobów brzegowych i w chmurze, aby rozpocząć pracę z krawędzią analizy wideo na żywo. W tej sekcji wystąpiły błędy, które mogą być widoczne dla skryptu i sposobu ich debugowania.

Skrypt działa częściowo podczas tworzenia kilku zasobów, ale kończy się niepowodzeniem z następującym komunikatem:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Aby rozwiązać ten problem:

1. Uruchom następujące polecenie:

    ```
    az --version
    ```
1. Upewnij się, że masz zainstalowane następujące rozszerzenia. W przypadku pisania w tym przewodniku wersja rozszerzeń była następująca:

    | Wewnętrzny | Wersja |
    |---|---|
    |azure-cli   |      2.5.1|
    |Moduły Command-nspkg         |   2.0.3|
    |Procesor  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetrii| 1.0.4|
    |Rozszerzenia    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Jeśli którekolwiek z rozszerzeń są starsze niż powyższe numery wydań, zaktualizuj rozszerzenie do najnowszej wersji za pomocą polecenia:

    ```
    az extension update --name <Extension name>
    ```

    Na przykład: `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Problemy z przykładową aplikacją

W ramach naszej wersji podano jakiś przykładowy kod platformy .NET, aby uzyskać informacje na temat uruchamiania społeczności deweloperów. Ta sekcja zawiera przechwycone błędy, które można napotkać podczas uruchamiania przykładowego kodu i debugowania takich błędów.

1. Program.cs kończy się niepowodzeniem z powodu następującego błędu w wywołaniu metody bezpośredniej:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Upewnij się, że masz zainstalowane [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) w środowisku programu vs Code i połączenie z konfiguracją IoT Hub. (Ctrl + Shift + P, a następnie wybierz IoT Hub metodę, aby nawiązać połączenie z subskrypcją i IoT Hub)
1. Sprawdź, czy można wywołać metodę bezpośrednią w module brzegowym za pośrednictwem VS Code (na przykład Wywołaj GraphToplogyList z następującym ładunkiem {" @apiVersion ": "1,0"}) i poproś o poniższą odpowiedź. 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Jeśli powyższe polecenie zakończy się niepowodzeniem, spróbuj wykonać następujące czynności:
    1. Przejdź do wiersza polecenia na urządzeniu brzegowym i wpisz.
    
    ```
    sudo systemctl restart iotedge
    ```

    Spowoduje to ponowne uruchomienie urządzenia brzegowego i wszystkich modułów. Poczekaj kilka minut i uruchom następujące polecenie, aby upewnić się, że moduły są uruchomione, a następnie spróbuj ponownie użyć DirectMethod.

    ```
    sudo iotedge list
    ```
    1. Jeśli powyższe nie powiedzie się, spróbuj ponownie uruchomić maszynę wirtualną lub maszynę.
    1. Jeśli wszystko nie powiedzie się, uruchom następujące polecenie, aby uzyskać plik ZIP ze wszystkimi [odpowiednimi dziennikami](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) , które zostaną dołączone do tego [biletu pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Jeśli otrzymasz kod z odpowiedzią na błąd 400, upewnij się, że ładunek wywołania metody jest poprawnie sformułowany zgodnie z przewodnikiem [metody bezpośredniej](direct-methods.md) .
1. Jeśli zostanie wyświetlony kod stanu 200, oznacza to, że koncentrator działa prawidłowo, a wdrożenie modułu jest poprawne i trwa. Następnym krokiem jest sprawdzenie, czy konfiguracje aplikacji są dokładne. Konfiguracja aplikacji składa się z następujących pól w appsettings.jspliku. Sprawdź dokładnie, czy deviceId i moduleId są dokładne. Można to łatwo sprawdzić za pośrednictwem sekcji rozszerzenia usługi Azure IoT Hub w programu vscode. Wartości w appsettings.jsw pliku oraz sekcja IoT Hub powinny być zgodne.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![CENTRUM IOT](./media/troubleshoot-how-to/iot-hub.png)

1. Na koniec upewnij się, że w appsettings.jsIoT Hub parametry połączenia, a nie parametry połączenia z urządzeniem IoT Hub, ponieważ ich [formaty](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) są różne.

### <a name="live-video-analytics-working-with-external-modules"></a>Analiza wideo na żywo pracująca z modułami zewnętrznymi

Analiza wideo na żywo za pośrednictwem procesora rozszerzeń HTTP może rozszerzać wykres multimedialny, aby wysyłać i odbierać dane z innych modułów IoT Edge za pośrednictwem protokołu HTTP przy użyciu interfejsu REST.  [Przykładowy](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) wykres multimedialny może wysyłać ramki wideo jako obrazy do modułu wnioskowania zewnętrznego, takiego jak Yolo v3, i odbierać wyniki analizy na podstawie JSON. W takiej topologii miejsce docelowe dla zdarzeń jest w większości IoT Hub. W sytuacjach, w których nie widzisz zdarzeń wnioskowania w centrum, sprawdź następujące kwestie:

1. Sprawdź, czy koncentrator, który jest publikowany przez program Media Graph, jest taki sam. Czasami podczas tworzenia wielu wdrożeń zostanie utworzone wiele centrów i może być błędnie sprawdzać niewłaściwe centrum pod kątem zdarzeń.
1. Sprawdź za pośrednictwem programu vscode, czy moduł zewnętrzny został wdrożony i uruchomiony. W przykładowym obrazie rtspsim i CV są IoT Edge moduły działające poza modułem lvaEdge.

    ![CENTRUM IOT](./media/troubleshoot-how-to/iot-hub.png)
1. Sprawdź, czy wysyłasz zdarzenia do prawidłowego punktu końcowego adresu URL. Zewnętrzny kontener AI ujawnia adres URL i port, za pośrednictwem którego otrzymuje i zwraca dane z żądań POST. Ten adres URL jest określony jako właściwość adresu URL dla procesora rozszerzenia http. Jak widać w [adresie URL topologii](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) jest ustawiony na parametr adresu URL inferencing. Upewnij się, że wartość domyślna parametru ( http://yolov3/score) lub przekazana wartość jest dokładna i można ją testować, jeśli działa przy użyciu zwinięcia.  
    1. Przykładowo kontener Yolo v3 uruchomiony na komputerze lokalnym, a adres IP dla kontenera to 172.17.0.3 (Użyj usługi Docker Sprawdź, aby znaleźć adres IP).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Zwrócony wynik:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Jeśli uruchamiasz co najmniej jedno wystąpienie grafu wykorzystujące procesor rozszerzenia http, należy mieć filtr szybkości klatek przed każdym procesorem rozszerzenia http, aby zarządzać klatkami na sekundę (FPS) kanału wideo. W niektórych sytuacjach, gdy procesor CPU/pamięć maszyny brzegowej jest wysoce wykorzystywany, można utracić niektóre zdarzenia wnioskowania. Aby rozwiązać ten fakt, Ustaw niską wartość właściwości maximumFps filtru szybkość klatek. Można ustawić ją na 0,5 ("maximumFps": 0,5) w każdym wystąpieniu wykresu i ponownie uruchomić w celu sprawdzenia zdarzeń wnioskowania w centrum.
    1. Alternatywnie można również uzyskać bardziej wydajną maszynę brzegową z większym procesorem CPU i pamięcią.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Wiele metod bezpośrednich równolegle — niepowodzenie przekroczenia limitu czasu 

Usługa Analiza filmów wideo na żywo na IoT Edge zapewnia bezpośredni model programowania oparty na metodzie, który umożliwia konfigurowanie wielu topologii i wystąpień wielu wykresów. W ramach konfiguracji topologii i wykresu zostanie wywołane wiele wywołań metody bezpośredniej w module Edge. Jeśli wywołasz te wywołania wielu metod, szczególnie te, które zaczynają i zatrzymują wykresy, mogą wystąpić pewne błędy przekroczenia limitu czasu, takie jak poniżej. 

Metoda inicjująca zestaw Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync zgłosiła wyjątek. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Zaleca się, aby nie należy wywoływać metod bezpośrednich w sposób równoległy, ale należy to zrobić w sposób sekwencyjny, tj.  jedno wywołanie metody bezpośredniej tylko po zakończeniu poprzedniego.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Zbieranie dzienników do przesyłania biletu pomocy technicznej

Gdy Samodzielne rozwiązywanie problemów nie rozwiąże problemów, należy przejść do Azure Portal i [otworzyć bilet pomocy technicznej](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Wykonaj następujące kroki, aby zebrać odpowiednie dzienniki, które należy dodać do biletu. Pliki dziennika można przekazać na karcie **szczegóły** żądania obsługi.

### <a name="support-bundle"></a>Obsługa — pakiet

Gdy zachodzi potrzeba zebrania dzienników z urządzenia IoT Edge, najprostszym sposobem jest użycie `support-bundle` polecenia. To polecenie zbiera dane:

- Dzienniki modułów
- Dzienniki programu IoT Edge Security Manager i aparatu kontenera
- Iotedge sprawdzanie danych wyjściowych JSON
- Przydatne informacje debugowania

#### <a name="use-the-iot-edge-security-manager"></a>Korzystanie z programu IoT Edge Security Manager
 
IoT Edge Security Manager jest odpowiedzialny za operacje, takie jak inicjowanie systemu IoT Edge przy uruchamianiu i aprowizacji urządzeń. Jeśli IoT Edge nie zostanie uruchomiona, dzienniki Menedżera zabezpieczeń mogą dostarczyć przydatne informacje. Aby wyświetlić bardziej szczegółowe dzienniki programu IoT Edge Security Manager:

1. Edytuj ustawienia demona IoT Edge na urządzeniu IoT Edge:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Zaktualizuj następujące wiersze:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Uruchom ponownie demona Security IoT Edge, uruchamiając następujące polecenia:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Uruchom `support-bundle` polecenie z flagą--od, aby określić, jak długo od dawna chcesz pobrać dzienniki. Na przykład, 2H będzie pobierać dzienniki od ostatnich dwóch godzin. Można zmienić wartość tej flagi, aby uwzględnić dzienniki w innym okresie.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Dzienniki debugowania LVA

Wykonaj następujące kroki, aby skonfigurować LVA IoT Edge module do generowania dzienników debugowania:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Kliknij link **Ustaw moduły** w górnym menu.

  ![Ustawianie modułów w witrynie Azure Portal](media/troubleshoot-how-to/set-modules.png)

5. W sekcji IoT Edge modułów Znajdź i kliknij pozycję **lvaEdge**.
1. Kliknij pozycję **kontener Utwórz opcje**.
1. W sekcji powiązania Dodaj następujące polecenie:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    To wiąże foldery dzienników między urządzeniem brzegowym i kontenerem.

1. Kliknij przycisk **Aktualizuj**
1. Kliknij przycisk **Recenzja + Utwórz** w dolnej części strony. Prosta weryfikacja zostanie przeprowadzona i po pomyślnym zakończeniu walidacji zostanie wyświetlony zielony baner.
1. Kliknij przycisk **Utwórz** .
1. Następnie zaktualizuj **sznury identyfikatora modułu** , aby wskazywały parametr DebugLogsDirectory, aby wskazywał katalog, w którym będą zbierane dzienniki:
    1. W tabeli **moduły** wybierz pozycję **lvaEdge** .
    1. Kliknij link do **identyfikacji modułu** . Znajdziesz to w górnej części strony. Spowoduje to otwarcie okienka edytowalnego.
    1. Dodaj następującą parę klucz-wartość w **odpowiednim kluczu**:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Kliknij pozycję **Zapisz**.

1. Odtwórz problem.
1. Połącz się z maszyną wirtualną ze strony IoT Hub w portalu.
1. Przejdź do `/var/local/mediaservices/logs` folderu, a następnie zawartość pliku zip tego folderu i udostępnij go nam. (Te pliki dziennika nie są przeznaczone do samodiagnostyki. Są one przeznaczone dla inżynierów platformy Azure, aby analizować swoje problemy.

1. Zbieranie dzienników można zatrzymać, ustawiając tę wartość w polu " **tożsamość modułu** " do wartości *null* . Wróć do strony z niezależną **tożsamością modułu** i zaktualizuj następujący parametr jako:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Następne kroki

[Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury](event-based-video-recording-tutorial.md)
