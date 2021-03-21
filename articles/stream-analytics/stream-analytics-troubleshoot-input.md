---
title: Rozwiązywanie problemów z danymi wejściowymi dla Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wejściowymi w Azure Stream Analytics zadaniach.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019911"
---
# <a name="troubleshoot-input-connections"></a>Rozwiązywanie problemów z połączeniami wejściowymi

W tym artykule opisano typowe problemy związane z Azure Stream Analytics połączeniami wejściowymi, rozwiązywanie problemów z danymi wejściowymi oraz sposób rozwiązania problemów. Wiele kroków rozwiązywania problemów wymaga włączenia dzienników zasobów dla zadania Stream Analytics. Jeśli nie masz włączonych dzienników zasobów, zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Zdarzenia wejściowe nieodebrane przez zadanie 

1.  Przetestuj połączenie danych wejściowych i wyjściowych. Sprawdź łączność z wejściami i wyjściami przy użyciu przycisku **Testuj połączenie** dla każdego wejścia i wyjścia.

2.  Sprawdź dane wejściowe.

    1. Użyj przycisku [**przykładowe dane**](./stream-analytics-test-query.md) dla każdego elementu wejściowego. Pobierz przykładowe dane wejściowe.
        
    1. Sprawdź przykładowe dane, aby zrozumieć schemat i [typy danych](/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Sprawdź [metryki centrum zdarzeń](../event-hubs/event-hubs-metrics-azure-monitor.md) , aby upewnić się, że zdarzenia są wysyłane. Metryki komunikatów powinny być większe od zera, jeśli Event Hubs otrzymuje komunikaty.

3.  Upewnij się, że wybrano zakres czasu w podglądzie danych wejściowych. Wybierz **pozycję Wybierz zakres czasu**, a następnie wprowadź przykładowy czas trwania przed przetestowaniem zapytania.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Źle sformułowane zdarzenia wejściowe powodują błędy deserializacji 

Problemy deserializacji są spowodowane tym, że strumień wejściowy zadania Stream Analytics zawiera źle sformułowane komunikaty. Na przykład źle sformułowany komunikat może być spowodowany brakującym nawiasem lub nawiasem klamrowym w obiekcie JSON lub w nieprawidłowym formacie sygnatur czasowych w polu Time. 
 
Gdy zadanie Stream Analytics odbierze nieprawidłowo sformułowany komunikat z danych wejściowych, opuszcza komunikat i powiadamia użytkownika o ostrzeżeniu. Na kafelku **dane wejściowe** zadania Stream Analytics zostanie wyświetlony symbol ostrzegawczy. Następujący symbol ostrzegawczy istnieje, o ile zadanie jest w stanie uruchomienia:

![Kafelek Azure Stream Analytics wejść](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Włącz dzienniki zasobów, aby wyświetlić szczegóły błędu i komunikat (ładunek), który spowodował błąd. Istnieje wiele powodów, dla których mogą wystąpić błędy deserializacji. Aby uzyskać więcej informacji na temat określonych błędów deserializacji, zobacz [błędy danych wejściowych](data-errors.md#input-data-errors). Jeśli dzienniki zasobów nie są włączone, w Azure Portal zostanie udostępniona skrócone powiadomienie.

![Powiadomienie ostrzegawcze szczegóły wejściowe](media/stream-analytics-malformed-events/warning-message-with-offset.png)

W przypadkach, gdy ładunek wiadomości jest większy niż 32 KB lub jest w formacie binarnym, uruchom kod CheckMalformedEvents. cs dostępny w [repozytorium przykładów GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji, przesunięcie i drukuje dane, które znajdują się w tym przesunięciu. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Zadanie przekracza maksymalną liczbę odbiorników centrum zdarzeń

Najlepszym rozwiązaniem w przypadku korzystania z Event Hubs jest użycie wielu grup konsumenckich na potrzeby skalowalności zadań. Liczba czytników w Stream Analytics zadania dla określonych danych wejściowych ma wpływ na liczbę czytników w pojedynczej grupie odbiorców. Dokładna liczba odbiorników opiera się na wewnętrznych szczegółach implementacji logiki topologii skalowania i nie jest ujawniana zewnętrznie. Liczba czytników, które mogą ulec zmianie, gdy zadanie zostanie rozpoczęte lub podczas uaktualniania zadań.

Następujące komunikaty o błędach są wyświetlane, gdy liczba odbiorników przekracza wartość maksymalną. Komunikat o błędzie zawiera listę istniejących połączeń z centrum zdarzeń w ramach grupy odbiorców. Tag `AzureStreamAnalytics` wskazuje, że połączenia pochodzą z usługi przesyłania strumieniowego Azure.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Gdy liczba czytelników ulegnie zmianie podczas uaktualniania zadania, w dziennikach inspekcji są zapisywane ostrzeżenia przejściowe. Stream Analytics zadania są automatycznie odzyskiwane po tych problemach przejściowych.

### <a name="add-a-consumer-group-in-event-hubs"></a>Dodawanie grupy odbiorców w Event Hubs

Aby dodać nową grupę odbiorców w wystąpieniu Event Hubs, wykonaj następujące kroki:

1. Zaloguj się w witrynie Azure Portal.

2. Znajdź centrum zdarzeń.

3. Wybierz **Event Hubs** w obszarze nagłówka **jednostki** .

4. Wybierz pozycję centrum zdarzeń według nazwy.

5. Na stronie **wystąpienie Event Hubs** w obszarze nagłówka **jednostki** wybierz pozycję **grupy odbiorców**. Zostanie wyświetlona Grupa odbiorców o nazwie **$default** .

6. Wybierz pozycję **+ Grupa odbiorców** , aby dodać nową grupę odbiorców. 

   ![Dodawanie grupy odbiorców w Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Po utworzeniu danych wejściowych w zadaniu Stream Analytics, aby wskazać centrum zdarzeń, w tym miejscu należy określić grupę odbiorców. **$Default** jest używany, gdy nie określono żadnej opcji. Po utworzeniu nowej grupy odbiorców należy edytować dane wejściowe centrum zdarzeń w Stream Analytics zadania i określić nazwę nowej grupy odbiorców.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Czytelnicy na partycję przekraczają limit Event Hubs

Jeśli Składnia zapytania przesyłania strumieniowego odwołuje się do tego samego zasobu danych wejściowych centrum zdarzeń wielokrotnie, aparat zadania może użyć wielu czytników na zapytanie z tej samej grupy odbiorców. Jeśli istnieje zbyt wiele odwołań do tej samej grupy odbiorców, zadanie może przekroczyć limit pięciu i zgłosiło błąd. W takich sytuacjach można dodatkowo podzielić się przy użyciu wielu danych wejściowych w wielu grupach odbiorców przy użyciu rozwiązania opisanego w poniższej sekcji. 

Scenariusze, w których liczba czytników na partycję przekracza limit Event Hubs równy pięć:

* Wielokrotne instrukcje SELECT: w przypadku używania wielu instrukcji SELECT odwołujących się do **tego samego** danych wejściowych centrum zdarzeń każda instrukcja SELECT powoduje utworzenie nowego odbiornika.

* Unia: w przypadku korzystania z Unii możliwe jest posiadanie wielu danych wejściowych odwołujących się do tego **samego** centrum zdarzeń i grupy konsumentów.

* Samosprzężenie: w przypadku korzystania z operacji samosprzężenia można odwoływać się do tego **samego** centrum zdarzeń wiele razy.

Poniższe najlepsze rozwiązania mogą pomóc w ograniczeniu scenariuszy, w których liczba czytników na partycję przekracza limit Event Hubs równy pięć.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dzielenie zapytania na wiele kroków przy użyciu klauzuli WITH

Klauzula WITH określa tymczasowy, nazwany zestaw wyników, do którego może odwoływać się klauzula FROM w zapytaniu. Należy zdefiniować klauzulę WITH w zakresie wykonywania pojedynczej instrukcji SELECT.

Na przykład zamiast tego zapytania:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Użyj tego zapytania:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Upewnij się, że dane wejściowe są powiązane z różnymi grupami odbiorców

W przypadku zapytań, w których trzy lub więcej danych wejściowych są połączone z tą samą Event Hubs grupą konsumentów, Utwórz osobne grupy odbiorców. Wymaga to utworzenia dodatkowych Stream Analytics danych wejściowych.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Tworzenie oddzielnych danych wejściowych z różnymi grupami odbiorców

Można utworzyć osobne dane wejściowe z różnymi grupami odbiorców dla tego samego centrum zdarzeń. Poniższe zapytanie UNION to przykład, w którym *InputOne* i *InputTwo* odnoszą się do tego samego źródła centrum zdarzeń. Każde zapytanie może mieć oddzielne dane wejściowe z różnymi grupami odbiorców. Kwerenda UNION ma tylko jeden przykład.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Czytelnicy na partycję przekraczają limit IoT Hub

Stream Analytics zadania używają wbudowanego [punktu końcowego zgodnego z centrum zdarzeń](../iot-hub/iot-hub-devguide-messages-read-builtin.md) IoT Hub, aby nawiązać połączenie i odczytać zdarzenia z IoT Hub. Jeśli odczyt na partycję przekracza limity IoT Hub, można użyć [rozwiązań dla centrum zdarzeń](#readers-per-partition-exceeds-event-hubs-limit) , aby rozwiązać ten problem. Grupę odbiorców można utworzyć dla wbudowanego punktu końcowego za pomocą sesji punktu końcowego portalu IoT Hub Portal lub [zestawu IoT Hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Uzyskaj pomoc

Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)