---
title: Azure Stream Analytics poziomów zgodności
description: Dowiedz się, jak ustawić poziom zgodności dla zadania Azure Stream Analytics i istotne zmiany na najnowszym poziomie zgodności
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 1b49cdb423e8fd2e70175d4ac71f6dcd07b459f9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097740"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Poziom zgodności dla zadań Azure Stream Analytics

W tym artykule opisano opcję poziomu zgodności w Azure Stream Analytics. Stream Analytics to usługa zarządzana z regularnymi aktualizacjami funkcji i ulepszeniami wydajności. Większość aktualizacji środowiska uruchomieniowego usługi jest automatycznie udostępnianych użytkownikom końcowym. 

Jednak niektóre nowe funkcje w usłudze mogą wprowadzić istotną zmianę, taką jak zmiana zachowania istniejącego zadania lub zmiana sposobu korzystania z danych w uruchomionych zadaniach. Istniejące zadania Stream Analytics można zachować bez poważniejszych zmian, pozostawiając obniżone ustawienie poziomu zgodności. Gdy wszystko będzie gotowe do najnowszych zachowań środowiska uruchomieniowego, możesz zdecydować się na zwiększenie poziomu zgodności. 

## <a name="choose-a-compatibility-level"></a>Wybierz poziom zgodności

Poziom zgodności kontroluje zachowanie zadania usługi Stream Analytics w czasie wykonywania. 

Azure Stream Analytics obecnie obsługuje trzy poziomy zgodności:

* 1,0 — oryginalny poziom zgodności wprowadzony podczas ogólnej dostępności Azure Stream Analytics kilku lat temu.
* 1,1 — poprzednie zachowanie
* 1,2 — najnowsze zachowanie z najnowszymi ulepszeniami

Gdy tworzysz nowe zadanie Stream Analytics, najlepszym rozwiązaniem jest utworzenie go przy użyciu najnowszego poziomu zgodności. Rozpocznij projektowanie zadań, opierając się na najnowszych zachowaniach, aby uniknąć późniejszej zmiany i złożoności.

## <a name="set-the-compatibility-level"></a>Ustawianie poziomu zgodności

Możesz ustawić poziom zgodności dla zadania Stream Analytics w Azure Portal lub przy użyciu [wywołania interfejsu API Rest tworzenia zadań](/azure/stream-analytics/stream-analytics-quick-create-portal).

Aby zaktualizować poziom zgodności zadania w Azure Portal:

1. Użyj [Azure Portal](https://portal.azure.com) , aby zlokalizować Stream Analytics zadanie.
2. **Zatrzymaj** zadanie przed aktualizacją poziomu zgodności. Nie można zaktualizować poziomu zgodności, jeśli zadanie jest w stanie uruchomienia.
3. W obszarze **Konfiguruj** nagłówek wybierz pozycję **poziom zgodności** .
4. Wybierz żądaną wartość poziomu zgodności.
5. Wybierz pozycję **Zapisz** w dolnej części strony.

![Stream Analytics poziom zgodności w Azure Portal](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Podczas aktualizowania poziomu zgodności, kompilator języka T sprawdza poprawność zadania z składnią odpowiadającą wybranemu poziomowi zgodności.

## <a name="compatibility-level-12"></a>Poziom zgodności 1,2

Następujące istotne zmiany są wprowadzane w obszarze poziom zgodności 1,2:

###  <a name="amqp-messaging-protocol"></a>AMQP Messaging Protocol

**poziom 1,2** : Azure Stream Analytics używa protokołu obsługi komunikatów [protokołu Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) do zapisu w Service Bus kolejkach i tematach. AMQP umożliwia tworzenie aplikacji hybrydowych dla wielu platform przy użyciu protokołu Open Standard.

### <a name="geospatial-functions"></a>Funkcje geoprzestrzenne

**Poprzednie poziomy:** Azure Stream Analytics używać obliczeń geograficznych.

**poziom 1,2:** Azure Stream Analytics umożliwia obliczanie geometrycznych współrzędnych geograficznych. Nie wprowadzono zmian w sygnaturze funkcji geoprzestrzennych. Jednak ich semantyka jest nieco inna, co pozwala na dokładniejsze Obliczanie niż wcześniej.

Azure Stream Analytics obsługuje indeksowanie danych referencyjnych geograficznych. Dane referencyjne zawierające elementy geograficzne mogą być indeksowane w celu przyspieszenia obliczeń sprzężeń.

Zaktualizowane funkcje geoprzestrzenne przyjmują pełny wyrazistości z formatem geograficznym dobrze znanego tekstu (WKT). Można określić inne składniki geograficzne, które nie były wcześniej obsługiwane w GeoJSON.

Aby uzyskać więcej informacji, zobacz [Aktualizacje funkcji geoprzestrzennych w Azure Stream Analytics — chmura i IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Równoległe wykonywanie zapytań dla źródeł danych wejściowych z wieloma partycjami

**Poprzednie poziomy:** Zapytania Azure Stream Analytics wymagały użycia klauzuli PARTITION BY w celu zrównoleglanie przetwarzania zapytań między wejściowymi partycjami źródłowymi.

**poziom 1,2:** Jeśli logika zapytań może być równoległa między wejściowymi partycjami źródłowymi, Azure Stream Analytics tworzy oddzielne wystąpienia zapytań i uruchamia obliczenia równolegle.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natywna integracja zbiorczej usługi API z danymi wyjściowymi CosmosDB

**Poprzednie poziomy:** Zachowanie upsert zostało *wstawione lub scalone* .

**poziom 1,2:** Natywna integracja zbiorczej interfejsu API z danymi wyjściowymi CosmosDB maksymalizuje przepływność i wydajnie obsługuje żądania ograniczania. Aby uzyskać więcej informacji, zobacz [stronę Azure Stream Analytics dane wyjściowe do Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Zachowanie upsert jest *wstawiane lub zastępowane* .

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset podczas zapisywania w danych wyjściowych SQL

**Poprzednie poziomy:** typy [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) zostały dostosowane do czasu UTC.

**poziom 1,2:** DateTimeOffset nie jest już dostosowywany.

### <a name="long-when-writing-to-sql-output"></a>Długi podczas zapisu w danych wyjściowych SQL

**Poprzednie poziomy:** Wartości zostały obcięte na podstawie typu docelowego.

**poziom 1,2:** Wartości, które nie mieszczą się w typie docelowym, są obsługiwane zgodnie z zasadami błędu wyjściowego.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Nagrywanie i Serializacja tablic podczas zapisywania do danych wyjściowych SQL

**Poprzednie poziomy:** Rekordy zostały zapisane w postaci "Record", a tablice zostały zapisane jako "Array".

**poziom 1,2:** Rekordy i tablice są serializowane w formacie JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Ścisła weryfikacja prefiksu funkcji

**Poprzednie poziomy:** Nie istnieje ścisła weryfikacja prefiksów funkcji.

**poziom 1,2:** Azure Stream Analytics ma ścisłą weryfikację prefiksów funkcji. Dodanie prefiksu do wbudowanej funkcji powoduje wystąpienie błędu. Na przykład `myprefix.ABS(…)` nie jest obsługiwana.

Dodanie prefiksu do wbudowanych agregacji powoduje także błąd. Na przykład `myprefix.SUM(…)` nie jest obsługiwana.

Użycie prefiksu "system" dla każdej funkcji zdefiniowanej przez użytkownika powoduje błąd.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Nie Zezwalaj na tablicę i obiekt jako właściwości klucza w adapterze danych wyjściowych Cosmos DB

**Poprzednie poziomy:** Typy tablic i obiektów były obsługiwane jako właściwości klucza.

**poziom 1,2:** Typy tablic i obiektów nie są już obsługiwane jako właściwości klucza.

## <a name="compatibility-level-11"></a>Poziom zgodności 1,1

Następujące istotne zmiany są wprowadzane w obszarze poziom zgodności 1,1:

### <a name="service-bus-xml-format"></a>Service Bus format XML

**poziom 1,0:** Azure Stream Analytics użyciu DataContractSerializer, więc zawartość komunikatów zawiera tagi XML. Przykład:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**poziom 1,1:** Zawartość wiadomości zawiera strumień bezpośrednio bez dodatkowych znaczników. Na przykład: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Utrwalanie wielkości liter w nazwach pól

**poziom 1,0:** Nazwy pól zostały zmienione na małe litery w przypadku przetworzenia przez aparat Azure Stream Analytics.

**poziom 1,1:** uwzględnianie wielkości liter jest zachowywane dla nazw pól podczas przetwarzania przez aparat Azure Stream Analytics.

> [!NOTE]
> Rozróżnianie wielkości liter nie jest jeszcze dostępne dla zadań usługi Stream Analytics hostowanych za pomocą środowiska brzegowego. W związku z tym wszystkie nazwy pól są konwertowane na małe litery, jeśli zadanie jest hostowane na krawędzi.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**poziom 1,0:** Polecenie CREATE TABLE nie odfiltruje zdarzeń z NaN (nie liczbą). Na przykład nieskończoność, nieskończoność w typie kolumny ZMIENNOPRZECINKOWej, ponieważ znajdują się poza udokumentowanym zakresem dla tych liczb.

**poziom 1,1:** CREATE TABLE pozwala określić mocny schemat. Aparat Stream Analytics sprawdza, czy dane są zgodne z tym schematem. Przy użyciu tego modelu polecenie umożliwia filtrowanie zdarzeń przy użyciu wartości NaN.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Wyłącz automatyczną konwersję ciągów DateTime na typ DateTime w danych wejściowych JSON

**poziom 1,0:** Analizator JSON automatycznie przekonwertuje wartości ciągu na dane typu Data/Godzina/Strefa na typ DATETIME w ruchu przychodzącym, więc wartość spowoduje natychmiastowe utratę pierwotnego formatowania i informacji o strefie czasowej. Ponieważ jest to wykonywane w przypadku ruchu przychodzącego, nawet jeśli to pole nie było używane w zapytaniu, jest konwertowane na datę i godzinę UTC.

**poziom 1,1:** Nie ma automatycznej konwersji wartości ciągu z informacjami o dacie/godzinie/strefie na typ DATETIME. W rezultacie są przechowywane informacje o strefie czasowej i oryginalne formatowanie. Jeśli jednak pole NVARCHAR (MAX) jest używane w zapytaniu jako część wyrażenia DATETIME (na przykład funkcja DATEADD), jest konwertowane na typ DATETIME w celu wykonania obliczeń i utraci oryginalny formularz.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów Azure Stream Analytics danych wejściowych](stream-analytics-troubleshoot-input.md)
* [Stream Analytics kondycji zasobów](stream-analytics-resource-health.md)
