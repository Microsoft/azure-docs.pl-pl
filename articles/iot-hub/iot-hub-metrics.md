---
title: Używanie metryk do monitorowania IoT Hub platformy Azure | Microsoft Docs
description: Jak oceniać i monitorować ogólną kondycję centrów IoT przy użyciu metryk IoT Hub platformy Azure.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: c448d7e5a5e0bea29063930bed3a59a0461b8cf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767615"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>Informacje o metrykach IoT Hub

Metryki IoT Hub zawierają informacje o stanie zasobów usługi Azure IoT w ramach subskrypcji platformy Azure. Metryki IoT Hub pomagają w ocenie ogólnej kondycji usługi IoT Hub i podłączonych do niej urządzeń. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z Centrum IoT Hub, i pomóc w analizie przyczyny problemów, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki IoT Hub z Azure Portal.

> [!NOTE]
> Za pomocą metryk IoT Hub można wyświetlać informacje o urządzeniach [Plug and Play IoT](../iot-pnp/overview-iot-plug-and-play.md) podłączonych do IoT Hub.

## <a name="how-to-view-iot-hub-metrics"></a>Jak wyświetlić metryki IoT Hub

1. Utwórz centrum IoT. Instrukcje dotyczące sposobu tworzenia Centrum IoT można znaleźć w przewodniku wysyłanie danych [telemetrycznych z urządzenia do IoT Hub](quickstart-send-telemetry-dotnet.md) .

2. Otwórz blok Centrum IoT. Z tego miejsca kliknij pozycję **metryki**.
   
    ![Zrzut ekranu przedstawiający, gdzie znajduje się opcja metryki na stronie zasobów IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. W bloku metryk można wyświetlić metryki Centrum IoT i utworzyć niestandardowe widoki metryk. 
   
    ![Zrzut ekranu przedstawiający stronę metryki dla IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, klikając pozycję **Ustawienia diagnostyczne**, a następnie **Dodaj ustawienie diagnostyczne**.

   ![Zrzut ekranu przedstawiający przycisk ustawień diagnostycznych](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metryki i sposoby ich używania

IoT Hub zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji centrum oraz łącznej liczbie połączonych urządzeń. Możesz połączyć informacje z wielu metryk, aby malować większy obraz stanu Centrum IoT. W poniższej tabeli opisano metryki poszczególnych ścieżek Centrum IoT oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu Centrum IoT Hub.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RoutingDeliveries | Próby dostarczania routingu (wersja zapoznawcza) | Liczba | Łącznie |Jest to metryka dostarczania routingu. Wymiary służą do identyfikowania stanu dostawy dla określonego punktu końcowego lub dla określonego źródła routingu.| Identyfikator<br>Wynik:<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Nazwapunktukoncowego<br>*Więcej szczegółowych informacji na temat wymiarów [**znajdziesz tutaj**](#dimensions)*. |
|RoutingDeliveryLatency| Opóźnienie routingu (wersja zapoznawcza) | ) | Średnia |Jest to metryka opóźnienia dostarczania routingu. Wymiary umożliwiają zidentyfikowanie opóźnienia dla określonego punktu końcowego lub dla określonego źródła routingu.| Identyfikator<br>RoutingSource,<br>EndpointType,<br>Nazwapunktukoncowego<br>*Więcej szczegółowych informacji na temat wymiarów [**znajdziesz tutaj**](#dimensions)*.|
|RoutingDataSizeInBytesDelivered| Rozmiar danych dostarczania routingu (w bajtach) (wersja zapoznawcza)| Bajty | Łącznie |Całkowita liczba bajtów kierowanych przez IoT Hub do niestandardowego punktu końcowego i wbudowanego punktu końcowego. Wymiary umożliwiają identyfikowanie rozmiaru danych kierowanych do określonego punktu końcowego lub dla określonego źródła routingu.| Identyfikator<br>RoutingSource,<br>Punkt końcowy<br>Nazwapunktukoncowego<br>*Więcej szczegółowych informacji na temat wymiarów [**znajdziesz tutaj**](#dimensions)*.|
|D2C. telemetrię. dane wejściowe.<br>allProtocol|Próby wysłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak|
|D2C. telemetrię. dane wejściowe.<br>powodzenie|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak|
|C2D. Commands. ruch wychodzący.<br>Ukończono. powodzenie|Zakończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak|
|C2D. Commands. ruch wychodzący.<br>Porzuć. sukces|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|C2D. Commands. ruch wychodzący.<br>Odrzuć. powodzenie|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|
|C2DMessagesExpired|Komunikaty C2D wygasły (wersja zapoznawcza)|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|Devices. connectedDevices.<br>allProtocol|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|D2C. telemetrię. ruch wychodzący.<br>powodzenie|Routing: dostarczono komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak|
|D2C. telemetrię. ruch wychodzący.<br>porzucony|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak|
|D2C. telemetrię. ruch wychodzący.<br>oddzielone|Routing: oddzielone komunikaty telemetryczne |Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną kwerendą routingu, gdy wartość trasy rezerwowej jest wyłączona.|Brak|
|D2C. telemetrię. ruch wychodzący.<br>nieprawidłowe|Routing: komunikaty telemetryczne są niezgodne|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Komunikat jest niezgodny z punktem końcowym, gdy Centrum IoT podejmie próbę dostarczenia komunikatu do punktu końcowego i kończy się niepowodzeniem z powodu błędu przejściowego. Nieprawidłowe wiadomości nie są ponawiane. Ta wartość nie obejmuje ponownych prób.|Brak|
|D2C. telemetrię. ruch wychodzący.<br>istnie|Routing: komunikaty dostarczane do powrotu|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak|
|D2C. endpoints. ruch wychodzący.<br>eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do niestandardowych punktów końcowych typu centrum zdarzeń w usłudze IoT Hub Routing. Nie obejmuje to tras komunikatów do wbudowanego punktu końcowego (zdarzenia).|Brak|
|D2C. endpoints. opóźnienie.<br>eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do niestandardowych punktów końcowych typu centrum zdarzeń. Nie obejmuje to tras komunikatów do wbudowanego punktu końcowego (zdarzenia).|Brak|
|D2C. endpoints. ruch wychodzący.<br>serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak|
|D2C. endpoints. opóźnienie.<br>serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak|
|D2C. endpoints. ruch wychodzący.<br>serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak|
|D2C. endpoints. opóźnienie.<br>serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do punktu końcowego tematu Service Bus.|Brak|
|D2C. endpoints. ruch wychodzący.<br>Wbudowane. Events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) i trasy rezerwowej IoT Hub.|Brak|
|D2C. endpoints. opóźnienie.<br>Wbudowane. Events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących do wbudowanego punktu końcowego (komunikaty/zdarzenia) i trasy rezerwowej.|Brak|
|D2C. endpoints. ruch wychodzący.<br>magazyn|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak|
|D2C. endpoints. opóźnienie.<br>magazyn|Routing: opóźnienie komunikatu dla magazynu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym magazynu.|Brak|
|D2C. endpoints. ruch wychodzący.<br>Storage. Bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak|
|D2C. endpoints. ruch wychodzący.<br>Storage. Blobs|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia ( https://aka.ms/ioteventgrid) .|Identyfikator<br/>Wynik:<br/>Typ zdarzenia|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|)|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Identyfikator<br/>Typ zdarzenia|
|D2C. splot. Read. Success|Pomyślne odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Read. Failure|Nieudane odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Read. size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Średnia|Liczba wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak|
|D2C. splot. Update. Success|Pomyślne aktualizacje bliźniaczych urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Update. size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Łączny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanego przez urządzenie.|Brak|
|C2D. Methods. Success|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak|
|C2D. Methods. requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Liczba wszystkich pomyślnych żądań metody bezpośredniej.|Brak|
|C2D. Methods. responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Liczba wszystkich pomyślnych odpowiedzi metody bezpośredniej.|Brak|
|C2D. splot. Read. Success|Pomyślne odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|C2D. splot. Read. Failure|Nieudane odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|C2D. splot. Read. size|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Średnia|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|C2D. splot. Update. Success|Pomyślne aktualizacje bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|C2D. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|C2D. splot. Update. size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Łączny rozmiar wszystkich pomyślnych zainicjowanych z powrotem sznurów danych.|Brak|
|twinQueries. Success|Pomyślne zapytania bliźniaczy|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak|
|twinQueries. Failure|Niepowodzenie zapytań bliźniaczych|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Całkowita wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak|
|Jobs. createTwinUpdateJob.<br>powodzenie|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak|
|Jobs. createTwinUpdateJob.<br>spraw|Nie można utworzyć dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak|
|Jobs. createDirectMethodJob.<br>powodzenie|Pomyślne utworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Jobs. createDirectMethodJob.<br>spraw|Nie można utworzyć zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Jobs. listJobs. Success|Pomyślne wywołania do zadań na liście|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|Jobs. listJobs. Failure|Wywołania zakończone niepowodzeniem do listy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak|
|Jobs. cancelJob. Success|Pomyślne anulowania zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak|
|Jobs. cancelJob. Failure|Nieudane anulowania zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak|
|Jobs. queryJobs. Success|Pomyślne zapytania dotyczące zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak|
|Jobs. queryJobs. Failure|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak|
|zadania. ukończone|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak|
|D2C. telemetrię. dane wejściowe.<br>sendThrottle|Liczba błędów ograniczania|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Liczba|Średnia|Całkowita liczba użytych komunikatów. Jest to skumulowana wartość, która jest resetowana do zera o godzinie 00:00 czasu każdego dnia.|Brak|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|komputerów|Metryki konfiguracji|Liczba|Łącznie|Łączna liczba operacji CRUD wykonanych na potrzeby konfigurowania urządzeń i wdrażania IoT Edge na zestawie urządzeń docelowych. Obejmuje to również liczbę operacji modyfikujących sznurki lub sznurki urządzenia ze względu na te konfiguracje.|Brak|

### <a name="dimensions"></a>Wymiary
Wymiary ułatwiają znalezienie dodatkowych informacji o metrykach. Niektóre metryki routingu zawierają informacje na punkt końcowy. Poniższa tabela zawiera listę możliwych wartości dla tych wymiarów.

|Wymiar|Wartości|
|---|---|
|ResourceID|zasób IoT Hub|
|Wynik|powodzenie<br>spraw|
|RoutingSource|Komunikaty urządzenia<br>Zdarzenia zmiany bliźniaczych<br>Zdarzenia cyklu życia urządzenia|
|Punkt końcowy|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>wbudowan<br>blobStorage|
|FailureReasonCategory|nieprawidłowe<br>porzucony<br>oddzielone<br>wartość null|
|Nazwapunktukoncowego|Nazwa punktu końcowego|

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z omówieniem metryk IoT Hub, Skorzystaj z tego linku, aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub:

* [Konfigurowanie dzienników diagnostycznych](iot-hub-monitor-resource-health.md)

* [Dowiedz się, jak rozwiązywać problemy z routingiem komunikatów](troubleshoot-message-routing.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
