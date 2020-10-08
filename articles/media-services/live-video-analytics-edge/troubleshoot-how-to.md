---
title: Rozwiązywanie problemów z analizą filmów wideo na żywo na IoT Edge — Azure
description: W tym artykule opisano kroki rozwiązywania problemów z analizą filmów wideo na żywo na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823187"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Rozwiązywanie problemów z analizą filmów wideo na żywo na IoT Edge

W tym artykule opisano procedurę rozwiązywania problemów z usługą Live Video Analytics (LVA) na Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

### <a name="diagnostics"></a>Diagnostyka

W ramach wdrożenia analizy wideo na żywo można skonfigurować zasoby platformy Azure, takie jak IoT Hub i IoT Edge urządzeń. Jako pierwszy krok do diagnozowania problemów zawsze upewnij się, że urządzenie brzegowe jest prawidłowo skonfigurowane, wykonując następujące instrukcje:

1. [Uruchom `check` polecenie](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Sprawdź wersję IoT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Sprawdź stan programu IoT Edge Security Manager i jego dzienników](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Wyświetlanie komunikatów przechodzących przez centrum IoT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Ponownie uruchom kontenery](../../iot-edge/troubleshoot.md#restart-containers).
1. [Sprawdź reguły konfiguracji zapory i portów](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemy przed wdrożeniem

Jeśli infrastruktura brzegowa jest dobra, można wyszukać problemy z plikiem manifestu wdrożenia. Aby wdrożyć usługę analiza filmów wideo na żywo w IoT Edge module na urządzeniu IoT Edge, obok innych modułów IoT, należy użyć manifestu wdrażania, który zawiera IoT Edge Hub, IoT Edge agenta i innych modułów oraz ich właściwości. Jeśli kod JSON nie jest poprawnie sformułowany, może zostać wyświetlony następujący błąd: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nie można przeanalizować danych JSON z pliku: " <deployment manifest.json> " dla argumentu "Content", wyjątek: "ekstra dane: wiersz 101 kolumna 1 (char 5325)"

W przypadku wystąpienia tego błędu zalecamy sprawdzenie, czy kod JSON zawiera brakujące nawiasy lub inne problemy ze strukturą pliku. Aby sprawdzić poprawność struktury plików, można użyć klienta takiego jak [Notatnik + + z dodatkiem plug-in przeglądarki JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) lub narzędzia online, takiego jak program [formatujący JSON & moduł sprawdzania poprawności](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Podczas wdrażania: diagnozowanie przy użyciu metod bezpośrednich w programie Media Graph 

Po poprawnym wdrożeniu modułu IoT Edge wideo na żywo na urządzeniu z systemem IoT Edge można utworzyć i uruchomić Graf multimedialny, wywołując [metody bezpośrednie](direct-methods.md). Można użyć Azure Portal do uruchamiania diagnostyki wykresu multimedialnego za pomocą metod bezpośrednich:

1. W Azure Portal przejdź do centrum IoT Hub połączonego z urządzeniem IoT Edge.

1. Poszukaj opcji **Automatyczne zarządzanie urządzeniami**, a następnie wybierz **IoT Edge**.  

1. Na liście urządzeń brzegowych wybierz urządzenie, które chcesz zdiagnozować.  
         
    ![Zrzut ekranu przedstawiający Azure Portal wyświetlania listy urządzeń brzegowych](./media/troubleshoot-how-to/lva-sample-device.png)

1. Sprawdź, czy kod odpowiedzi to *200-OK*. Inne kody odpowiedzi dla [środowiska uruchomieniowego IoT Edge](../../iot-edge/iot-edge-runtime.md) obejmują:
    * 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
    * 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
    * 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
    * 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
    * 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

1. Jeśli zostanie wyświetlony kod stanu 501, sprawdź, czy nazwa metody bezpośredniej jest dokładna. Jeśli nazwa metody i ładunek żądania są dokładne, należy uzyskać wyniki wraz z kodem sukcesu = 200. Jeśli ładunek żądania jest niedokładny, otrzymasz stan = 400 i ładunek odpowiedzi wskazujący na kod błędu i komunikat, który powinien pomóc w diagnozowaniu problemu z wywołaniem metody bezpośredniej.
    * Sprawdzanie raportowanych i żądanych właściwości może pomóc zrozumieć, czy właściwości modułu zostały zsynchronizowane ze wdrożeniem. Jeśli nie, możesz ponownie uruchomić urządzenie IoT Edge. 
    * Skorzystaj z przewodnika po [metodach bezpośrednich](direct-methods.md) , aby wywołać kilka metod, szczególnie prostych, takich jak GraphTopologyList. Przewodnik określa również oczekiwane ładunki żądań i odpowiedzi oraz kody błędów. Po pomyślnym zaistnieniu prostych metod bezpośrednich można mieć pewność, że moduł IoT Edge analizy wideo na żywo jest funkcjonalnie prawidłowy.
        
       ![Zrzut ekranu przedstawiający okienko "Metoda bezpośrednia" dla modułu IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Jeśli **określone we wdrożeniu** i **raportowane przez kolumny urządzeń** wskazują *wartość tak*, można wywołać metody bezpośrednie na żywo analizy wideo w module IoT Edge. Wybierz moduł, aby przejść do strony, na której można sprawdzić odpowiednie i zgłoszone właściwości i wywołać metody bezpośrednie. Należy pamiętać o następujących elementach: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Po wdrożeniu: diagnozowanie dzienników pod kątem problemów podczas przebiegu 

Dzienniki kontenerów modułu IoT Edge powinny zawierać informacje diagnostyczne ułatwiające Debugowanie problemów podczas środowiska uruchomieniowego modułu. [Dzienniki kontenerów można sprawdzić pod kątem problemów](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) i samoobsługowego zdiagnozowania problemu. 

Jeśli wszystkie poprzednie testy zostały uruchomione i nadal występują problemy, Zbierz dzienniki z urządzenia IoT Edge [za pomocą `support bundle` polecenia](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) , aby przeprowadzić dalszą analizę według zespołu platformy Azure. Możesz [skontaktować się z nami](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) , aby uzyskać pomoc techniczną i przesłać zebrane dzienniki.

## <a name="common-error-resolutions"></a>Rozwiązania typowych błędów

Usługa Analiza filmów wideo na żywo jest wdrażana jako moduł IoT Edge na urządzeniu IoT Edge i współpracuje z modułami IoT Edge agenta i centrum. Niektóre typowe błędy, które można napotkać w przypadku wdrożenia analizy wideo na żywo, są spowodowane problemami z podstawową infrastrukturą IoT. Błędy obejmują:

* [Agent IoT Edge przestaje działać po około minucie](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [Agent IoT Edge nie może uzyskać dostępu do obrazu modułu (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Moduł agenta IoT Edge raportuje "pusty plik konfiguracji" i żadne moduły nie są uruchamiane na urządzeniu](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Nie można uruchomić centrum IoT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Demon zabezpieczeń IoT Edge kończy się niepowodzeniem z nieprawidłową nazwą hosta](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Nie można wysłać komunikatu do centrum brzegowego z błędem 404 przy użyciu usługi Live Video Analytics lub innego niestandardowego modułu IoT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Moduł IoT Edge został pomyślnie wdrożony, a następnie znika z urządzenia](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Problemy związane z skryptem Instalatora krawędzi

W ramach naszej dokumentacji udostępniono [skrypt instalacyjny](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) służący do wdrażania zasobów usługi Edge i w chmurze oraz rozpoczynania pracy z usługą Live Video Analytics Edge. W tej sekcji przedstawiono niektóre błędy skryptów, które mogą wystąpić, wraz z rozwiązaniami do debugowania.

Problem: skrypt działa, częściowo tworząc kilka zasobów, ale kończy się niepowodzeniem z następującym komunikatem:

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
1. Upewnij się, że masz zainstalowane następujące rozszerzenia. W przypadku publikacji tego artykułu rozszerzenia i ich wersje są następujące:

    | Wewnętrzny | Wersja |
    |---|---|
    |azure-cli   |      2.5.1|
    |Moduły Command-nspkg         |   2.0.3|
    |Procesor  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Jeśli masz zainstalowane rozszerzenie, którego wersja jest wcześniejsza niż numer wydania wymieniony w tym miejscu, zaktualizuj rozszerzenie przy użyciu następującego polecenia:

    ```
    az extension update --name <Extension name>
    ```

    Na przykład możesz uruchomić polecenie `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Problemy z przykładową aplikacją

W ramach naszej wersji podano jakiś przykładowy kod platformy .NET, aby pomóc Ci w rozpoczęciu pracy z naszą społeczność deweloperów. W tej sekcji przedstawiono niektóre błędy, które mogą wystąpić podczas uruchamiania przykładowego kodu, wraz z rozwiązaniami do debugowania.

Problem: Program.cs kończy się niepowodzeniem z powodu następującego błędu w wywołaniu metody bezpośredniej:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Upewnij się, że masz zainstalowane [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) w środowisku Visual Studio Code i że skonfigurowano połączenie z Centrum IoT. Aby to zrobić, wybierz kombinację klawiszy Ctrl + Shift + P, a następnie wybierz **pozycję wybierz IoT Hub metodę**.

1. Sprawdź, czy można wywołać metodę bezpośrednią w module IoT Edge za pośrednictwem Visual Studio Code. Na przykład Wywołaj GraphTopologyList z następującym ładunkiem { &nbsp; " @apiVersion ": "1,0"}. Powinna zostać wyświetlona następująca odpowiedź: 

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

    ![Zrzut ekranu przedstawiający odpowiedź w Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Jeśli poprzednie rozwiązanie nie powiedzie się, spróbuj wykonać następujące czynności:

    a. Przejdź do wiersza polecenia na urządzeniu IoT Edge i uruchom następujące polecenie:
    
      ```
      sudo systemctl restart iotedge
      ```

      To polecenie uruchamia ponownie urządzenie IoT Edge i wszystkie moduły. Poczekaj kilka minut, a następnie, zanim spróbujesz ponownie użyć metody bezpośredniej, potwierdź, że moduły są uruchomione, uruchamiając następujące polecenie:

      ```
      sudo iotedge list
      ```

    b. Jeśli poprzednie podejście również nie powiedzie się, spróbuj ponownie uruchomić maszynę wirtualną lub komputer.

    c. Jeśli wszystkie podejścia zakończą się niepowodzeniem, uruchom następujące polecenie, aby uzyskać plik skompresowany ze wszystkimi [odpowiednimi dziennikami](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)i dołączyć go do [biletu pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Jeśli zostanie wyświetlony kod z błędem *400* , upewnij się, że ładunek wywołania metody jest poprawnie sformułowany, zgodnie z przewodnikiem [bezpośrednich metod](direct-methods.md) .
1. Jeśli zostanie wyświetlony kod stanu *200* , oznacza to, że koncentrator działa prawidłowo, a wdrożenie modułu jest poprawne i trwa. 

1. Sprawdź, czy konfiguracja aplikacji jest dokładna. Konfiguracja aplikacji składa się z następujących pól w *appsettings.js* pliku. Sprawdź podwójne, aby upewnić się, że deviceId i moduleId są dokładne. Aby łatwo sprawdzić, przejdź do sekcji rozszerzenie usługi Azure IoT Hub w Visual Studio Code. Wartości w *appsettings.jsw* pliku oraz sekcja IoT Hub powinny być zgodne.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. W *appsettings.js* pliku upewnij się, że podano IoT Hub parametry połączenia, a *nie* parametry połączenia z urządzeniem IoT Hub, ponieważ [formaty parametrów połączenia są różne](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Analiza wideo na żywo pracująca z modułami zewnętrznymi

Analiza wideo na żywo za pośrednictwem procesora rozszerzeń HTTP może rozszerzać wykres multimedialny, aby wysyłać i odbierać dane z innych modułów IoT Edge za pośrednictwem protokołu HTTP przy użyciu interfejsu REST. Na [przykład](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)wykres multimedialny może wysyłać ramki wideo jako obrazy do modułu wnioskowania zewnętrznego, takiego jak Yolo v3 i odbierać wyniki analityczne oparte na notacji JSON. W takiej topologii miejsce docelowe dla zdarzeń jest w większości Centrum IoT Hub. W sytuacjach, w których nie widzisz zdarzeń wnioskowania w centrum, sprawdź następujące kwestie:

* Zaznacz, aby sprawdzić, czy koncentrator jest publikowany przez program Media Graph, a centrum, które badasz, jest takie samo. Podczas tworzenia wielu wdrożeń można utworzyć wiele centrów i nieprawidłowo sprawdzić niewłaściwe centrum pod kątem zdarzeń.
* W Visual Studio Code Sprawdź, czy moduł zewnętrzny został wdrożony i uruchomiony. W przykładowym obrazie rtspsim i CV są IoT Edge moduły działające poza modułem lvaEdge.

    ![Zrzut ekranu przedstawiający stan uruchomienia modułów w usłudze Azure IoT Hub.](./media/troubleshoot-how-to/iot-hub.png)

* Sprawdź, czy są wysyłane zdarzenia do prawidłowego punktu końcowego adresu URL. Zewnętrzny kontener AI udostępnia adres URL i port, za pośrednictwem którego odbiera i zwraca dane z żądań POST. Ten adres URL jest określony jako `endpoint: url` Właściwość procesora rozszerzenia http. Jak widać w [adresie URL topologii](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), punkt końcowy jest ustawiony na parametr adresu URL inferencing. Upewnij się, że wartość domyślna parametru lub wartości przekazywania jest dokładna. Możesz sprawdzić, czy działa on przy użyciu adresu URL klienta (zwinięcie).  

    Na przykład poniżej znajduje się kontener Yolo v3, który jest uruchomiony na komputerze lokalnym z adresem IP 172.17.0.3. Użyj inspekcji platformy Docker, aby znaleźć adres IP.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Zwrócony wynik:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Jeśli uruchamiasz co najmniej jedno wystąpienie grafu, który używa procesora rozszerzeń HTTP, należy mieć filtr szybkości klatek przed każdym procesorem rozszerzenia HTTP do zarządzania szybkością klatek na sekundę (FPS) kanału wideo. 

   W niektórych sytuacjach, w których procesor lub pamięć komputera brzegowego są wysoce wykorzystywane, można utracić pewne zdarzenia wnioskowania. Aby rozwiązać ten problem, Ustaw niską wartość właściwości maximumFps filtru szybkość klatek. Można ustawić ją na 0,5 ("maximumFps": 0,5) w każdym wystąpieniu grafu, a następnie ponownie uruchomić wystąpienie, aby sprawdzić zdarzenia wnioskowania w centrum.

   Alternatywnie możesz uzyskać bardziej wydajną maszynę brzegową z większym procesorem CPU i pamięcią.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Wiele metod bezpośrednich równolegle — niepowodzenie przekroczenia limitu czasu 

Usługa Analiza filmów wideo na żywo na IoT Edge zapewnia bezpośredni model programowania oparty na metodzie, który umożliwia skonfigurowanie wielu topologii i wystąpień wielu wykresów. W ramach konfiguracji topologii i wykresu można wywołać wiele wywołań metod bezpośrednich w module IoT Edge. W przypadku wywołania tych wielu metod równolegle, zwłaszcza tych, które uruchamiają i zatrzymują wykresy, może wystąpić błąd przekroczenia limitu czasu, który jest następujący: 

Metoda inicjująca zestaw Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync zgłosiła wyjątek. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Zalecamy, aby metody bezpośrednie *nie* były wywoływane równolegle. Wywoływanie ich sekwencyjnie (to oznacza, że jedno wywołanie metody bezpośredniej jest wykonane tylko po zakończeniu poprzedniej operacji).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Zbieranie dzienników do przesyłania biletu pomocy technicznej

Gdy Samodzielne rozwiązywanie problemów nie rozwiąże problemu, przejdź do Azure Portal i [Otwórz bilet pomocy technicznej](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Dzienniki mogą zawierać dane osobowe, takie jak adres IP. Wszystkie lokalne kopie dzienników zostaną usunięte natychmiast po sprawdzeniu ich i zamknięciu biletu pomocy technicznej.  

Aby zebrać odpowiednie dzienniki, które należy dodać do biletu, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach. Pliki dziennika można przekazać w okienku **szczegółów** żądania obsługi.

### <a name="use-the-support-bundle-command"></a>Korzystanie z polecenia support-Binding

Gdy zachodzi potrzeba zebrania dzienników z urządzenia IoT Edge, najprostszym sposobem jest użycie `support-bundle` polecenia. To polecenie zbiera dane:

- Dzienniki modułów
- Dzienniki programu IoT Edge Security Manager i aparatu kontenera
- Iotedge sprawdzanie danych wyjściowych JSON
- Przydatne informacje debugowania

1. Uruchom `support-bundle` polecenie z flagą *--od* , aby określić, ile czasu mają dotyczyć dzienniki. Na przykład, 2H będzie pobierać dzienniki z ostatnich dwóch godzin. Możesz zmienić wartość tej flagi, aby uwzględnić dzienniki dla różnych okresów.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   To polecenie tworzy plik o nazwie *support_bundle.zip* w katalogu, w którym uruchomiono polecenie. 
   
1. Dołącz plik *support_bundle.zip* do biletu pomocy technicznej.

### <a name="live-video-analytics-debug-logs"></a>Dzienniki debugowania analizy wideo na żywo

Aby skonfigurować usługę Live Video Analytics na IoT Edge module do generowania dzienników debugowania, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do centrum IoT Hub.
1. W okienku po lewej stronie wybierz pozycję **IoT Edge**.
1. Na liście urządzeń wybierz identyfikator urządzenia docelowego.
1. W górnej części okienka wybierz pozycję **Ustaw moduły**.

   ![Zrzut ekranu przedstawiający przycisk "Ustaw moduły" w Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. W sekcji **IoT Edge modułów** Wyszukaj i wybierz pozycję **lvaEdge**.
1. Wybierz pozycję **Opcje tworzenia kontenera**.
1. W sekcji **powiązania** Dodaj następujące polecenie:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > To polecenie wiąże foldery dzienników między urządzeniem brzegowym i kontenerem. Jeśli chcesz zebrać dzienniki w innej lokalizacji, użyj następującego polecenia, zastępując **$LOG _LOCATION_ON_EDGE_DEVICE** lokalizacją, której chcesz użyć: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Wybierz pozycję **Aktualizuj**.
1. Wybierz pozycję **Recenzja + Utwórz**. Komunikat o pomyślnym sprawdzeniu poprawności jest ogłaszany na zielonym banerze.
1. Wybierz pozycję **Utwórz**.
1. Zaktualizuj **sznury identyfikacyjne modułu** , aby wskazywały na DebugLogsDirectory parametr, który wskazuje katalog, w którym zbierane są dzienniki:

    a. W obszarze tabela **modułów** wybierz pozycję **lvaEdge**.  
    b. W górnej części okienka wybierz pozycję **moduł identyfikacji sznurka**. Zostanie otwarte okienko edytowalne.  
    c. W obszarze **żądany klucz**Dodaj następującą parę klucz/wartość:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > To polecenie wiąże foldery dzienników między urządzeniem brzegowym i kontenerem. Jeśli chcesz zebrać dzienniki w innej lokalizacji na urządzeniu:
    > 1. Utwórz powiązanie dla lokalizacji dziennika debugowania w sekcji **powiązania** , zastępując **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** i **$Debug _LOG_LOCATION** z wybraną lokalizacją: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Użyj następującego polecenia, zastępując **$DEBUG _LOG_LOCATION** lokalizacją używaną w poprzednim kroku:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Wybierz pozycję **Zapisz**.

1. Odtwórz problem.
1. Nawiąż połączenie z maszyną wirtualną ze strony **IoT Hub** w portalu.
1. Wszystkie pliki w folderze *debugLogs* są zip.

   > [!NOTE]
   > Te pliki dziennika nie są przeznaczone do samodiagnostyki. Są one przeznaczone dla zespołu inżynierów platformy Azure, aby analizować swoje problemy.

   a. W poniższym poleceniu Pamiętaj, aby zastąpić **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** lokalizacją dzienników debugowania na urządzeniu brzegowym skonfigurowanym wcześniej.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Dołącz plik *debugLogs.zip* do biletu pomocy technicznej.

1. Można zatrzymać zbieranie dzienników, ustawiając wartość w polu " **tożsamość modułu** " na *null*. Wróć do strony z niezależną **tożsamością modułu** i zaktualizuj następujący parametr jako:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Następne kroki

[Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury](event-based-video-recording-tutorial.md)
