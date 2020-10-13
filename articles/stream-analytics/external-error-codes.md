---
title: Kody błędów zewnętrznych — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z zewnętrznymi kodami błędów.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f963f4c3538146b7f9929960092a18294659be5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045250"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics zewnętrzne kody błędów

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. Ten artykuł zawiera opis każdego zewnętrznego kodu błędu. Błędy zewnętrzne to błędy ogólne zgłoszone przez usługę nadrzędną lub przechodzącą, która Stream Analytics nie może rozróżnić jako błąd danych, błąd konfiguracji lub zewnętrzny błąd dostępności.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Przyczyna**: Wystąpił błąd podczas inicjowania adaptera.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Przyczyna**: Wystąpił błąd podczas zapisywania danych na karcie.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Przyczyna**: błąd http został zwrócony z usługi Azure Functions.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Przyczyna**Stream Analytics: Wystąpił błąd podczas zapisywania zdarzeń w usłudze Azure Function.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Przyczyna**: Wystąpił błąd przekierowania podczas wprowadzania do Azure Functions.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Przyczyna**: Wystąpił błąd klienta podczas wprowadzania do Azure Functions.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Przyczyna**: Wystąpił błąd serwera do Azure Functions.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Przyczyna**: zapisywanie w usłudze Azure Functions nie powiodło się, ponieważ żądanie HTTP przekroczyło limit czasu. 
* **Zalecenie**: sprawdź dzienniki Azure Functions, aby uzyskać potencjalne opóźnienia.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Przyczyna**: przesunięcia danych wejściowych są nieprawidłowe. Może to być spowodowane przełączeniem w tryb failover.
* **Zalecenie**: Uruchom ponownie zadanie Stream Analytics od ostatniego czasu wyjściowego.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Przyczyna**: Wystąpił błąd podczas wysyłania danych do centrum zdarzeń.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Przyczyna**: nie można nawiązać połączenia z kontem Cosmos DB po osiągnięciu maksymalnej liczby ponownych prób Stream Analytics.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Przyczyna**: Stream Analytics nie mógł wykonać zapytania dotyczącego Cosmos DB bazy danych i kolekcji po osiągnięciu maksymalnej liczby ponownych prób.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Przyczyna**: CosmosDB nie może utworzyć procedury składowanej po kilku ponownych próbach.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Przyczyna**: procedura składowana upsert zwróciła błąd. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Przyczyna**: Stream Analytics nie może zainicjować SQL Database danych wyjściowych.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Przyczyna**: Stream Analytics nie może zapisywać zdarzeń do SQL Database danych wyjściowych.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Przyczyna**: Wystąpił błąd podczas inicjowania danych wyjściowych puli SQL Synapse.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Przyczyna**: Wystąpił błąd podczas zapisywania danych wyjściowych w puli SQL Synapse.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)
