---
title: Kody błędów danych — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z kodami błędów danych.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016494"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Kody błędów danych Azure Stream Analytics

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. W tym artykule przedstawiono opis każdego kodu błędu błędu. Błędy danych występują, gdy w strumieniu znajdują się złe dane, takie jak schemat nieoczekiwanego rekordu.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Przyczyna**: Wystąpił błąd podczas deserializacji danych wejściowych.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Przyczyna**: Stream Analytics nie może pobrać sygnatury czasowej dla zasobu. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Przyczyna**: Stream Analytics nie może pobrać wartości `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Przyczyna**: Zdarzenie wejściowe zostało wysłane później niż skonfigurowana tolerancja.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Przyczyna**: czas przybycia zdarzenia wejściowego jest wcześniejszy niż próg sygnatury czasowej aplikacji zdarzenia wejściowego.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Przyczyna**: Wartość wyjściowa komunikatu do Azure Functions przekracza limit rozmiaru.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Przyczyna**: rekord wyjściowy przekracza maksymalny limit rozmiaru podczas zapisywania do centrum zdarzeń.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Przyczyna**: wartość lub typ określonej kolumny jest nieprawidłowy.
* **Zalecenie**: Podaj unikatowe niepuste ciągi, które nie są dłuższe niż 255 znaków.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Przyczyna**: Identyfikator dokumentu rekordu wyjściowego zawiera nieprawidłowy znak.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Przyczyna**: rekord wyjściowy nie zawiera \[ identyfikatora kolumny], aby można go było użyć jako właściwości klucza podstawowego.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Przyczyna**: rekord wyjściowy nie zawiera właściwości identyfikatora dokumentu. 
* **Zalecenie**: Upewnij się, że dane wyjściowe zapytania zawierają kolumnę z unikatowym niepustym ciągiem krótszym niż znak "255".

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Przyczyna**: w rekordzie wyjściowym brakuje kolumny, która ma być używana jako właściwość klucza partycji.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Przyczyna**: zapis pojedynczego rekordu w Cosmos DB jest zbyt duży.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Przyczyna**: Stream Analytics nie może zapisywać zdarzeń do SQL Database ze względu na problemy z danymi.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)
