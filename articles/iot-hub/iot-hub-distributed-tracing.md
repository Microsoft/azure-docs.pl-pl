---
title: Dodawanie identyfikatorów korelacji do komunikatów IoT w/rozproszonym śledzeniu (pre)
description: Dowiedz się, jak korzystać z możliwości śledzenia rozproszonego do śledzenia komunikatów IoT w usługach platformy Azure używanych przez rozwiązanie.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b1dc7873140f885ec3efac11dec5fbf6aab7aa9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732574"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Śledzenie wiadomości z urządzenia usługi Azure IoT do chmury za pomocą śledzenia rozproszonego (wersja zapoznawcza)

Usługa Microsoft Azure IoT Hub obsługuje obecnie śledzenie rozproszone jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Usługa IoT Hub jest jedną z pierwszych usług platformy Azure obsługujących śledzenie rozproszone. Ponieważ więcej usług platformy Azure obsługuje śledzenie rozproszone, będziesz mógł śledzić komunikaty IoT w usługach platformy Azure zaangażowanych w rozwiązanie. Aby uzyskać tło dotyczące śledzenia rozproszonego, zobacz [Śledzenie rozproszone](../azure-monitor/app/distributed-tracing.md).

Włączenie śledzenia rozproszonego dla usługi IoT Hub umożliwia:

- Dokładnie monitoruj przepływ każdej wiadomości za pośrednictwem usługi IoT Hub przy użyciu [kontekstu śledzenia](https://github.com/w3c/trace-context). Ten kontekst śledzenia zawiera identyfikatory korelacji, które umożliwiają skorelowanie zdarzeń z jednego składnika ze zdarzeniami z innego składnika. Można go zastosować do podzbioru lub wszystkich komunikatów urządzenia IoT przy użyciu [bliźniaczej reprezentacji urządzenia.](iot-hub-devguide-device-twins.md)
- Automatycznie rejestruj kontekst śledzenia w [dziennikach diagnostycznych usługi Azure Monitor](iot-hub-monitor-resource-health.md).
- Mierzenie i rozumienie przepływu i opóźnienia komunikatów z urządzeń do usługi IoT Hub i punktów końcowych routingu.
- Zacznij rozważać, jak chcesz zaimplementować śledzenie rozproszone dla usług innych niż Azure w rozwiązaniu IoT.

W tym artykule używasz [zestawu SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md) z śledzeniem rozproszonym. Obsługa śledzenia rozproszonego jest nadal w toku dla innych SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- Podgląd śledzenia rozproszonego jest obecnie obsługiwany tylko dla centrów IoT utworzonych w następujących regionach:

  - **Europa Północna**
  - **Azja Południowo-Wschodnia**
  - **Zachodnie stany USA 2**

- W tym artykule przyjęto założenie, że znasz wysyłanie wiadomości telemetrycznych do centrum IoT Hub. Upewnij się, że ukończono przewodnik [Szybki start w trybie wyślij dane telemetryczne C](quickstart-send-telemetry-c.md).

- Zarejestruj urządzenie w centrum IoT hub (kroki dostępne w każdym przewodniku Szybki start) i zanotuj ciąg połączenia.

- Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurowanie Centrum IoT

W tej sekcji można skonfigurować Centrum IoT do rejestrowania atrybutów śledzenia rozproszonego (identyfikatory korelacji i sygnatury czasowe).

1. Przejdź do centrum IoT hub w [witrynie Azure portal](https://portal.azure.com/).

1. W lewym okienku centrum IoT przewiń w dół do sekcji **Monitorowanie** i kliknij pozycję **Ustawienia diagnostyki**.

1. Jeśli ustawienia diagnostyczne nie są jeszcze włączone, kliknij pozycję **Włącz diagnostykę**. Jeśli ustawienia diagnostyczne zostały już włączone, kliknij przycisk **Dodaj ustawienie diagnostyczne**.

1. W polu **Nazwa** wprowadź nazwę nowego ustawienia diagnostycznego. Na przykład **RozproszoneTracingSettings**.

1. Wybierz jedną lub więcej z następujących opcji, które określają, gdzie rejestrowanie zostanie wysłane:

    - **Archiwum na koncie magazynu**: Skonfiguruj konto magazynu, aby zawierało informacje o rejestrowaniu.
    - **Strumień do centrum zdarzeń:** Skonfiguruj centrum zdarzeń, aby zawierało informacje o rejestrowaniu.
    - **Wyślij do usługi Log Analytics:** Skonfiguruj obszar roboczy analizy dzienników, aby zawierał informacje o rejestrowaniu.

1. W sekcji **Dziennik** wybierz operacje, dla których chcesz rejestrować informacje.

    Pamiętaj, aby uwzględnić **distributedTracing**i skonfigurować **retencji** dla liczby dni, które mają być zachowane rejestrowania. Przechowywanie dziennika ma wpływ na koszty magazynu.

    ![Zrzut ekranu przedstawiający, gdzie znajduje się kategoria DistributedTracing dla ustawień diagnostycznych IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kliknij **przycisk Zapisz** dla nowego ustawienia.

1. (Opcjonalnie) Aby wyświetlić przepływ wiadomości do różnych miejsc, należy [skonfigurować reguły routingu do co najmniej dwóch różnych punktów końcowych](iot-hub-devguide-messages-d2c.md).

Po włączeniu rejestrowania Usługa IoT Hub rejestruje dziennik, gdy w dowolnej z następujących sytuacji napotkano komunikat zawierający prawidłowe właściwości śledzenia:

- Wiadomości docierają do bramy usługi IoT Hub.
- Komunikat jest przetwarzany przez Centrum IoT.
- Wiadomość jest kierowana do niestandardowych punktów końcowych. Routing musi być włączony.

Aby dowiedzieć się więcej o tych dziennikach i ich schematach, zobacz [Śledzenie rozproszone w dziennikach diagnostycznych usługi IoT Hub.](iot-hub-monitor-resource-health.md#distributed-tracing-preview)

## <a name="set-up-device"></a>Konfigurowanie urządzenia

W tej sekcji należy przygotować środowisko programistyczne do użycia z [zestawem SDK C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Następnie należy zmodyfikować jedną z próbek, aby włączyć śledzenie rozproszone w komunikatach telemetrycznych urządzenia.

Te instrukcje służą do tworzenia przykładu w systemie Windows. W przypadku innych środowisk zobacz [Kompilowanie zestawu SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) lub [wstępnie spakowanego SDK C dla rozwoju specyficznego dla platformy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonowanie kodu źródłowego i inicjowanie

1. Zainstaluj [zadanie "Tworzenie pulpitu z c++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) dla programu Visual Studio 2019. Visual Studio 2017 i 2015 są również obsługiwane.

1. Zainstaluj [CMake](https://cmake.org/). Upewnij się, że `PATH` znajduje `cmake -version` się w wierszu polecenia.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium GitHub [SDK usługi Azure IoT C:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Jeśli `cmake` nie można znaleźć kompilatora Języka C++, może pojawić się błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Edytowanie próbki telemetrii wysyłania w celu włączenia śledzenia rozproszonego

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Pobierz próbkę w usłudze GitHub</a>

1. Użyj edytora, `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` aby otworzyć plik źródłowy.

1. Znajdź deklarację stałej `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Zastąp `connectionString` wartość stałej ciągiem połączenia urządzenia, o których zanotowano w sekcji [rejestru urządzenia](./quickstart-send-telemetry-c.md#register-a-device) [szybki start w trybie "Wyślij dane telemetryczne C".](./quickstart-send-telemetry-c.md)

1. Zmień `MESSAGE_COUNT` definicję `5000`na:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Znajdź wiersz kodu, `IoTHubDeviceClient_LL_SetConnectionStatusCallback` który wywołuje, aby zarejestrować funkcję wywołania zwrotnego stanu połączenia przed pętlą wysyłania wiadomości. Dodaj kod pod tym wierszem, jak pokazano poniżej, aby wywołać `IoTHubDeviceClient_LL_EnablePolicyConfiguration` włączenie śledzenia rozproszonego dla urządzenia:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    Funkcja `IoTHubDeviceClient_LL_EnablePolicyConfiguration` umożliwia zasady dla określonych funkcji IoTHub, które są konfigurowane za pomocą [bliźniąt urządzeń.](./iot-hub-devguide-device-twins.md) Po `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` włączeniu z wierszem kodu powyżej, zachowanie śledzenia urządzenia będzie odzwierciedlać rozproszone zmiany śledzenia wprowadzone na bliźniaczej reprezentacji urządzenia.

1. Aby zachować uruchamianie przykładowej aplikacji bez użycia całego przydziału, dodaj jednosekundowe opóźnienie na końcu pętli wiadomości wysyłania:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilowanie i uruchamianie

1. Przejdź do katalogu projektu *iothub_ll_telemetry_sample* z katalogu`azure-iot-sdk-c/cmake`CMake utworzonego wcześniej i skompiluj przykład:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Uruchom aplikację. Urządzenie wysyła dane telemetryczne obsługujące śledzenie rozproszone.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Utrzymuj aplikację w pracy. Opcjonalnie należy obserwować komunikat wysyłany do usługi IoT Hub, patrząc na okno konsoli.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Obejście dla klientów innych firm

Podgląd funkcji śledzenia rozproszonego bez użycia SDK C nie jest **trywialny.** W związku z tym takie podejście nie jest zalecane.

Najpierw należy zaimplementować wszystkie podstawowe promity protokołu Usługi IoT Hub w wiadomościach, postępując zgodnie z przewodnikiem deweloperskim [Tworzenie i odczytywanie komunikatów Usługi IoT Hub](iot-hub-devguide-messages-construct.md). Następnie edytuj właściwości protokołu w komunikatach MQTT/AMQP, aby dodać `tracestate` je jako **właściwość systemu**. Są to:

* W przypadku MQTT dodaj `%24.tracestate=timestamp%3d1539243209` do `1539243209` tematu wiadomości, gdzie należy zastąpić czas utworzenia wiadomości w formacie sygnatury czasowej unix. Na przykład należy zapoznać się z implementacją [w seddku C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* W przypadku usługi `key("tracestate")` AMQP dodaj i `value("timestamp=1539243209")` jako adnotację wiadomości. Aby uzyskać implementację referencyjną, zobacz [tutaj](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Aby kontrolować procent komunikatów zawierających tę właściwość, należy zaimplementować logikę do nasłuchiwania zdarzeń inicjowanych w chmurze, takich jak aktualizacje bliźniacze.

## <a name="update-sampling-options"></a>Aktualizowanie opcji próbkowania 

Aby zmienić procent wiadomości, które mają być śledzone z chmury, należy zaktualizować bliźniaczej reprezentacji urządzenia. Można wykonać to wiele sposobów, w tym edytor JSON w portalu i zestaw SDK usługi Usługi IoT Hub. Poniższe podsekcje zawierają przykłady.

### <a name="update-using-the-portal"></a>Aktualizacja za pomocą portalu

1. Przejdź do centrum IoT hub w [witrynie Azure portal,](https://portal.azure.com/)a następnie kliknij pozycję **Urządzenia IoT**.

1. Kliknij urządzenie.

1. **Poszukaj opcji Włącz śledzenie rozproszone (wersja zapoznawcza),** a następnie wybierz pozycję **Włącz**.

    ![Włączanie śledzenia rozproszonego w witrynie Azure portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Wybierz **częstotliwość próbkowania** między 0% a 100%.

1. Kliknij pozycję **Zapisz**.

1. Odczekaj kilka sekund i naciśnij **przycisk Odśwież**, a jeśli zostanie pomyślnie potwierdzona przez urządzenie, pojawi się ikona synchronizacji ze znacznikiem wyboru.

1. Wróć do okna konsoli dla aplikacji wiadomości telemetrycznych. Zostaną wyświetlone wiadomości `tracestate` wysyłane z we właściwościach aplikacji.

    ![Stan śledzenia](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcjonalnie) Zmień częstotliwość próbkowania na inną wartość i obserwuj `tracestate` zmianę częstotliwości, która zawiera komunikaty we właściwościach aplikacji.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aktualizacja przy użyciu usługi Azure IoT Hub for VS Code

1. Zainstaluj kod VS, a następnie zainstaluj najnowszą wersję usługi Azure IoT Hub for VS Code [w tym miejscu.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

1. Otwórz program VS Code i [skonfiguruj parametry połączenia usługi IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozwiń urządzenie i **poszukaj ustawienia śledzenia rozproszonego (podgląd)**. W obszarze niego kliknij pozycję **Aktualizuj ustawienie śledzenia rozproszonego (podgląd)** węzła podrzędnego.

    ![Włączanie śledzenia rozproszonego w rozszerzeniu usługi Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. W oknie podręcznym wybierz pozycję **Włącz**, a następnie naciśnij klawisz Enter, aby potwierdzić 100 jako częstotliwość próbkowania.

    ![Aktualizowanie trybu próbkowania](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Aktualizacja częstotliwości próbkowania](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Zbiorcza aktualizacja dla wielu urządzeń

Aby zaktualizować konfigurację próbkowania śledzenia rozproszonego dla wielu urządzeń, należy użyć [automatycznej konfiguracji urządzenia](iot-hub-auto-device-config.md). Upewnij się, że wykonaj ten schemat bliźniaczej reprezentacji:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nazwa elementu | Wymagany | Typ | Opis |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Tak | Liczba całkowita | Dwie wartości trybu są obecnie obsługiwane, aby włączyć i wyłączyć próbkowanie. `1`jest włączony `2` i, jest wyłączony. |
| `sampling_rate` | Tak | Liczba całkowita | Ta wartość jest wartością procentową. Dozwolone są `0` `100` tylko wartości od (włącznie).  |

## <a name="query-and-visualize"></a>Zapytanie i wizualizacja

Aby wyświetlić wszystkie ślady zarejestrowane przez Centrum IoT, należy zbadać magazyn dzienników wybrany w ustawieniach diagnostycznych. W tej sekcji przechodzi kilka różnych opcji.

### <a name="query-using-log-analytics"></a>Kwerenda przy użyciu usługi Log Analytics

Jeśli skonfigurowałeś usługi [Log Analytics z dziennikami diagnostycznymi,](../azure-monitor/platform/resource-logs-collect-storage.md)zapytaj, wyszukując dzienniki w `DistributedTracing` kategorii. Na przykład ta kwerenda pokazuje wszystkie ślady rejestrowane:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Przykładowe dzienniki wyświetlane przez usługa Log Analytics:

| TimeGenerated | OperationName | Kategoria | Poziom | CorrelationId | Czas trwaniaMs | Właściwości |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnostykaIoTHubD2C | Śledzenie rozproszone | Informacyjne | 00-8cd869a412459a25f5b4f3131123344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress ( DiagnosticIoTHubIngress ) | Śledzenie rozproszone | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | Ruch diagnostycznyIoTHubEgress | Śledzenie rozproszone | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Aby zrozumieć różne typy dzienników, zobacz [Dzienniki diagnostyczne usługi Azure IoT Hub.](iot-hub-monitor-resource-health.md#distributed-tracing-preview)

### <a name="application-map"></a>Mapa aplikacji

Aby wizualizować przepływ komunikatów IoT, skonfiguruj przykładową aplikację Mapy aplikacji. Przykładowa aplikacja wysyła rozproszone dzienniki śledzenia do [mapy aplikacji](../application-insights/app-insights-app-map.md) przy użyciu funkcji platformy Azure i Centrum zdarzeń.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Pobierz próbkę w usłudze GitHub</a>

Na poniższym obrazku przedstawiono śledzenie rozproszone w mapie aplikacji z trzema punktami końcowymi routingu:

![Śledzenie rozproszone IoT w mapie aplikacji](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Opis śledzenia rozproszonego IoT platformy Azure

### <a name="context"></a>Kontekst

Wiele rozwiązań IoT, w tym nasza własna [architektura referencyjna](https://aka.ms/iotrefarchitecture) (tylko w języku angielskim), zazwyczaj jest zgodna z wariantem [architektury mikrousług.](https://docs.microsoft.com/azure/architecture/microservices/) Jako rozwiązanie IoT staje się bardziej złożone, w końcu przy użyciu kilkunastu mikrousług. Te mikrousługi mogą lub nie mogą być z platformy Azure. Wskazanie, gdzie wiadomości IoT upuszczają lub spowalnia, może stać się wyzwaniem. Na przykład masz rozwiązanie IoT, które używa 5 różnych usług platformy Azure i 1500 aktywnych urządzeń. Każde urządzenie wysyła 10 komunikatów z urządzenia do chmury na sekundę (w sumie 15 000 wiadomości na sekundę), ale można zauważyć, że aplikacja sieci web widzi tylko 10 000 wiadomości na sekundę. Gdzie jest problem? Jak znaleźć winowajcę?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Wzorzec śledzenia rozproszonego w architekturze mikrousług

Aby odtworzyć przepływ komunikatu IoT w różnych usługach, każda usługa powinna propagować *identyfikator korelacji,* który jednoznacznie identyfikuje komunikat. Po zebraniu w scentralizowanym systemie identyfikatory korelacji umożliwiają wyświetlanie przepływu wiadomości. Ta metoda jest nazywana [wzorcem śledzenia rozproszonego](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Aby wspierać szersze przyjęcie do śledzenia rozproszonego, Microsoft przyczynia się do [standardowej propozycji W3C dla śledzenia rozproszonego](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Obsługa usługi IoT Hub

Po włączeniu obsługa śledzenia rozproszonego dla usługi IoT Hub będzie podążać za tym przepływem:

1. Komunikat jest generowany na urządzeniu IoT.
1. Urządzenie IoT decyduje (z pomocą chmury), że ten komunikat powinien być przypisany z kontekstem śledzenia.
1. SDK dodaje `tracestate` do właściwości aplikacji wiadomości, zawierający sygnaturę czasową tworzenia wiadomości.
1. Urządzenie IoT wysyła komunikat do usługi IoT Hub.
1. Wiadomość dociera do bramy centrum IoT.
1. Usługa IoT Hub `tracestate` wyszukuje we właściwościach aplikacji wiadomości i sprawdza, czy jest w poprawnym formacie.
1. Jeśli tak, Usługa IoT Hub `trace-id` generuje globalnie `span-id` unikatowy dla wiadomości, a dla "przeskoku" `DiagnosticIoTHubD2C`i rejestruje je w dziennikach diagnostycznych usługi Azure Monitor w ramach operacji.
1. Po zakończeniu przetwarzania wiadomości Usługa IoT `span-id` Hub generuje kolejną i `trace-id` rejestruje `DiagnosticIoTHubIngress`ją wraz z istniejącą operacją .
1. Jeśli routing jest włączony dla wiadomości, Usługa IoT Hub zapisuje ją `span-id` w niestandardowym punkcie końcowym i rejestruje inną z tą samą `trace-id` kategorią `DiagnosticIoTHubEgress`.
1. Powyższe kroki są powtarzane dla każdej wygenerowanej wiadomości.

## <a name="public-preview-limits-and-considerations"></a>Publiczne limity podglądu i uwagi

- Wniosek dotyczący standardu kontekstu śledzenia W3C jest obecnie roboczym projektem.
- Obecnie jedynym językiem programowania obsługiwanym przez sdk klienta jest C.
- Funkcja bliźniaczej reprezentacji chmury na urządzenia nie jest dostępna dla [warstwy podstawowej usługi IoT Hub.](iot-hub-scaling.md#basic-and-standard-tiers) Jednak Usługa IoT Hub nadal będzie logować się do usługi Azure Monitor, jeśli zobaczy poprawnie skomponowany nagłówek kontekstu śledzenia.
- Aby zapewnić wydajną obsługę, Centrum IoT nałoży przepustnicę na szybkość rejestrowania, która może wystąpić w ramach śledzenia rozproszonego.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat ogólnego wzorca śledzenia rozproszonego w mikrousługach, zobacz [wzorzec architektury mikrousług: śledzenie rozproszone.](https://microservices.io/patterns/observability/distributed-tracing.html)
- Aby skonfigurować konfigurację w celu zastosowania rozproszonych ustawień śledzenia do dużej liczby urządzeń, zobacz [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę](iot-hub-auto-device-config.md).
- Aby dowiedzieć się więcej o usłudze Azure Monitor, zobacz [Co to jest usługa Azure Monitor?](../azure-monitor/overview.md).
