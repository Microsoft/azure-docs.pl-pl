---
title: Rozwiązywanie problemów z analizą filmów wideo na żywo na IoT Edge — Azure
description: W tym artykule opisano kroki rozwiązywania problemów z analizą filmów wideo na żywo na IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d23294c21d49b1c2ab83c4bf8f110d5d4bc7aafb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878294"
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

Jeśli infrastruktura brzegowa jest dobra, można wyszukać problemy z plikiem manifestu wdrożenia. Aby wdrożyć usługę analiza filmów wideo na żywo w IoT Edge module na urządzeniu IoT Edge, obok innych modułów IoT, należy użyć manifestu wdrażania, który zawiera IoT Edge Hub, IoT Edge agenta i innych modułów oraz ich właściwości. Aby wdrożyć plik manifestu, można użyć następującego polecenia:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Jeśli kod JSON nie jest poprawnie sformułowany, może zostać wyświetlony następujący błąd:   
&nbsp;&nbsp;&nbsp;**Nie można przeanalizować danych JSON z pliku: " <deployment manifest.json> " dla argumentu "Content", wyjątek: "ekstra dane: wiersz 101 kolumna 1 (Char 5325)"**

W przypadku wystąpienia tego błędu zalecamy sprawdzenie, czy kod JSON zawiera brakujące nawiasy lub inne problemy ze strukturą pliku. Aby sprawdzić poprawność struktury plików, można użyć klienta takiego jak [Notatnik + + z dodatkiem plug-in przeglądarki JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) lub narzędzia online, takiego jak program [formatujący JSON & moduł sprawdzania poprawności](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Podczas wdrażania: diagnozowanie przy użyciu metod bezpośrednich w programie Media Graph 

Po poprawnym wdrożeniu modułu IoT Edge wideo na żywo na urządzeniu z systemem IoT Edge można utworzyć i uruchomić Graf multimedialny, wywołując [metody bezpośrednie](direct-methods.md).  
>[!NOTE]
>  Wywołania metody bezpośredniej powinny być wykonywane tylko w **`lvaEdge`** module.

Możesz użyć Azure Portal, aby uruchomić diagnostykę grafu mediów przy użyciu metod bezpośrednich:

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

    > [!TIP]
    > Jeśli napotykasz problemy z uruchamianiem modułów Azure IoT Edge w środowisku, użyj **[Azure IoT Edge standardowych kroków diagnostycznych](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** jako przewodnika dotyczącego rozwiązywania problemów i diagnostyki.
### <a name="post-deployment-direct-method-error-code"></a>Po wdrożeniu: kod błędu metody bezpośredniej
1. W przypadku uzyskania stanu `501 code` Sprawdź, czy nazwa metody bezpośredniej jest dokładna. Jeśli nazwa metody i ładunek żądania są dokładne, należy uzyskać wyniki wraz z kodem sukcesu = 200. 
1. Jeśli ładunek żądania jest niedokładny, zostanie wyświetlony stan `400 code` i ładunek odpowiedzi wskazujący kod błędu i komunikat, który powinien pomóc w diagnozowaniu problemu z wywołaniem metody bezpośredniej.
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

    > [!TIP]
    > Jeśli napotykasz problemy z uruchamianiem modułów Azure IoT Edge w środowisku, użyj **[Azure IoT Edge standardowych kroków diagnostycznych](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** jako przewodnika dotyczącego rozwiązywania problemów i diagnostyki.
### <a name="live-video-analytics-working-with-external-modules"></a>Analiza wideo na żywo pracująca z modułami zewnętrznymi

Analiza wideo na żywo za pośrednictwem procesorów rozszerzeń grafu multimediów może rozszerzać wykres multimedialny, aby wysyłać i odbierać dane z innych modułów IoT Edge przy użyciu protokołów HTTP lub gRPC. Na [przykład](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)ten Graf multimedialny może wysyłać ramki wideo jako obrazy do modułu wnioskowania zewnętrznego, takiego jak Yolo v3 i odbierać wyniki analizy oparte na notacji JSON przy użyciu protokołu HTTP. W takiej topologii miejsce docelowe dla zdarzeń jest w większości Centrum IoT Hub. W sytuacjach, w których nie widzisz zdarzeń wnioskowania w centrum, sprawdź następujące kwestie:

* Zaznacz, aby sprawdzić, czy koncentrator jest publikowany przez program Media Graph, a centrum, które badasz, jest takie samo. Podczas tworzenia wielu wdrożeń można utworzyć wiele centrów i nieprawidłowo sprawdzić niewłaściwe centrum pod kątem zdarzeń.
* W Azure Portal Sprawdź, czy moduł zewnętrzny został wdrożony i uruchomiony. W przykładowym obrazie w tym miejscu rtspsim, yolov3, tinyyolov3 i logAnalyticsAgent są IoT Edge moduły działające poza modułem lvaEdge.

    [![Zrzut ekranu przedstawiający stan uruchomienia modułów w usłudze Azure IoT Hub. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Sprawdź, czy są wysyłane zdarzenia do prawidłowego punktu końcowego adresu URL. Zewnętrzny kontener AI udostępnia adres URL i port, za pośrednictwem którego odbiera i zwraca dane z żądań POST. Ten adres URL jest określony jako `endpoint: url` Właściwość procesora rozszerzenia http. Jak widać w [adresie URL topologii](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), punkt końcowy jest ustawiony na parametr adresu URL inferencing. Upewnij się, że wartość domyślna parametru lub wartości przekazywania jest dokładna. Możesz sprawdzić, czy działa on przy użyciu adresu URL klienta (zwinięcie).  

    Na przykład poniżej znajduje się kontener Yolo v3, który jest uruchomiony na komputerze lokalnym z adresem IP 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Zwrócony wynik:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Użyj **[polecenia Docker Inspekcja](https://docs.docker.com/engine/reference/commandline/inspect/)** , aby znaleźć adres IP maszyny.
    
* Jeśli uruchamiasz co najmniej jedno wystąpienie grafu, w którym jest używany procesor rozszerzenia wykresu multimediów, należy użyć `samplingOptions` pola do zarządzania szybkością klatek na sekundę (FPS) kanału wideo. 

   * W niektórych sytuacjach, w których procesor lub pamięć komputera brzegowego są wysoce wykorzystywane, można utracić pewne zdarzenia wnioskowania. Aby rozwiązać ten problem, Ustaw niską wartość `maximumSamplesPerSecond` właściwości w `samplingOptions` polu. Można ustawić ją na 0,5 ("maximumSamplesPerSecond": "0,5") w każdym wystąpieniu grafu, a następnie ponownie uruchomić wystąpienie, aby sprawdzić, czy istnieją zdarzenia wnioskowania w centrum.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Wiele metod bezpośrednich równolegle — niepowodzenie przekroczenia limitu czasu 

Usługa Analiza filmów wideo na żywo na IoT Edge zapewnia bezpośredni model programowania oparty na metodzie, który umożliwia skonfigurowanie wielu topologii i wystąpień wielu wykresów. W ramach konfiguracji topologii i wykresu można wywołać wiele wywołań metod bezpośrednich w module IoT Edge. W przypadku wywołania tych wielu metod równolegle, zwłaszcza tych, które uruchamiają i zatrzymują wykresy, może wystąpić błąd przekroczenia limitu czasu, który jest następujący: 

Metoda inicjująca zestaw Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync zgłosiła wyjątek. Microsoft. Azure. Devices. Common. Exceptions. IotHubException: Microsoft. Azure. Devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Zalecamy, aby metody bezpośrednie *nie* były wywoływane równolegle. Wywoływanie ich sekwencyjnie (to oznacza, że jedno wywołanie metody bezpośredniej jest wykonane tylko po zakończeniu poprzedniej operacji).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Zbieranie dzienników do przesyłania biletu pomocy technicznej

Gdy Samodzielne rozwiązywanie problemów nie rozwiąże problemu, przejdź do Azure Portal i [Otwórz bilet pomocy technicznej](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Dzienniki mogą zawierać dane osobowe, takie jak adres IP. Wszystkie lokalne kopie dzienników zostaną usunięte natychmiast po sprawdzeniu ich i zamknięciu biletu pomocy technicznej.  

Aby zebrać odpowiednie dzienniki, które należy dodać do biletu, postępuj zgodnie z poniższymi instrukcjami, aby przesłać pliki dziennika w okienku **szczegółów** żądania obsługi.  
1. [Konfigurowanie modułu analizy wideo na żywo w celu zbierania pełnych dzienników](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Włącz Dzienniki debugowania](#live-video-analytics-debug-logs)
1. Odtwórz problem
1. Nawiązywanie połączenia z maszyną wirtualną ze strony **IoT Hub** w portalu
    1. Wszystkie pliki w folderze *debugLogs* są zip.

       > [!NOTE]
       > Te pliki dziennika nie są przeznaczone do samodiagnostyki. Są one przeznaczone dla zespołu inżynierów platformy Azure, aby analizować swoje problemy.

       * W poniższym poleceniu Pamiętaj, aby zastąpić **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** lokalizacją dzienników debugowania na urządzeniu brzegowym, które zostało skonfigurowane wcześniej w **kroku 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Dołącz plik *debugLogs.zip* do biletu pomocy technicznej.
1. Uruchom [pakiet pomocy technicznej](#use-the-support-bundle-command), Zbierz dzienniki i Dołącz do biletu pomocy technicznej.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Konfigurowanie modułu analizy wideo na żywo w celu zbierania pełnych dzienników
Skonfiguruj dynamiczny moduł analizy filmów wideo, aby zbierać pełne dzienniki przez skonfigurowanie `logLevel` i `logCategories` w następujący sposób:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Można to zrobić w dowolnym:
* W **Azure Portal**, przez zaktualizowanie identyfikatora modułu właściwości dwuosiowego modułu modułu analiza filmów wideo na żywo   [ ![ . ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Lub w pliku **manifestu wdrożenia** można dodać te wpisy w węźle właściwości modułu analizy wideo na żywo

### <a name="use-the-support-bundle-command"></a>Korzystanie z polecenia support-Binding

Gdy zachodzi potrzeba zebrania dzienników z urządzenia IoT Edge, najprostszym sposobem jest użycie `support-bundle` polecenia. To polecenie zbiera dane:

- Dzienniki modułów
- Dzienniki programu IoT Edge Security Manager i aparatu kontenera
- IoT Edge sprawdzanie danych wyjściowych JSON
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
1. Wybierz pozycję **Przejrzyj i utwórz**. Komunikat o pomyślnym sprawdzeniu poprawności jest ogłaszany na zielonym banerze.
1. Wybierz przycisk **Utwórz**.
1. Zaktualizuj **sznury identyfikacyjne modułu** , aby wskazywały na DebugLogsDirectory parametr, który wskazuje katalog, w którym zbierane są dzienniki:

    a. W obszarze tabela **modułów** wybierz pozycję **lvaEdge**.  
    b. W górnej części okienka wybierz pozycję **moduł identyfikacji sznurka**. Zostanie otwarte okienko edytowalne.  
    c. W obszarze **żądany klucz** Dodaj następującą parę klucz/wartość:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > To polecenie wiąże foldery dzienników między urządzeniem brzegowym i kontenerem. Jeśli chcesz zebrać dzienniki w innej lokalizacji na urządzeniu:
    > 1. Utwórz powiązanie dla lokalizacji dziennika debugowania w sekcji **powiązania** , zastępując **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** i **$Debug _LOG_LOCATION** z wybraną lokalizacją: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Użyj następującego polecenia, zastępując **$DEBUG _LOG_LOCATION** lokalizacją używaną w poprzednim kroku:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Wybierz pozycję **Zapisz**.


1. Można zatrzymać zbieranie dzienników, ustawiając wartość w polu " **tożsamość modułu** " na *null*. Wróć do strony z niezależną **tożsamością modułu** i zaktualizuj następujący parametr jako:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Najlepsze rozwiązania dotyczące rejestrowania

[Monitorowanie i rejestrowanie](monitoring-logging.md) powinien pomóc w zrozumieniu taksonomii i sposobach generowania dzienników, które pomogą w debugowaniu problemów z LVA. 

Ponieważ implementacja serwera gRPC różni się w różnych językach, nie ma standardowego sposobu dodawania rejestrowania wewnątrz na serwerze.  

Jeśli na przykład utworzysz serwer gRPC przy użyciu platformy .NET Core, usługa gRPC dodaje dzienniki w kategorii **gRPC** . Aby włączyć szczegółowe dzienniki z gRPC, skonfiguruj prefiksy GRPC na poziomie debugowania w appsettings.jsna pliku, dodając następujące elementy do podsekcji LogLevel w obszarze Rejestrowanie: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Można to również skonfigurować w pliku Startup.cs z ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Rejestrowanie i Diagnostyka w programie gRPC na platformie .NET](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) zawiera wskazówki dotyczące zbierania niektórych dzienników diagnostycznych z serwera gRPC. 

### <a name="a-failed-grpc-connection"></a>Niepowodzenie połączenia gRPC 

Jeśli wykres jest aktywny i przesyła strumieniowo z aparatu, połączenie będzie utrzymywane przez działającą analizę wideo. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Monitorowanie i równoważenie obciążenia zasobów procesora i procesora GPU, gdy te zasoby staną się wąskimi gardłami

Analiza wideo na żywo nie monitoruje ani nie zapewnia monitorowania zasobów sprzętowych. Deweloperzy będą musieli korzystać z rozwiązań do monitorowania producentów sprzętu. Jeśli jednak korzystasz z kontenerów Kubernetes, możesz monitorować urządzenie przy użyciu [pulpitu nawigacyjnego Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC w dokumentach .NET Core również udostępniają pewne cenne informacje dotyczące najlepszych rozwiązań w zakresie [wydajności](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) i [równoważenia obciążenia](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Rozwiązywanie problemów z serwerem wnioskowania, gdy nie odebrano żadnych ramek i otrzymujesz komunikat o błędzie "nieznany" 

Istnieje kilka rzeczy, które można zrobić, aby uzyskać więcej informacji o problemie.  

* Uwzględnij kategorię "**ediaPipeline** log" w żądanych właściwościach modułu Live Video Analytics i upewnij się, że poziom dziennika jest ustawiony na `Information` .  
* Aby przetestować łączność sieciową, można uruchomić następujące polecenie z urządzenia brzegowego. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Jeśli polecenie wyprowadza krótki ciąg tekstu Jumbled, program Telnet pomyślnie mógł otworzyć połączenie z serwerem wnioskowania i otworzyć binarny kanał gRPC. Jeśli ta wartość nie jest wyświetlana, program Telnet zgłosi błąd sieciowy. 
* Na serwerze wnioskowania można włączyć dodatkowe rejestrowanie w bibliotece gRPC. Może to spowodować dodanie dodatkowych informacji o kanale gRPC. W ten sposób różnią się w zależności od języka, poniżej przedstawiono instrukcje w języku [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Pobieranie większej liczby obrazów z bufora gRPC bez wysyłania wyniku z tyłu dla pierwszego buforu

W ramach kontraktu gRPC Data Transfer wszystkie komunikaty wysyłane na żywo przez usługę wideo Analytics do serwera gRPC inferencing powinny zostać potwierdzone. Brak potwierdzenia, że otrzymanie ramki obrazu powoduje przerwanie działania kontraktu danych i może spowodować niepożądane sytuacje.  

Aby korzystać z serwera gRPC z analizą wideo na żywo, można użyć udostępnionej pamięci w celu uzyskania najlepszej wydajności. Wymaga to używania możliwości pamięci udostępnionej systemu Linux udostępnianej przez język programowania/środowisko. 

1. Otwórz uchwyt pamięci udostępnionej systemu Linux.
1. Po odebraniu ramki uzyskaj dostęp do przesunięcia adresu w pamięci współdzielonej.
1. Potwierdź zakończenie przetwarzania ramek, aby jego pamięć mogła zostać odczytana przez dynamiczną analizę wideo.

   > [!NOTE]
   > Jeśli opóźnisz, że otrzymanie ramki do analizy wideo na żywo przez dłuższy czas, może to spowodować zapełnienie pamięci współdzielonej i spowoduje to porzucanie danych.
1. Przechowuj każdą ramkę w wybranej strukturze danych (listę, tablicę itd.) na serwerze inferencing.
1. Następnie można uruchomić logikę przetwarzania, gdy liczba klatek obrazu jest wymagana.
1. Zwróć wynik inferencing z powrotem do analizy wideo na żywo, gdy wszystko będzie gotowe.

## <a name="next-steps"></a>Następne kroki

[Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury](event-based-video-recording-tutorial.md)