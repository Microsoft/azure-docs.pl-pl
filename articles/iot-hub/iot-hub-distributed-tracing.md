---
title: Dodawanie identyfikatorów korelacji do komunikatów IoT w ramach śledzenia rozproszonego (pre)
description: Dowiedz się, jak śledzić komunikaty IoT w ramach usług platformy Azure używanych przez rozwiązanie przy użyciu funkcji śledzenia rozproszonego.
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
- fasttrack-edit
- iot
ms.openlocfilehash: fca5c1a0aee6e62890d734e3233177549d932672
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643673"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Śledzenie komunikatów z urządzenia do chmury w usłudze Azure IoT z rozproszonym śledzeniem (wersja zapoznawcza)

Microsoft Azure IoT Hub obecnie obsługuje śledzenie rozproszone jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub jest jedną z pierwszych usług platformy Azure, która obsługuje śledzenie rozproszone. Ponieważ więcej usług platformy Azure obsługuje śledzenie rozproszone, będziesz mieć możliwość śledzenia komunikatów IoT w ramach usług platformy Azure, które są objęte tym rozwiązaniem. Aby zapoznać się z tłem rozproszonego śledzenia, zobacz [śledzenie rozproszone](../azure-monitor/app/distributed-tracing.md).

Włączenie śledzenia rozproszonego dla IoT Hub daje możliwość:

- Precyzyjne monitorowanie przepływu poszczególnych komunikatów za pośrednictwem IoT Hub przy użyciu [kontekstu śledzenia](https://github.com/w3c/trace-context). Ten kontekst śledzenia zawiera identyfikatory korelacji, które umożliwiają skorelowanie zdarzeń z jednego składnika ze zdarzeniami z innego składnika. Można go zastosować dla podzestawów lub wszystkich komunikatów urządzeń IoT przy użyciu [sznurka urządzenia](iot-hub-devguide-device-twins.md).
- Automatycznie Rejestruj kontekst śledzenia do [Azure monitor dzienników](monitor-iot-hub.md).
- Zmierz i poznanie przepływu komunikatów oraz opóźnień z urządzeń w celu IoT Hub i kierowania punktów końcowych.
- Zacznij rozważać, jak chcesz zaimplementować rozproszone śledzenie dla usług spoza platformy Azure w rozwiązaniu IoT.

W tym artykule opisano użycie [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md) z rozproszoną funkcją śledzenia. Obsługa śledzenia rozproszonego jest nadal w toku dla innych zestawów SDK.

## <a name="prerequisites"></a>Wymagania wstępne

- Wersja zapoznawcza śledzenia rozproszonego jest obecnie obsługiwana tylko w przypadku centrów IoT utworzonych w następujących regionach:

  - **Europa Północna**
  - **Southeast Asia**
  - **Zachodnie stany USA 2**

- W tym artykule założono, że wiesz już, jak wysyłać komunikaty telemetryczne do centrum IoT. Upewnij się, że zakończono [wysyłanie danych telemetrycznych dla przewodnika Szybki Start](quickstart-send-telemetry-c.md).

- Zarejestruj urządzenie w centrum IoT Hub (kroki dostępne w każdym przewodniku Szybki Start) i zanotuj parametry połączenia.

- Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurowanie IoT Hub

W tej sekcji skonfigurujesz IoT Hub do rejestrowania atrybutów śledzenia rozproszonego (identyfikatorów korelacji i sygnatur czasowych).

1. Przejdź do centrum IoT Hub w [Azure Portal](https://portal.azure.com/).

1. W lewym okienku usługi IoT Hub przewiń w dół do sekcji **monitorowanie** , a następnie kliknij pozycję **Ustawienia diagnostyki**.

1. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

1. W polu **Nazwa** wprowadź nazwę nowego ustawienia diagnostycznego. Na przykład **DistributedTracingSettings**.

1. Wybierz co najmniej jedną z następujących opcji, które określają, gdzie będzie wysyłane rejestrowanie:

    - **Archiwizuj na koncie magazynu**: Skonfiguruj konto magazynu tak, aby zawierało informacje o rejestrowaniu.
    - **Przesyłaj strumieniowo do centrum zdarzeń**: Skonfiguruj centrum zdarzeń, aby zawierało informacje o rejestrowaniu.
    - **Wyślij do log Analytics**: Skonfiguruj obszar roboczy usługi log Analytics, aby zawierał informacje o rejestrowaniu.

1. W sekcji **Dziennik** wybierz operacje, dla których chcesz rejestrować informacje.

    Upewnij się, że dołączysz **DistributedTracing** i skonfigurujesz **przechowywanie** dla liczby dni zachowywania rejestrowania. Przechowywanie dzienników ma wpływ na koszty magazynowania.

    ![Zrzut ekranu przedstawiający lokalizację kategorii DistributedTracing dla ustawień diagnostycznych usługi IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Kliknij przycisk **Zapisz** , aby nowe ustawienie.

1. Obowiązkowe Aby wyświetlić komunikaty przepływu w różnych miejscach, skonfiguruj [reguły routingu do co najmniej dwóch różnych punktów końcowych](iot-hub-devguide-messages-d2c.md).

Po włączeniu rejestrowania IoT Hub rejestruje dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia zostanie napotkany w jednej z następujących sytuacji:

- Komunikaty docierają do bramy IoT Hub.
- Komunikat jest przetwarzany przez IoT Hub.
- Komunikat jest kierowany do niestandardowych punktów końcowych. Routing musi być włączony.

Aby dowiedzieć się więcej o tych dziennikach i ich schematach, zobacz [monitorowanie IoT Hub](monitor-iot-hub.md) i [rozproszone śledzenie w IoT Hub dzienników zasobów](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Konfigurowanie urządzenia

W tej sekcji utworzysz środowisko programistyczne do użycia z [zestawem SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Następnie należy zmodyfikować jeden z przykładów, aby włączyć śledzenie rozproszone na komunikatów telemetrycznych urządzenia.

Te instrukcje dotyczą tworzenia przykładu w systemie Windows. W przypadku innych środowisk należy zapoznać [się z tematem kompilowanie zestawu SDK języka c](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) lub [prepakowanie SDK języka c dla projektowania specyficznego dla platformy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonuj kod źródłowy i zainicjuj

1. Zainstaluj [obciążenie "Programowanie aplikacji klasycznych w języku C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) dla programu Visual Studio 2019. Obsługiwane są również programy Visual Studio 2017 i 2015.

1. Zainstaluj [CMAKE](https://cmake.org/). Upewnij się, że znajduje się w obszarze `PATH` , wpisując `cmake -version` polecenie w wierszu polecenia.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium [usługi Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub:

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

    Jeśli `cmake` nie można znaleźć kompilatora języka C++, może wystąpić błąd kompilacji przy uruchamianiu powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Edytuj przykład wysyłania danych telemetrycznych w celu włączenia śledzenia rozproszonego

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Pobierz przykład w witrynie GitHub</a>

1. Użyj edytora, aby otworzyć `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` plik źródłowy.

1. Znajdź deklarację stałej `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Zastąp wartość `connectionString` stałą parametrami połączenia urządzenia, które zostały zanotowane w sekcji [Rejestrowanie urządzenia](./quickstart-send-telemetry-c.md#register-a-device) w [przewodniku szybki start dotyczącego wysyłania telemetrii C](./quickstart-send-telemetry-c.md).

1. Zmień wartość `MESSAGE_COUNT` Definiuj na `5000` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Znajdź wiersz kodu, który wywołuje, `IoTHubDeviceClient_LL_SetConnectionStatusCallback` Aby zarejestrować funkcję wywołania zwrotnego stanu połączenia przed pętlą wysyłania komunikatów. Dodaj kod w tym wierszu, jak pokazano poniżej, aby wywołać `IoTHubDeviceClient_LL_EnablePolicyConfiguration` Włączenie śledzenia rozproszonego dla urządzenia:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration`Funkcja włącza zasady dla określonych funkcji IoTHub skonfigurowanych za pośrednictwem [urządzenia bliźniaczych reprezentacji](./iot-hub-devguide-device-twins.md). Po `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` włączeniu z wierszem kodu powyżej zachowanie śledzenia urządzenia będzie odzwierciedlać rozproszone zmiany śledzenia dotyczące sznurka urządzenia.

1. Aby zapewnić, że aplikacja Przykładowa będzie uruchomiona bez korzystania ze wszystkich limitów przydziału, należy dodać jednosekundowe opóźnienie na końcu pętli wysyłania komunikatu:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompiluj i uruchom

1. Przejdź do katalogu projektu *iothub_ll_telemetry_sample* z utworzonego wcześniej katalogu CMAKE ( `azure-iot-sdk-c/cmake` ) i skompiluj przykład:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Uruchom aplikację. Urządzenie wysyła dane telemetryczne obsługujące śledzenie rozproszone.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Pozostaw uruchomioną aplikację. Opcjonalnie Obserwuj komunikat wysyłany do IoT Hub, przeglądając okno konsoli.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Obejście dla klientów innych firm

Nie jest **proste** , aby wyświetlić podgląd funkcji śledzenia rozproszonego bez użycia zestawu SDK języka C. W tym przypadku takie podejście nie jest zalecane.

Najpierw należy zaimplementować wszystkie elementy podstawowe protokołu IoT Hub w wiadomościach, postępując zgodnie z instrukcją deweloperskią [Tworzenie i odczytywanie IoT Hub komunikatów](iot-hub-devguide-messages-construct.md). Następnie Edytuj właściwości protokołu w komunikatach MQTT/AMQP, aby dodać `tracestate` jako **Właściwość systemu**. Są to:

* W przypadku MQTT Dodaj `%24.tracestate=timestamp%3d1539243209` do tematu wiadomości, gdzie `1539243209` powinien zostać zastąpiony czasem utworzenia komunikatu w formacie sygnatury czasowej systemu UNIX. Na przykład zapoznaj się z implementacją [w zestawie SDK języka C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) .
* Dla AMQP, Dodawanie `key("tracestate")` i `value("timestamp=1539243209")` jak adnotacja wiadomości. Aby zapoznać się z implementacją referencyjną, zobacz [tutaj](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Aby kontrolować procent komunikatów zawierających tę właściwość, należy wdrożyć logikę w celu nasłuchiwania zdarzeń inicjowanych w chmurze, takich jak aktualizacje bliźniaczye.

## <a name="update-sampling-options"></a>Opcje próbkowania aktualizacji 

Aby zmienić procent komunikatów, które mają być śledzone z chmury, należy zaktualizować sznurki urządzenia. Można to zrobić na wiele sposobów, łącznie z edytorem JSON w portalu i zestawem SDK usługi IoT Hub. W poniższych podsekcjach przedstawiono przykłady.

### <a name="update-using-the-portal"></a>Aktualizowanie przy użyciu portalu

1. Przejdź do centrum IoT Hub w [Azure Portal](https://portal.azure.com/), a następnie kliknij pozycję **urządzenia IoT**.

1. Kliknij urządzenie.

1. Wyszukaj pozycję **Włącz śledzenie rozproszone (wersja zapoznawcza)**, a następnie wybierz pozycję **Włącz**.

    ![Włącz śledzenie rozproszone w Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Wybierz **częstotliwość próbkowania** w zakresie od 0% do 100%.

1. Kliknij pozycję **Zapisz**.

1. Odczekaj kilka sekund, a następnie kliknij przycisk **Odśwież**, a następnie, jeśli został pomyślnie potwierdzony przez urządzenie, zostanie wyświetlona ikona synchronizacji z znacznikiem wyboru.

1. Wróć do okna konsoli dla aplikacji wiadomości telemetrycznych. Komunikaty wysyłane za pomocą `tracestate` we właściwościach aplikacji zostaną wyświetlone.

    ![Stan śledzenia](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Obowiązkowe Zmień częstotliwość próbkowania na inną wartość i obserwuj zmianę częstotliwości, jaką mogą zawierać komunikaty `tracestate` we właściwościach aplikacji.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aktualizowanie za pomocą usługi Azure IoT Hub dla VS Code

1. Zainstaluj VS Code a następnie zainstaluj najnowszą wersję usługi Azure IoT Hub na potrzeby VS Code z tego [miejsca](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Otwórz VS Code i [skonfiguruj IoT Hub parametry połączenia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozwiń urządzenie i Wyszukaj **ustawienia śledzenia rozproszonego (wersja zapoznawcza)**. W obszarze IT kliknij pozycję **Aktualizuj rozproszone śledzenie ustawień (wersja zapoznawcza)** węzła podrzędnego.

    ![Włącz śledzenie rozproszone w rozszerzeniu usługi Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. W oknie podręcznym wybierz pozycję **Włącz**, a następnie naciśnij klawisz ENTER, aby potwierdzić 100 jako częstotliwość próbkowania.

    ![Aktualizowanie trybu próbkowania](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Częstotliwość próbkowania aktualizacji](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Aktualizacja zbiorcza dla wielu urządzeń

Aby zaktualizować konfigurację próbkowania śledzenia rozproszonego dla wielu urządzeń, użyj [automatycznej konfiguracji urządzenia](./iot-hub-automatic-device-management.md). Upewnij się, że obserwujesz ten schemat bliźniaczy:

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
| `sampling_mode` | Tak | Liczba całkowita | Dwie wartości trybu są obecnie obsługiwane do włączania i wyłączania próbkowania. `1` jest włączone i, `2` jest wyłączone. |
| `sampling_rate` | Tak | Liczba całkowita | Ta wartość jest wartością procentową. Dozwolone są tylko wartości z `0` do `100` (włącznie).  |

## <a name="query-and-visualize"></a>Zapytanie i wizualizacja

Aby wyświetlić wszystkie ślady zarejestrowane przez IoT Hub, wykonaj zapytanie do magazynu dzienników wybranego w ustawieniach diagnostycznych. Ta sekcja zawiera kilka różnych opcji.

### <a name="query-using-log-analytics"></a>Zapytanie przy użyciu Log Analytics

Jeśli skonfigurowano [log Analytics przy użyciu dzienników zasobów](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage), wyszukaj dzienniki w `DistributedTracing` kategorii. Na przykład to zapytanie wyświetla wszystkie zarejestrowane ślady:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Przykładowe dzienniki, jak pokazano w Log Analytics:

| TimeGenerated | OperationName | Kategoria | Poziom | CorrelationId | DurationMs | Właściwości |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018 R-02-22T03:28:28.633 Z | DiagnosticIoTHubD2C | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId": "AZ3166", "messageSize": "96", "callerLocalTimeUtc": "2018 r-02-22T03:27:28.633 Z", "calleeLocalTimeUtc": "2018 r-02-22T03:27:28.687 Z"} |
| 2018 R-02-22T03:28:38.633 Z | DiagnosticIoTHubIngress | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false", "parentSpanId": "0144d2590aacd909"} |
| 2018 R-02-22T03:28:48.633 Z | DiagnosticIoTHubEgress | DistributedTracing | Informacyjne | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"EndpointType": "EventHub", "EndpointName": "myEventHub", "parentSpanId": "0144d2590aacd909"} |

Aby poznać różne typy dzienników, zobacz [dzienniki śledzenia rozproszonego systemu Azure IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa aplikacji

Aby wizualizować przepływ komunikatów IoT, skonfiguruj przykładową aplikację mapy aplikacji. Przykładowa aplikacja wysyła dzienniki śledzenia rozproszonego do [mapy aplikacji](../azure-monitor/app/app-map.md) przy użyciu funkcji platformy Azure i centrum zdarzeń.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Pobierz przykład w witrynie GitHub</a>

Na poniższym obrazie przedstawiono śledzenie rozproszone w mapie aplikacji z trzema punktami końcowymi routingu:

![Śledzenie rozproszone IoT w mapie aplikacji](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Informacje o śledzeniu rozproszonym usługi Azure IoT

### <a name="context"></a>Kontekst

Wiele rozwiązań IoT, w tym nasza [Architektura referencyjna](/azure/architecture/reference-architectures/iot) (tylko w języku angielskim), zazwyczaj jest zgodna z wariantem [architektury mikrousług](/azure/architecture/microservices/). Ponieważ rozwiązanie IoT zwiększa złożoność, można korzystać z wielu mikrousług. Te mikrousługi mogą lub nie mogą pochodzić z platformy Azure. Lokalizowanie miejsca, w którym komunikaty IoT są upuszczane lub spowalniają działanie, mogą stać się trudne. Na przykład masz rozwiązanie IoT korzystające z 5 różnych usług platformy Azure i 1500 aktywnych urządzeń. Każde urządzenie wysyła 10 komunikatów z urządzenia do chmury na sekundę (łącznie 15 000 komunikatów/sekundę), ale zauważ, że Twoja aplikacja internetowa widzi tylko 10 000 komunikatów/sekundę. Gdzie jest problem? Jak znaleźć Przyczyna?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Rozproszony wzorzec śledzenia w architekturze mikrousług

W celu odtworzenia przepływu wiadomości IoT w różnych usługach każda usługa powinna propagować *Identyfikator korelacji* , który jednoznacznie identyfikuje komunikat. Po zebraniu w scentralizowanym systemie identyfikatory korelacji umożliwiają wyświetlanie przepływu komunikatów. Ta metoda jest nazywana [wzorcem śledzenia rozproszonego](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Aby zapewnić obsługę szerszego wdrożenia rozproszonego śledzenia, firma Microsoft przyczynimy do [standardowej oferty na potrzeby rozproszonego śledzenia](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Obsługa IoT Hub

Po włączeniu obsługa śledzenia rozproszonego dla IoT Hub będzie zgodna z tym przepływem:

1. Na urządzeniu IoT jest generowany komunikat.
1. Urządzenie IoT przyjmuje decyzję (z pomocą techniczną), do której ten komunikat powinien zostać przypisany przy użyciu kontekstu śledzenia.
1. Zestaw SDK dodaje `tracestate` do właściwości Message zawierającej sygnaturę czasową tworzenia wiadomości.
1. Urządzenie IoT wysyła komunikat do IoT Hub.
1. Wiadomość dotarła do bramy IoT Hub.
1. IoT Hub szuka `tracestate` właściwości w komunikacie i sprawdza, czy jest w poprawnym formacie.
1. Jeśli tak, IoT Hub generuje globalnie unikatowy `trace-id` dla wiadomości, `span-id` dla "przeskok" i rejestruje je do [IoT Hub rozproszonych dzienników śledzenia](monitor-iot-hub-reference.md#distributed-tracing-preview) w ramach operacji `DiagnosticIoTHubD2C` .
1. Po zakończeniu przetwarzania komunikatu IoT Hub generuje inny `span-id` i rejestruje go wraz z istniejącym `trace-id` w ramach operacji `DiagnosticIoTHubIngress` .
1. Jeśli dla wiadomości jest włączona funkcja routingu, IoT Hub zapisuje ją w niestandardowym punkcie końcowym i rejestruje inne `span-id` dane przy użyciu tej samej `trace-id` kategorii `DiagnosticIoTHubEgress` .
1. Powyższe kroki są powtórzone dla każdego wygenerowanego komunikatu.

## <a name="public-preview-limits-and-considerations"></a>Limity i zagadnienia dotyczące publicznej wersji zapoznawczej

- Propozycja standardu kontekstu śledzenia W3C jest obecnie działającą wersją roboczą.
- Obecnie jedynym językiem programistycznym obsługiwanym przez zestaw SDK klienta jest C.
- Funkcja przędzy z chmury do urządzeń nie jest dostępna dla [IoT Hub warstwy Podstawowa](iot-hub-scaling.md#basic-and-standard-tiers). Jednak IoT Hub nadal będą logować się do Azure Monitor, jeśli zobaczy prawidłowo utworzony nagłówek kontekstu śledzenia.
- W celu zapewnienia wydajnej pracy IoT Hub nakłada ograniczenia dotyczące szybkości rejestrowania, które może wystąpić w ramach śledzenia rozproszonego.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat ogólnego wzorca śledzenia rozproszonego w mikrousługach, zobacz [wzorzec architektury mikrousług: śledzenie rozproszone](https://microservices.io/patterns/observability/distributed-tracing.html).
- Aby skonfigurować konfigurację w celu zastosowania ustawień śledzenia rozproszonego na dużej liczbie urządzeń, zobacz [Konfigurowanie i monitorowanie urządzeń IoT w odpowiedniej skali](./iot-hub-automatic-device-management.md).
- Aby dowiedzieć się więcej na temat Azure Monitor, zobacz [co to jest Azure monitor?](../azure-monitor/overview.md).
- Aby dowiedzieć się więcej o korzystaniu z Azure Monitor z usługą IoT HUb, zobacz [monitorowanie IoT Hub](monitor-iot-hub.md)