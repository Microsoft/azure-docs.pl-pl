---
title: Monitorowanie danych IoT Hub platformy Azure
description: Ważne materiały referencyjne potrzebne podczas monitorowania IoT Hub platformy Azure
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 166234711ce00f0ed1f45c35ef661aa5b35f8a3c
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926329"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Monitorowanie danych IoT Hub platformy Azure

Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla IoT Hub platformy Azure, zobacz artykuł [monitorowanie usługi azure IoT Hub](monitor-iot-hub.md) .

## <a name="metrics"></a>Metryki

Ta sekcja zawiera listę wszystkich automatycznie zebranych metryk platformy dla IoT Hub platformy Azure. Przestrzeń nazw dostawcy zasobów dla metryk IoT Hub to **Microsoft. Devices** i typ przestrzeni nazw to **IoTHubs** .

Poniższe podsekcje dzielą metryki platformy IoT Hub według ogólnej kategorii i wyświetlają je według nazwy, która pojawia się w Azure Portal za pomocą. Informacje są również dostępne dla metryk, które pojawiają się w każdej podsekcji.

Możesz również znaleźć jedną tabelę zawierającą wszystkie IoT Hub metryki platformy według nazwy metryki w obszarze [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/metrics-supported#microsoftdevicesiothubs) w dokumentacji Azure monitor. Należy pamiętać, że ta tabela nie zawiera niektórych informacji, takich jak [obsługiwane agregacje](#supported-aggregations) dla niektórych metryk, które są dostępne w tym artykule.

Aby dowiedzieć się więcej o metrykach obsługiwanych przez inne usługi platformy Azure, zobacz temat [obsługiwane metryki w Azure monitor](/azure/azure-monitor/platform/metrics-supported).

**Tematy w tej sekcji**

- [Obsługiwane agregacje](#supported-aggregations)
- [Metryki poleceń z chmury do urządzenia](#cloud-to-device-command-metrics)
- [Metryki metod bezpośrednich z chmury do urządzenia](#cloud-to-device-direct-methods-metrics)
- [Metryki operacji w chmurze do urządzenia](#cloud-to-device-twin-operations-metrics)
- [Metryki konfiguracji](#configurations-metrics)
- [Metryki dziennego limitu przydziału](#daily-quota-metrics)
- [Metryki urządzeń](#device-metrics)
- [Metryki telemetrii urządzenia](#device-telemetry-metrics)
- [Metryki dotyczące urządzeń z przędzą w chmurze](#device-to-cloud-twin-operations-metrics)
- [Metryki dotyczące siatki zdarzeń](#event-grid-metrics)
- [Metryki zadań](#jobs-metrics)
- [Metryki routingu](#routing-metrics)
- [Metryki zapytań bliźniaczych](#twin-query-metrics)

### <a name="supported-aggregations"></a>Obsługiwane agregacje

Kolumna **typ agregacji** w każdej tabeli odpowiada agregacji domyślnej, która jest używana, gdy Metryka została wybrana dla wykresu lub alertu.

   ![Zrzut ekranu przedstawiający agregację dla metryk](./media/monitor-iot-hub-reference/aggregation-type.png)

W przypadku większości metryk wszystkie typy agregacji są prawidłowe; Jednak w przypadku metryk zliczania, które mają wartość kolumny **jednostki** **Count** , tylko niektóre agregacje są prawidłowe. Metryki zliczania mogą być jednym z dwóch typów:

* W przypadku metryk zliczania **pojedynczych punktów** IoT Hub rejestruje pojedynczy punkt danych — zasadniczo 1---za każdym razem, gdy nastąpi pomiar operacji. Azure Monitor następnie sumuje te punkty danych w ramach określonego stopnia szczegółowości. Przykładami metryk **pojedynczych punktów** są *wysyłane komunikaty telemetryczne* i *Zakończono dostarczanie komunikatów C2D* . Dla tych metryk jedynym odpowiednim typem agregacji jest suma (sum). Portal umożliwia wybranie opcji minimum, maksimum i średnika; te wartości będą jednak zawsze 1.

* W przypadku metryk zliczania **migawek** IoT Hub rejestruje łączną liczbę w przypadku wystąpienia operacji mierzonej. Obecnie istnieją trzy metryki **migawek** wyemitowane przez IoT Hub: *całkowita liczba użytych komunikatów* , *łączne urządzenia (wersja zapoznawcza)* i *połączone urządzenia (wersja zapoznawcza)* . Ponieważ te metryki stanowią "łączną ilość" przy każdej emisji, sumowanie ich od określonego stopnia szczegółowości nie ma sensu. Azure Monitor ogranicza wybór wartości średniej, minimalnej i maksymalnej dla typu agregacji dla tych metryk.

### <a name="cloud-to-device-command-metrics"></a>Metryki poleceń z chmury do urządzenia

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Komunikaty C2D wygasły (wersja zapoznawcza)|C2DMessagesExpired|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|Zakończono dostarczanie komunikatów C2D|C2D. Commands. ruch wychodzący.<br>Ukończono. powodzenie|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak|
|Porzucone komunikaty C2D|C2D. Commands. ruch wychodzący.<br>Porzuć. sukces|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|Odrzucone komunikaty C2D|C2D. Commands. ruch wychodzący.<br>Odrzuć. powodzenie|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Metryki metod bezpośrednich z chmury do urządzenia

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Nieudane wywołania metody bezpośredniej|C2D. Methods. Failure|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak|
|Rozmiar żądania wywołań metody bezpośredniej|C2D. Methods. requestSize|Bajty|Średnia|Liczba wszystkich pomyślnych żądań metody bezpośredniej.|Brak|
|Rozmiar odpowiedzi wywołań metody bezpośredniej|C2D. Methods. responseSize|Bajty|Średnia|Liczba wszystkich pomyślnych odpowiedzi metody bezpośredniej.|Brak|
|Pomyślne wywołania metody bezpośredniej|C2D. Methods. Success|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak|

W przypadku metryk z wartością **jednostkową** agregacji Total (suma) jest prawidłowa. **Count** Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Metryki operacji w chmurze do urządzenia

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Nieudane odczyty sznurów z zaplecza|C2D. splot. Read. Failure|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|Niepowodzenie aktualizacji bliźniaczych z zaplecza|C2D. splot. Update. Failure|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|Rozmiar odpowiedzi na odwrocie od zaplecza|C2D. splot. Read. size|Bajty|Średnia|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|Rozmiar aktualizacji przędzy od zaplecza|C2D. splot. Update. size|Bajty|Średnia|Łączny rozmiar wszystkich pomyślnych zainicjowanych z powrotem sznurów danych.|Brak|
|Pomyślne odczyty sznurów z zaplecza|C2D. splot. Read. Success|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|Pomyślne aktualizacje bliźniaczych z zaplecza|C2D. splot. Update. Success|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="configurations-metrics"></a>Metryki konfiguracji

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Metryki konfiguracji|komputerów|Liczba|Łącznie|Łączna liczba operacji CRUD wykonanych na potrzeby konfigurowania urządzeń i wdrażania IoT Edge na zestawie urządzeń docelowych. Obejmuje to również liczbę operacji modyfikujących sznurki lub sznurki urządzenia ze względu na te konfiguracje.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Metryki dziennego limitu przydziału

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Całkowite użycie danych urządzenia|deviceDataUsage|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|Całkowite użycie danych urządzenia (wersja zapoznawcza)|deviceDataUsageV2|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|Całkowita liczba użytych komunikatów|dailyMessageQuotaUsed|Liczba|Średnia|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Brak|

W przypadku *całkowitej liczby użytych komunikatów* obsługiwane są tylko agregacje minimalne, maksymalne i średnie. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="device-metrics"></a>Metryki urządzeń

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna liczba urządzeń (przestarzałe)|Devices. totalDevices|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|Podłączone urządzenia (przestarzałe) |Devices. connectedDevices.<br>allProtocol|Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|Łączna liczba urządzeń (wersja zapoznawcza)|totalDeviceCount|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|Podłączone urządzenia (wersja zapoznawcza)|connectedDeviceCount|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|

W przypadku *urządzeń łącznie (przestarzałych)* i *podłączonych urządzeń (przestarzałe)* jest prawidłowa tylko agregacja SUM (sum). Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

W przypadku *łącznej liczby urządzeń (wersja zapoznawcza)* i *podłączonych urządzeń (wersja zapoznawcza)* są dozwolone tylko agregacje minimalne, maksymalne i średnie. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

*Urządzenia połączone (wersja zapoznawcza)* i *łączna liczba urządzeń (wersja zapoznawcza)* nie są eksportowane za pośrednictwem ustawień diagnostycznych.

### <a name="device-telemetry-metrics"></a>Metryki telemetrii urządzenia

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Liczba błędów ograniczania|D2C. telemetrię. dane wejściowe.<br>sendThrottle|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak|
|Próby wysłania komunikatów telemetrycznych|D2C. telemetrię. dane wejściowe.<br>allProtocol|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak|
|Wysłane komunikaty telemetryczne|D2C. telemetrię. dane wejściowe.<br>powodzenie|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Metryki dotyczące urządzeń z przędzą w chmurze

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Nieudane odczyty sznurów z urządzeń|D2C. splot. Read. Failure|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak|
|Niepowodzenie aktualizacji bliźniaczych z urządzeń|D2C. splot. Update. Failure|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak|
|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|D2C. splot. Read. size|Bajty|Średnia|Liczba wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak|
|Rozmiar aktualizacji bliźniaczych z urządzeń|D2C. splot. Update. size|Bajty|Średnia|Łączny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanego przez urządzenie.|Brak|
|Pomyślne odczyty sznurów z urządzeń|D2C. splot. Read. Success|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak|
|Pomyślne aktualizacje bliźniaczych urządzeń|D2C. splot. Update. Success|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="event-grid-metrics"></a>Metryki dotyczące siatki zdarzeń

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Dostawy Event Grid (wersja zapoznawcza)|EventGridDeliveries|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia ( https://aka.ms/ioteventgrid) .|Wynik:<br/>Typ zdarzenia<br>*Aby uzyskać więcej informacji, zobacz [Dimension Metrics](#metric-dimensions)* .|
|Opóźnienie Event Grid (wersja zapoznawcza)|EventGridLatency|)|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Typ zdarzenia<br>*Aby uzyskać więcej informacji, zobacz [Dimension Metrics](#metric-dimensions)* .|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="jobs-metrics"></a>Metryki zadań

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ukończone zadania|zadania. ukończone|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|Wywołania zakończone niepowodzeniem do listy zadań|Jobs. listJobs. Failure|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak|
|Nie można utworzyć zadań wywołania metody|Jobs. createDirectMethodJob.<br>spraw|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Nie można utworzyć dwuosiowych zadań aktualizacji|Jobs. createTwinUpdateJob.<br>spraw|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak|
|Nieudane anulowania zadań|Jobs. cancelJob. Failure|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak|
|Nieudane kwerendy zadań|Jobs. queryJobs. Failure|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak|
|Zadania zakończone niepowodzeniem|zadania. Niepowodzenie|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak|
|Pomyślne wywołania do zadań na liście|Jobs. listJobs. Success|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|Pomyślne utworzenie zadań wywołania metody|Jobs. createDirectMethodJob.<br>powodzenie|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Jobs. createTwinUpdateJob.<br>powodzenie|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak|
|Pomyślne anulowania zadań|Jobs. cancelJob. Success|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak|
|Pomyślne zapytania dotyczące zadań|Jobs. queryJobs. Success|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="routing-metrics"></a>Metryki routingu

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
| Próby dostarczania routingu (wersja zapoznawcza) |RoutingDeliveries | Liczba | Łącznie |Jest to metryka dostarczania routingu. Wymiary służą do identyfikowania stanu dostawy dla określonego punktu końcowego lub dla określonego źródła routingu.| Wynik:<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Nazwapunktukoncowego<br>*Aby uzyskać więcej informacji, zobacz [Dimension Metrics](#metric-dimensions)* . |
| Rozmiar danych dostarczania routingu (w bajtach) (wersja zapoznawcza)|RoutingDataSizeInBytesDelivered| Bajty | Łącznie |Całkowita liczba bajtów kierowanych przez IoT Hub do niestandardowego punktu końcowego i wbudowanego punktu końcowego. Wymiary umożliwiają identyfikowanie rozmiaru danych kierowanych do określonego punktu końcowego lub dla określonego źródła routingu.| RoutingSource,<br>Punkt końcowy<br>Nazwapunktukoncowego<br>*Aby uzyskać więcej informacji, zobacz [Dimension Metrics](#metric-dimensions)* .|
| Opóźnienie routingu (wersja zapoznawcza) |RoutingDeliveryLatency| ) | Średnia |Jest to metryka opóźnienia dostarczania routingu. Wymiary umożliwiają zidentyfikowanie opóźnienia dla określonego punktu końcowego lub dla określonego źródła routingu.| RoutingSource,<br>EndpointType,<br>Nazwapunktukoncowego<br>*Aby uzyskać więcej informacji, zobacz [Dimension Metrics](#metric-dimensions)* .|
|Routing: obiekty blob dostarczane do magazynu|D2C. endpoints. ruch wychodzący.<br>Storage. Blobs|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak|
|Routing: dane dostarczane do magazynu|D2C. endpoints. ruch wychodzący.<br>Storage. Bytes|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak|
|Routing: opóźnienie komunikatu dla centrum zdarzeń|D2C. endpoints. opóźnienie.<br>eventHubs|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do niestandardowych punktów końcowych typu centrum zdarzeń. Nie obejmuje to tras komunikatów do wbudowanego punktu końcowego (zdarzenia).|Brak|
|Routing: opóźnienie komunikatu dla kolejki Service Bus|D2C. endpoints. opóźnienie.<br>serviceBusQueues|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak|
|Routing: opóźnienie komunikatu dla Service Bus tematu|D2C. endpoints. opóźnienie.<br>serviceBusTopics|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do punktu końcowego tematu Service Bus.|Brak|
|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|D2C. endpoints. opóźnienie.<br>Wbudowane. Events|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do wbudowanego punktu końcowego (komunikaty/zdarzenia) i trasy rezerwowej.|Brak|
|Routing: opóźnienie komunikatu dla magazynu|D2C. endpoints. opóźnienie.<br>magazyn|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym magazynu.|Brak|
|Routing: komunikaty dostarczane do centrum zdarzeń|D2C. endpoints. ruch wychodzący.<br>eventHubs|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do niestandardowych punktów końcowych typu centrum zdarzeń w usłudze IoT Hub Routing. Nie obejmuje to tras komunikatów do wbudowanego punktu końcowego (zdarzenia).|Brak|
|Routing: komunikaty dostarczone do kolejki Service Bus|D2C. endpoints. ruch wychodzący.<br>serviceBusQueues|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak|
|Routing: komunikaty dostarczane do Service Bus tematu|D2C. endpoints. ruch wychodzący.<br>serviceBusTopics|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak|
|Routing: komunikaty dostarczane do powrotu|D2C. telemetrię. ruch wychodzący.<br>istnie|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak|
|Routing: komunikaty dostarczane do komunikatów/zdarzeń|D2C. endpoints. ruch wychodzący.<br>Wbudowane. Events|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) i trasy rezerwowej IoT Hub.|Brak|
|Routing: komunikaty dostarczane do magazynu|D2C. endpoints. ruch wychodzący.<br>magazyn|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak|
|Routing: dostarczono komunikaty telemetryczne|D2C. telemetrię. ruch wychodzący.<br>powodzenie|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak|
|Routing: porzucone komunikaty telemetryczne |D2C. telemetrię. ruch wychodzący.<br>porzucony|Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak|
|Routing: komunikaty telemetryczne są niezgodne|D2C. telemetrię. ruch wychodzący.<br>nieprawidłowe|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Komunikat jest niezgodny z punktem końcowym, gdy program IoT Hub próbuje dostarczyć komunikat do punktu końcowego i kończy się niepowodzeniem z powodu błędu nieprzejściowego. Nieprawidłowe wiadomości nie są ponawiane. Ta wartość nie obejmuje ponownych prób.|Brak|
|Routing: oddzielone komunikaty telemetryczne |D2C. telemetrię. ruch wychodzący.<br>oddzielone|Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną kwerendą routingu, gdy wartość trasy rezerwowej jest wyłączona.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

### <a name="twin-query-metrics"></a>Metryki zapytań bliźniaczych

|Nazwa wyświetlana metryki|Metryka|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Niepowodzenie zapytań bliźniaczych|twinQueries. Failure|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak|
|Pomyślne zapytania bliźniaczy|twinQueries. Success|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak|
|Rozmiar wyniku zapytań bliźniaczych|twinQueries.resultSize|Bajty|Średnia|Całkowita wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak|

W przypadku metryk z wartością **jednostki** **Count** tylko suma (sum) jest prawidłowa. Agregacje minimalna, maksymalna i średnia zawsze zwracają wartość 1. Aby uzyskać więcej informacji, zobacz [obsługiwane agregacje](#supported-aggregations).

## <a name="metric-dimensions"></a>Wymiary metryk

Usługa Azure IoT Hub ma następujące wymiary skojarzone z niektórymi metrykami routingu i usługi Event Grid.

|Nazwa wymiaru | Opis|
|---|---|
||
|**Nazwapunktukoncowego**| Nazwa punktu końcowego.|
|**Punkt końcowy**|Jedną z następujących wartości: **eventHubs** , **serviceBusQueues** , **cosmosDB** , **serviceBusTopics** . **wbudowane** lub **blobStorage** .|
|**Klasę**| Jeden z następujących Event Grid typów zdarzeń: **Microsoft. Devices. DeviceCreated** . **Microsoft. Devices. DeviceDeleted** , **Microsoft. Devices. DeviceConnected** , **Microsoft. Devices. DeviceDisconnected** , lub **Microsoft. Devices. DeviceTelemetry** . Aby uzyskać więcej informacji, zobacz [typy zdarzeń](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Jeden z następujących: **nieprawidłowy** , **porzucony** , **oddzielony** lub **zerowy** .|
|**Wynik**| **Powodzenie** lub **Niepowodzenie** .|
|**RoutingSource**| Komunikaty urządzenia<br>Zdarzenia zmiany bliźniaczych<br>Zdarzenia cyklu życia urządzenia|

Aby dowiedzieć się więcej na temat wymiarów metryk, zobacz [wielowymiarowe metryki](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

## <a name="resource-logs"></a>Dzienniki zasobów

Ta sekcja zawiera listę wszystkich typów kategorii dzienników zasobów i schematów zebranych dla IoT Hub platformy Azure. Dostawca zasobów i typ wszystkich dzienników IoT Hub to [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/resource-logs-categories#microsoftdevicesiothubs).

**Tematy w tej sekcji**

- [Połączenia](#connections)
- [Dane telemetryczne urządzenia](#device-telemetry)
- [Polecenia chmura-urządzenie](#cloud-to-device-commands)
- [Operacje tożsamości urządzeń](#device-identity-operations)
- [Operacje przekazywania plików](#file-upload-operations)
- [Trasy](#routes)
- [Operacje wieloosiowe między urządzeniami a chmurą](#device-to-cloud-twin-operations)
- [Operacje na sznurze z chmury do urządzenia](#cloud-to-device-twin-operations)
- [Zapytania bliźniaczye](#twin-queries)
- [Operacje zadań](#jobs-operations)
- [Metody bezpośrednie](#direct-methods)
- [Śledzenie rozproszone (wersja zapoznawcza)](#distributed-tracing-preview)
  - [Dzienniki IoT Hub D2C (urządzenie-chmura)](#iot-hub-d2c-device-to-cloud-logs)
  - [IoT Hub dzienniki danych wejściowych](#iot-hub-ingress-logs)
  - [Dzienniki wychodzące IoT Hub](#iot-hub-egress-logs)
- [Konfiguracje](#configurations)
- [Strumienie urządzeń (wersja zapoznawcza)](#device-streams-preview)

### <a name="connections"></a>Połączenia

Kategoria połączenia śledzi zdarzenia łączenia i rozłączania urządzeń z Centrum IoT Hub, a także błędy. Ta kategoria jest przydatna do identyfikowania nieautoryzowanych prób połączeń i alertów w przypadku utraty połączenia z urządzeniami.

> [!NOTE]
> W przypadku stanu niezawodnego połączenia urządzenia sprawdź [puls urządzenia](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Dane telemetryczne urządzenia

Kategoria telemetrii urządzenia śledzi błędy występujące w centrum IoT i są powiązane z potokiem telemetrii. Ta kategoria zawiera błędy występujące podczas wysyłania zdarzeń telemetrii (takich jak ograniczenie przepustowości) i otrzymywanie zdarzeń telemetrii (na przykład nieautoryzowanego czytnika). Ta kategoria nie może przechwytywać błędów spowodowanych przez kod uruchomiony na urządzeniu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Polecenia chmura-urządzenie

Kategoria poleceń z chmury do urządzenia śledzi błędy występujące w centrum IoT Hub i powiązane z potokiem komunikatów z chmury do urządzenia. Ta kategoria zawiera błędy występujące w programie:

* Wysyłanie komunikatów z chmury do urządzeń (takich jak błędy nieautoryzowanego nadawcy),
* Otrzymywanie komunikatów z chmury do urządzeń (takich jak liczba błędów przekraczania liczby dostaw) i
* Otrzymywanie informacji zwrotnych z chmury do urządzenia (na przykład błędów ważności opinii).

Ta kategoria nie przechwytuje błędów, gdy komunikat z chmury do urządzenia jest dostarczany pomyślnie, ale nieprawidłowo obsłużony przez urządzenie.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operacje tożsamości urządzeń

Kategoria operacje tożsamości urządzenia śledzi błędy występujące podczas próby utworzenia, zaktualizowania lub usunięcia wpisu w rejestrze tożsamości Centrum IoT. Śledzenie tej kategorii jest przydatne w scenariuszach aprowizacji.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operacje przekazywania plików

Kategoria przekazywanie plików śledzi błędy występujące w centrum IoT Hub i są powiązane z funkcją przekazywania plików. Ta kategoria obejmuje:

* Błędy występujące przy użyciu identyfikatora URI sygnatury dostępu współdzielonego, na przykład kiedy wygasa przed powiadomieniem centrum o ukończonym przekazywaniu przez urządzenie.

* Przekazywanie zgłoszone przez urządzenie nie powiodło się.

* Błędy występujące, gdy podczas tworzenia komunikatu powiadomienia IoT Hub nie znaleziono pliku w magazynie.

Ta kategoria nie może przechwytywać błędów, które są bezpośrednio wykonywane, gdy urządzenie przekazuje plik do magazynu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Trasy

Kategoria [routingu wiadomości](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) śledzi błędy występujące podczas oceny trasy komunikatów i kondycji punktu końcowego, jak zostało to postrzegane przez IoT Hub. Ta kategoria obejmuje następujące zdarzenia:

* Reguła ma wartość "undefined".
* IoT Hub oznacza punkt końcowy jako martwy lub
* Wszystkie błędy odebrane z punktu końcowego.

Ta kategoria nie zawiera określonych błędów dotyczących samych komunikatów (takich jak błędy ograniczania urządzeń), które są raportowane w kategorii "dane telemetryczne urządzenia".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Poniżej przedstawiono więcej szczegółów dotyczących routingu dzienników zasobów:

* [Lista kodów błędów dzienników zasobów routingu](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista dzienników zasobów routingu operationNames](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Operacje wieloosiowe między urządzeniami a chmurą

Kategoria operacji ze osiową między urządzeniami a chmurą śledzi zdarzenia zainicjowane przez urządzenie w usłudze Device bliźniaczych reprezentacji. Te operacje mogą obejmować pobieranie sznurka, aktualizowanie raportowanych właściwości i subskrybowanie żądanych właściwości.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Operacje na sznurze z chmury do urządzenia

Kategoria operacji dla sznurów z chmury do urządzenia śledzi zdarzenia zainicjowane przez usługę na urządzeniu bliźniaczych reprezentacji. Operacje te mogą obejmować pobieranie, aktualizowanie i zastępowanie tagów oraz aktualizowanie lub zastępowanie żądanych właściwości.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Zapytania bliźniaczye

Kategoria zapytania dwuosiowe raportuje żądania zapytań dotyczące bliźniaczych reprezentacji urządzeń, które są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operacje zadań

Kategoria operacje zadań raportuje żądania zadań, aby zaktualizować bliźniaczych reprezentacji urządzeń lub wywołać metody bezpośrednie na wielu urządzeniach. Te żądania są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Metody bezpośrednie

Kategoria metody bezpośrednie śledzi interakcje z żądaniem odpowiedzi wysyłane do poszczególnych urządzeń. Te żądania są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Śledzenie rozproszone (wersja zapoznawcza)

Kategoria śledzenie rozproszone śledzi identyfikatory korelacji dla komunikatów, które zawierają nagłówek kontekstu śledzenia. Aby w pełni włączyć te dzienniki, kod po stronie klienta należy zaktualizować, wykonując następujące czynności: [analizowanie i diagnozowanie aplikacji IoT w ramach kompleksowego śledzenia IoT Hub (wersja zapoznawcza)](iot-hub-distributed-tracing.md).

Należy pamiętać, że jest to zgodne z `correlationId` propozycją w [kontekście śledzenia W3C](https://github.com/w3c/trace-context) , w której znajduje się, a także `trace-id` `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Dzienniki IoT Hub D2C (urządzenie-chmura)

IoT Hub rejestruje ten dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia dociera do IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

W tym miejscu `durationMs` nie jest obliczany, ponieważ zegar IoT Hub może nie być zsynchronizowany z zegarem urządzenia i w ten sposób Obliczanie czasu trwania może być mylące. Zalecamy zapisanie logiki przy użyciu sygnatur czasowych w `properties` sekcji, aby przechwytywać opóźnienia w czasie oczekiwania między urządzeniami a chmurą.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Liczba całkowita | Rozmiar komunikatu z urządzenia do chmury w bajtach |
| **deviceId** | Ciąg znaków alfanumerycznych ASCII 7-bitowych | Tożsamość urządzenia |
| **callerLocalTimeUtc** | Sygnatura czasowa UTC | Godzina utworzenia komunikatu zgłoszonego przez zegar lokalny urządzenia |
| **calleeLocalTimeUtc** | Sygnatura czasowa UTC | Godzina przybycia wiadomości w bramie IoT Hubej zgłoszonej przez IoT Hub zegar po stronie usługi |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub dzienniki danych wejściowych

IoT Hub rejestruje ten dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia jest zapisywany w wewnętrznym lub wbudowanym centrum zdarzeń.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

W `properties` sekcji ten dziennik zawiera dodatkowe informacje na temat przychodzących komunikatów.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Wartość true lub false wskazuje, czy w IoT Hub jest włączona funkcja routingu komunikatów |
| **parentSpanId** | String | [Identyfikator zakresu](https://w3c.github.io/trace-context/#parent-id) komunikatu nadrzędnego, który będzie w tym przypadku D2C śledzenia komunikatów |

#### <a name="iot-hub-egress-logs"></a>Dzienniki wychodzące IoT Hub

IoT Hub rejestruje ten dziennik, gdy [Routing](iot-hub-devguide-messages-d2c.md) jest włączony, a komunikat jest zapisywana w [punkcie końcowym](iot-hub-devguide-endpoints.md). Jeśli Routing nie jest włączony, IoT Hub nie rejestruje tego dziennika.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

W `properties` sekcji ten dziennik zawiera dodatkowe informacje na temat przychodzących komunikatów.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Nazwapunktukoncowego** | String | Nazwa punktu końcowego routingu |
| **punkt końcowy** | String | Typ punktu końcowego routingu |
| **parentSpanId** | String | [Identyfikator zakresu](https://w3c.github.io/trace-context/#parent-id) komunikatu nadrzędnego, który będzie IoT Hub śledzenia komunikatów przychodzących w tym przypadku |

### <a name="configurations"></a>Konfiguracje

Dzienniki konfiguracji IoT Hub śledzą zdarzenia i błędy dla zestawu funkcji automatycznego zarządzania urządzeniami.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Strumienie urządzeń (wersja zapoznawcza)

Kategoria strumienie urządzenia śledzi interakcje z żądaniem odpowiedzi wysyłane do poszczególnych urządzeń.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabele dzienników Azure Monitor
<!-- REQUIRED. Please keep heading in this order -->

Ta sekcja odnosi się do wszystkich dzienników Azure Monitor, które odnoszą się do usługi Azure IoT Hub i są dostępne dla zapytań Log Analytics. Aby uzyskać listę tych tabel i linki do dodatkowych informacji dotyczących typu zasobu IoT Hub, zobacz [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) w tabeli dzienników Azure monitor.

Aby uzyskać informacje na temat wszystkich Azure Monitor dzienników/tabel Log Analytics, zobacz [Dokumentacja tabeli dziennika Azure monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Zobacz też

* Aby uzyskać opis monitorowania IoT Hub platformy Azure, zobacz artykuł [monitorowanie usługi azure IoT Hub](monitor-iot-hub.md) .
* Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resources) .
