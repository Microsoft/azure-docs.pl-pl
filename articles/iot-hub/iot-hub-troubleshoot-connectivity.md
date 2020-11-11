---
title: Monitorowanie i rozwiązywanie problemów z połączeniami przy użyciu usługi Azure IoT Hub
description: Dowiedz się, jak monitorować typowe błędy i rozwiązywać problemy z łącznością z urządzeniami dla platformy Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: b179bb3566cc19b8033a56348db34cd1f05cee10
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506400"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z połączeniami z platformą Azure IoT Hub

Problemy z łącznością dla urządzeń IoT mogą być trudne do rozwiązania, ponieważ istnieje wiele możliwych punktów awarii. Aplikacje logiki aplikacji, sieci fizycznych, protokoły, sprzęt, IoT Hub i inne usługi w chmurze mogą spowodować problemy. Możliwość wykrywania i lokalizowania źródła problemu jest krytyczna. Jednak rozwiązanie IoT na dużą skalę może mieć tysiące urządzeń, więc nie jest praktyczne sprawdzanie poszczególnych urządzeń ręcznie. IoT Hub integruje się z dwiema usługami platformy Azure, aby pomóc:

* **Azure monitor** Aby ułatwić wykrywanie, diagnozowanie i rozwiązywanie problemów w odpowiedniej skali, użyj możliwości monitorowania IoT Hub zapewnia Azure Monitor. Obejmuje to Konfigurowanie alertów w celu wyzwolenia powiadomień i akcji podczas odłączania i konfigurowania dzienników, których można użyć do wykrywania warunków, które spowodowały odłączenia.

* **Azure Event Grid** W celu zapewnienia krytycznej infrastruktury i rozłączeń na urządzenie Użyj Azure Event Grid, aby subskrybować zdarzenia łączenia i rozłączania urządzenia emitowane przez IoT Hub.

W obu przypadkach te funkcje są ograniczone do tego, co IoT Hub może obsłużyć, dlatego zalecamy przestrzeganie najlepszych rozwiązań dotyczących monitorowania dla urządzeń i innych usług platformy Azure.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid a Azure Monitor

Event Grid zapewnia rozwiązanie do monitorowania o małym opóźnieniu i na urządzenie, którego można użyć do śledzenia połączeń urządzeń z krytycznymi urządzeniami i infrastrukturą. Azure Monitor zapewnia metrykę i *połączone urządzenia* , za pomocą których można monitorować liczbę urządzeń podłączonych do IoT Hub i wyzwalać alert, gdy liczba spadnie poniżej progu statycznego.

Podczas decydowania, czy należy używać Event Grid lub Azure Monitor w przypadku konkretnego scenariusza:

* Opóźnienie alertów: IoT Hub zdarzenia połączeń są dostarczane znacznie szybciej przez Event Grid. Dzięki temu Event Grid lepszy wybór w scenariuszach, w których jest wymagane szybkie powiadamianie.

* Powiadomienia dla poszczególnych urządzeń: Event Grid zapewnia możliwość śledzenia połączeń i rozłączania dla poszczególnych urządzeń. Dzięki temu Event Grid lepszy wybór dla scenariuszy, w których należy monitorować połączenia dla urządzeń o krytycznym znaczeniu.

* Konfiguracja uproszczona: alerty metryk Azure Monitor zapewniają uproszczoną konfigurację, która nie wymaga integracji z innymi usługami w celu dostarczania powiadomień za pośrednictwem poczty E-mail, wiadomości SMS, głosu i innych powiadomień.  Za pomocą Event Grid należy zintegrować z innymi usługami platformy Azure w celu dostarczania powiadomień. Obie usługi mogą zintegrować z innymi usługami, aby wyzwolić bardziej złożone działania.

Ze względu na możliwości dotyczące małych opóźnień dla poszczególnych urządzeń w środowiskach produkcyjnych zdecydowanie zalecamy używanie Event Grid do monitorowania połączeń. Oczywiście wybór nie jest wyłączny, można użyć zarówno Azure Monitor alertów metryk, jak i Event Grid. Bez względu na wybór sposobu śledzenia rozłączeń prawdopodobnie będziesz używać dzienników zasobów Azure Monitor, aby pomóc w rozwiązywaniu problemów z nieoczekiwanym odłączeniem urządzenia. W poniższych sekcjach szczegółowo omówiono każdą z tych opcji.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: Monitoruj zdarzenia łączenia i rozłączania urządzenia

Aby monitorować zdarzenia łączenia i rozłączania urządzeń w środowisku produkcyjnym, zalecamy subskrybowanie [zdarzeń **DeviceConnected** i **DeviceDisconnected**](iot-hub-event-grid.md#event-types) w Event Grid do wyzwalania alertów i monitorowania stanu połączenia urządzenia. Event Grid zapewnia znacznie małe opóźnienia zdarzeń niż Azure Monitor i można monitorować dla poszczególnych urządzeń, a nie dla łącznej liczby połączonych urządzeń. Te czynniki sprawiają, że Event Grid preferowaną metodę monitorowania krytycznych urządzeń i infrastruktury.

W przypadku korzystania z Event Grid do monitorowania lub wyzwalania alertów dotyczących rozłączeń urządzeń należy się upewnić, że kompilacja jest w sposób umożliwiający odfiltrowanie okresowych połączeń z powodu odnowienia tokenu SAS na urządzeniach korzystających z zestawów SDK usługi Azure IoT. Aby dowiedzieć się więcej, zobacz temat [zachowanie rozłączenia urządzenia MQTT przy użyciu zestawów SDK usługi Azure IoT](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Zapoznaj się z następującymi tematami, aby dowiedzieć się więcej o monitorowaniu zdarzeń połączeń urządzeń za pomocą Event Grid:

* Aby zapoznać się z omówieniem używania Event Grid z IoT Hub, zobacz temat [reagowanie na zdarzenia IoT Hub z Event Grid](iot-hub-event-grid.md). Należy zwrócić szczególną uwagę na ograniczenia związane z sekcjami [zdarzenia połączone i odłączone urządzenia](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Aby zapoznać się z samouczkiem dotyczącym określania kolejności zdarzeń połączeń urządzeń, zobacz temat porządkowanie [zdarzeń połączeń urządzeń z platformy Azure IoT Hub przy użyciu Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Aby zapoznać się z samouczkiem dotyczącym wysyłania powiadomień E-mail, zobacz [wysyłanie powiadomień e-mail dotyczących zdarzeń platformy Azure IoT Hub przy użyciu Event Grid i Logic Apps](/azure/event-grid/publish-iot-hub-events-to-logic-apps) w dokumentacji Event Grid.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: kierowanie zdarzeń połączenia do dzienników

Centrum IoT Hub ciągle emituje dzienniki zasobów dla kilku kategorii operacji. Aby zebrać te dane dziennika, należy utworzyć ustawienie diagnostyczne, aby skierować je do miejsca docelowego, gdzie można je przeanalizować lub zarchiwizować. Jednym z takich miejsc docelowych jest Azure Monitor dzienników za pośrednictwem Log Analytics obszaru roboczego ([Zobacz cennik](https://azure.microsoft.com/pricing/details/log-analytics/)), w którym można analizować dane przy użyciu zapytań Kusto.

[Kategoria połączenia dzienników zasobów](monitor-iot-hub-reference.md#connections) IoT Hub emituje operacje i błędy związane z połączeniami urządzeń. Poniższy zrzut ekranu przedstawia ustawienia diagnostyczne, które umożliwiają kierowanie tych dzienników do obszaru roboczego Log Analytics:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Zalecane ustawienie wysyłania dzienników połączeń do obszaru roboczego Log Analytics.":::

Zaleca się utworzenie ustawień diagnostycznych najszybciej jak to możliwe po utworzeniu Centrum IoT Hub, ponieważ mimo że usługa IoT Hub zawsze emituje dzienniki zasobów, nie są zbierane przez Azure Monitor do momentu rozesłania ich do miejsca docelowego.

Aby dowiedzieć się więcej na temat routingu dzienników do lokalizacji docelowej, zobacz [zbieranie i Routing](monitor-iot-hub.md#collection-and-routing). Szczegółowe instrukcje dotyczące tworzenia ustawień diagnostycznych znajdują się w [samouczku Korzystanie z metryk i dzienników](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: Skonfiguruj alerty metryki dla rozłączenia urządzenia w skali

Można skonfigurować alerty na podstawie metryk platformy emitowanych przez IoT Hub. Za pomocą alertów metryk można powiadamiać użytkowników, że wystąpiły warunki zainteresowania, a także wyzwalać akcje, które mogą odpowiedzieć na ten warunek automatycznie.

Metryka [*połączone urządzenia (wersja zapoznawcza)*](monitor-iot-hub-reference.md#device-metrics) informuje, ile urządzeń jest podłączonych do IoT Hub. Można utworzyć alerty, które będą wyzwalać, jeśli ta Metryka spadnie poniżej wartości progowej:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Ustawienia logiki alertu dotyczące metryk podłączonych urządzeń.":::

Można użyć reguł alertów dotyczących metryk, aby monitorować anomalie rozłączenia urządzenia w skali. Oznacza to, że w przypadku nieoczekiwanego odłączenia dużej liczby urządzeń. Po wykryciu takiego wystąpienia można sprawdzić dzienniki, aby pomóc w rozwiązaniu problemu. Aby monitorować rozłączanie i rozłączanie poszczególnych urządzeń dla urządzeń krytycznych; należy jednak użyć Event Grid. Event Grid również zapewnia więcej doświadczenia w czasie rzeczywistym niż metryki platformy Azure.

Aby dowiedzieć się więcej o alertach z IoT Hub, zobacz [alerty w IoT Hub monitor](monitor-iot-hub.md#alerts). Aby uzyskać szczegółowe instrukcje dotyczące tworzenia alertów w IoT Hub, zobacz [samouczek korzystanie z metryk i dzienników](tutorial-use-metrics-and-diags.md). Aby zapoznać się z bardziej szczegółowym omówieniem alertów, zobacz [Omówienie alertów w Microsoft Azure](../azure-monitor/platform/alerts-overview.md) w dokumentacji Azure monitor.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: korzystanie z dzienników w celu rozwiązywania problemów z łącznością

W przypadku wykrycia odłączeń urządzeń, niezależnie od tego, czy jest to Azure Monitor alerty metryki czy Event Grid, można użyć dzienników, aby pomóc w rozwiązaniu tego problemu. W tej sekcji opisano sposób wyszukiwania typowych problemów w dziennikach Azure Monitor. W poniższych krokach przyjęto założenie, że zostało już utworzone [ustawienie diagnostyczne](#azure-monitor-route-connection-events-to-logs) służące do wysyłania dzienników połączeń IoT Hub do log Analytics obszaru roboczego.

Po utworzeniu ustawienia diagnostycznego w celu kierowania dzienników zasobów IoT Hub do dzienników Azure Monitor, wykonaj następujące kroki, aby wyświetlić dzienniki w Azure Portal.

1. Przejdź do centrum IoT Hub w [Azure Portal](https://portal.azure.com).

1. W obszarze **monitorowanie** w okienku po lewej stronie Centrum IoT wybierz pozycję **dzienniki**.

1. Aby wyizolować dzienniki błędów łączności dla IoT Hub, wprowadź następujące zapytanie w edytorze zapytań, a następnie wybierz polecenie **Uruchom** :

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Jeśli istnieją wyniki, Wyszukaj `OperationName` , `ResultType` (kod błędu) i `ResultDescription` (komunikat o błędzie), aby uzyskać więcej szczegółów dotyczących błędu.

   ![Przykładowy dziennik błędów](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Po zidentyfikowaniu błędu postępuj zgodnie z przewodnikiem rozwiązywania problemów, aby uzyskać pomoc dotyczącą najczęstszych błędów:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Zachowanie rozłączenia urządzenia MQTT przy użyciu zestawów SDK usługi Azure IoT

Zestawy SDK urządzeń Azure IoT odłączają się od IoT Hub, a następnie ponownie nawiązują połączenie przy odnowieniu tokenów SAS za pośrednictwem protokołu MQTT (i MQTT over WebSockets). W dziennikach pokazuje jako informacje o zdarzeniach odłączenia urządzenia i połączenia, które czasami towarzyszą zdarzenia błędów.

Domyślnie token cykl życia wynosi 60 minut dla wszystkich zestawów SDK; można go jednak zmienić przez deweloperów w niektórych zestawach SDK. W poniższej tabeli zestawiono cykl życia tokenów, odnowienie tokenu oraz zachowanie odnowienia tokenu dla każdego z zestawów SDK:

| SDK | Cykl życia tokenu | Odnowienie tokenu | Zachowanie odnawiania |
|-----|----------|---------------------|---------|
| .NET | 60 minut, konfigurowalne | 85% cykl życia, konfigurowalne | Zestaw SDK łączy się i rozłącza przy użyciu tokenu cykl życia oraz 10-minutowego okresu prolongaty. Zdarzenia informacyjne i błędy wygenerowane w dziennikach. |
| Java | 60 minut, konfigurowalne | 85% cykl życia, nie można skonfigurować | Zestaw SDK łączy się i rozłącza przy użyciu tokenu cykl życia oraz 10-minutowego okresu prolongaty. Zdarzenia informacyjne i błędy wygenerowane w dziennikach. |
| Node.js | 60 minut, konfigurowalne | skonfigurować | Zestaw SDK łączy się i rozłącza przy odnowieniu tokenu. W dziennikach są generowane tylko zdarzenia informacyjne. |
| Python | 60 minut, nie można skonfigurować | -- | Zestaw SDK łączy się i rozłącza przy tokenie cykl życia. |

Poniższe zrzuty ekranu pokazują zachowanie odnowienia tokenu w dziennikach Azure Monitor dla różnych zestawów SDK. Próg cykl życia i odnowienia tokenu został zmieniony z wartości domyślnych, zgodnie z oczekiwaniami.

* Zestaw SDK urządzeń .NET z 1200 sekund (20 minut) token cykl życia i odnowienie ma ustawioną wartość o 90% z cykl życia. rozłączenia odbywa się co 30 minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Zachowanie błędów w przypadku odnowienia tokenu przez MQTT w dziennikach Azure Monitor przy użyciu zestawu .NET SDK.":::

* Zestaw Java SDK z tokenem 300 sekund (5 minut) cykl życia i domyślnym 85% odnowienia cykl życia. Rozłączenia odbywa się co 15 minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Zachowanie błędów w przypadku odnowienia tokenu przez MQTT w dziennikach Azure Monitor przy użyciu zestawu Java SDK.":::

* Zestaw SDK węzła z 300 sekund (5 minut) tokenem cykl życia i odnowieniem tokenu ustawionym na 3 minuty. Rozłączenia odbywa się przy odnowieniu tokenu. Ponadto nie ma błędów, emitowane są tylko zdarzenia informacyjne Connect/Disconnect:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Zachowanie błędów w przypadku odnowienia tokenu przez MQTT w dziennikach Azure Monitor z zestawem SDK węzła.":::

Poniższe zapytanie zostało użyte do zebrania wyników. Zapytanie wyodrębnia nazwę i wersję zestawu SDK z zbioru właściwości; Aby dowiedzieć się więcej, zobacz [wersja zestawu SDK w dzienniku IoT Hub](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Jako deweloper rozwiązań IoT lub operator korzystasz z tego zachowania, aby interpretować zdarzenia łączenia/rozłączania i pokrewne błędy w dziennikach. Jeśli chcesz zmienić zachowanie cykl życia tokenu lub odnowienia dla urządzeń, sprawdź, czy urządzenie implementuje ustawienie pojedynczej urządzenia lub metodę urządzenia, która to możliwe.

Jeśli monitorujesz połączenia urządzeń za pomocą centrum zdarzeń, upewnij się, że kompilujesz się w sposób filtrowania okresowych odłączeń z powodu odnowienia tokenu SAS; na przykład przez niewyzwalanie akcji na podstawie rozłączeń, o ile następuje zdarzenie Connect w określonym przedziale czasu.

> [!NOTE]
> IoT Hub obsługuje tylko jedno aktywne połączenie MQTT dla każdego urządzenia. Każde nowe połączenie MQTT w imieniu tego samego identyfikatora urządzenia powoduje, że IoT Hub porzucić istniejące połączenie.
>
> 400027 ConnectionForcefullyClosedOnNewConnection zostanie zalogowany do dzienników IoT Hub

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Podjęto próbę wykonania czynności, ale nie zadziałały

Jeśli poprzednie kroki nie były pomocne, spróbuj wykonać następujące czynności:

* Jeśli masz dostęp do problematycznych urządzeń, fizycznie lub zdalnie (na przykład SSH), postępuj zgodnie z [przewodnikiem rozwiązywania problemów po stronie urządzenia](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) , aby kontynuować rozwiązywanie problemów.

* Sprawdź, czy urządzenia są **włączone** w Azure Portal > urządzeniu iot Hub > IoT.

* Jeśli urządzenie korzysta z protokołu MQTT, sprawdź, czy port 8883 jest otwarty. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Uzyskaj pomoc od [firmy Microsoft pytań&stronie pytań dotyczących usługi azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)lub [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/).

Aby pomóc w ulepszaniu dokumentacji dla wszystkich użytkowników, należy pozostawić komentarz w sekcji opinii poniżej, jeśli ten przewodnik nie pomoże.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o rozwiązywaniu problemów przejściowych, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults).

* Aby dowiedzieć się więcej o zestawie SDK usługi Azure IoT i zarządzaniu ponownymi próbami, zobacz [jak zarządzać łącznością i niezawodną obsługą przy użyciu zestawów SDK urządzeń IoT Hub Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).