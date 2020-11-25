---
title: Błędy danych dziennika zasobów Azure Stream Analytics
description: W tym artykule wyjaśniono różne błędy danych wejściowych i wyjściowych, które mogą wystąpić podczas korzystania z Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 416e6cb29ab2816d53cb837f72233a9fe098f659
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011425"
---
# <a name="azure-stream-analytics-data-errors"></a>Błędy danych Azure Stream Analytics

Błędy danych to błędy występujące podczas przetwarzania danych.  Te błędy najczęściej występują podczas operacji cofania serializacji, serializacji i zapisu danych.  W przypadku wystąpienia błędów danych Stream Analytics zapisuje szczegółowe informacje i przykładowe zdarzenia do dzienników zasobów. Aby uzyskać dodatkowe informacje, Włącz dzienniki diagnostyczne w zadaniu. W niektórych przypadkach podsumowanie tych informacji jest również udostępniane za pomocą powiadomień portalu.

W tym artykule opisano różne typy błędów, przyczyny i szczegóły dotyczące błędów danych wejściowych i wyjściowych.

## <a name="resource-logs-schema"></a>Schemat dzienników zasobów

Zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) , aby wyświetlić schemat dzienników zasobów. Poniższy kod JSON jest przykładową wartością dla pola **Właściwości** dziennika zasobów dla błędu danych.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Błędy danych wejściowych

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Przyczyna: wybrany typ kompresji danych wejściowych nie jest zgodny z danymi.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: komunikaty z błędami deserializacji, w tym nieprawidłowy typ kompresji, są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. W przypadku centrum zdarzeń identyfikator ma wartość PartitionId, przesunięcie i numer sekwencyjny.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Przyczyna: nagłówek danych wejściowych jest nieprawidłowy. Na przykład wolumin CSV ma kolumny o zduplikowanych nazwach.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: komunikaty z błędami deserializacji, w tym nieprawidłowy nagłówek, są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Przyczyna: kolumny wejściowe zdefiniowane za pomocą CREATE TABLE lub przez SYGNATURę CZASową nie istnieją.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: zdarzenia z brakującymi kolumnami są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. 
   * Nazwy nieobecnych kolumn. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikaty o błędach**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Przyczyna: nie można skonwertować danych wejściowych na typ określony w instrukcji CREATE TABLE.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: zdarzenia z błędem konwersji typu są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. 
   * Nazwa kolumny i oczekiwanego typu.

**Komunikaty o błędach**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Przyczyna: dane wejściowe nie mają właściwego formatu. Na przykład dane wejściowe nie są prawidłowym kodem JSON.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: Napotkano wszystkie zdarzenia w komunikacie po wystąpieniu nieprawidłowego błędu danych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikaty o błędach**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Przyczyna: wartość wyrażenia TIMESTAMP BY nie może zostać przekonwertowana na typ DateTime.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: zdarzenia z nieprawidłowym wejściowym znacznikiem czasu są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator wiadomości wejściowej. 
   * Komunikat o błędzie. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Przyczyna: wartość SYGNATURy CZASowej przez ponad timestampColumn ma wartość NULL.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: zdarzenia z nieprawidłowym wejściowym kluczem znacznika czasu są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Przyczyna: różnica między czasem aplikacji a czasem przybycia jest większa niż opóźnione okno tolerancji przybycia.
* Podane powiadomienie portalu: nie
* Poziom dziennika zasobów: informacje
* Wpływ: opóźnione zdarzenia wejściowe są obsługiwane zgodnie z ustawieniem "Obsługuj inne zdarzenia" w sekcji porządkowanie zdarzeń w konfiguracji zadania. Aby uzyskać więcej informacji, zobacz [zasady czasu obsługi](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Szczegóły dziennika
   * Czas aplikacji i czas przybycia. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Przyczyna: różnica między czasem aplikacji a czasem przybycia jest większa niż 5 minut.
* Podane powiadomienie portalu: nie
* Poziom dziennika zasobów: informacje
* Wpływ: wczesne zdarzenia wejściowe są obsługiwane zgodnie z ustawieniem "Obsługuj inne zdarzenia" w sekcji porządkowanie zdarzeń w konfiguracji zadania. Aby uzyskać więcej informacji, zobacz [zasady czasu obsługi](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Szczegóły dziennika
   * Czas aplikacji i czas przybycia. 
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Przyczyna: zdarzenie jest uznawane za nieuporządkowane zgodnie ze zdefiniowanym oknem tolerancji poza kolejnością.
* Podane powiadomienie portalu: nie
* Poziom dziennika zasobów: informacje
* Wpływ: zdarzenia poza kolejnością są obsługiwane zgodnie z ustawieniem "Obsługuj inne zdarzenia" w sekcji porządkowanie zdarzeń w konfiguracji zadania. Aby uzyskać więcej informacji, zobacz [zasady czasu obsługi](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Szczegóły dziennika
   * Rzeczywisty ładunek do kilku kilobajtów.

**Komunikat o błędzie**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Błędy danych wyjściowych

Azure Stream Analytics może identyfikować błędy danych wyjściowych z lub bez żądania we/wy do ujścia danych wyjściowych w zależności od konfiguracji. Na przykład brakuje wymaganej kolumny, na przykład  `PartitionKey` podczas korzystania z danych wyjściowych w tabeli platformy Azure bez żądania we/wy. Jednak naruszenia ograniczeń w danych wyjściowych SQL wymagają żądania we/wy.

Istnieje kilka błędów danych, które mogą być wykrywane tylko po wywołaniu ujścia danych wyjściowych, co może spowolnić przetwarzanie. Aby rozwiązać ten problem, Zmień konfigurację zadania lub zapytanie, które powoduje błąd danych.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Przyczyna: kolumna wymagana dla danych wyjściowych nie istnieje. Przykładowo istnieje kolumna zdefiniowana jako Azure Table PartitionKey does't.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: wszystkie błędy konwersji danych wyjściowych, w tym brakująca wymagana kolumna, są obsługiwane zgodnie z ustawieniem [zasad danych wyjściowych](./stream-analytics-output-error-policy.md) .
* Szczegóły dziennika
   * Nazwa kolumny oraz identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Przyczyna: wartość kolumny nie jest zgodna z danymi wyjściowymi. Na przykład nazwa kolumny nie jest prawidłową kolumną tabeli platformy Azure.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: wszystkie błędy konwersji danych wyjściowych, w tym Nieprawidłowa nazwa kolumny, są obsługiwane zgodnie z ustawieniem [zasad danych wyjściowych](./stream-analytics-output-error-policy.md) .
* Szczegóły dziennika
   * Nazwa kolumny i identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Przyczyna: nie można przekonwertować kolumny na prawidłowy typ w danych wyjściowych. Na przykład wartość kolumny jest niezgodna z ograniczeniami lub typem zdefiniowanym w tabeli SQL.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: wszystkie błędy konwersji danych wyjściowych, w tym błąd konwersji typu, są obsługiwane zgodnie z ustawieniem [zasad danych wyjściowych](./stream-analytics-output-error-policy.md) .
* Szczegóły dziennika
   * Nazwa kolumny.
   * Identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Przyczyna: wartość komunikatu jest większa niż obsługiwany rozmiar wyjściowy. Na przykład rekord jest większy niż 1 MB dla danych wyjściowych centrum zdarzeń.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: wszystkie błędy konwersji danych wyjściowych, w tym limit rozmiaru dla rekordu, są obsługiwane zgodnie z ustawieniem [zasad danych wyjściowych](./stream-analytics-output-error-policy.md) .
* Szczegóły dziennika
   * Identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Przyczyna: rekord zawiera już kolumnę o tej samej nazwie co kolumna systemowa. Na przykład dane wyjściowe CosmosDB z kolumną o nazwie ID, gdy kolumna ID ma inną kolumnę.
* Podane powiadomienie portalu: tak
* Poziom dziennika zasobów: Ostrzeżenie
* Wpływ: wszystkie błędy konwersji danych wyjściowych, w tym zduplikowany klucz, są obsługiwane zgodnie z ustawieniem [zasad danych wyjściowych](./stream-analytics-output-error-policy.md) .
* Szczegóły dziennika
   * Nazwa kolumny.
   * Identyfikator rekordu lub część rekordu.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md)

* [Informacje o monitorowaniu zadań Stream Analytics i sposobach monitorowania zapytań](stream-analytics-monitoring.md)