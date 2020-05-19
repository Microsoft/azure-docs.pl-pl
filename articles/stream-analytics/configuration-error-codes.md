---
title: Kody błędów konfiguracji — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z kodami błędów konfiguracji.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9951a6ddfbd1c562184e2b7e02a9da98f7d94b12
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598066"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Kody błędów konfiguracji Azure Stream Analytics

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. W tym artykule przedstawiono opis każdego kodu błędu konfiguracji. Błędy konfiguracji są powiązane z konfiguracją zadania lub konfiguracjami wejściowymi i wyjściowymi.

## <a name="kafkainvalidpermission"></a>KafkaInvalidPermission

* **Przyczyna**: zadanie Stream Analytics nie ma odpowiednich uprawnień do wykonania określonych akcji.

## <a name="kafkainvalidconfiguration"></a>KafkaInvalidConfiguration

* **Przyczyna**: Niektóre konfiguracje na karcie Kafka są nieprawidłowe.

## <a name="kafkainvalidtimestamptype"></a>KafkaInvalidTimestampType

* **Przyczyna**: typ znacznika czasu używany przez Kafka jest nieprawidłowy.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Przyczyna**: centrum zdarzeń zgłosiło błąd *nieautoryzowanego dostępu* .

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Przyczyna**: istnieje więcej niż jeden odbiornik centrum zdarzeń z różnymi wartościami epoki.
* **Zalecenie**: Upewnij się, że *Service Bus Explorer* lub aplikacja *klasy eventprocessorhost* nie jest połączona, gdy zadanie Stream Analytics jest uruchomione.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Przyczyna**: Stream Analytics nie może nawiązać połączenia z partycją, ponieważ osiągnięto maksymalną dozwoloną liczbę odbiorników na partycję w grupie odbiorców.
* **Zalecenie**: Upewnij się, że inne zadania Stream Analytics lub Eksplorator Service Bus nie korzystają z tej samej grupy odbiorców.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Przyczyna**: Wystąpił błąd podczas zapisywania danych w centrum zdarzeń z powodu ograniczenia przepustowości.
* **Zalecenie**: Jeśli tak się stanie, Uaktualnij przepływność.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Przyczyna**: podana Konfiguracja połączenia jest niepoprawna.
* **Zalecenie**: Popraw konfigurację i ponownie uruchom zadanie.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Przyczyna**: Host centrum zdarzeń jest nieosiągalny.
* **Zalecenie**: Upewnij się, że podana nazwa hosta jest poprawna.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Przyczyna**: nadawca eventhub napotkał nieoczekiwaną liczbę partycji EventHub.
* **Zalecenie**: Uruchom ponownie zadanie Stream Analytics, jeśli liczba partycji centrum zdarzeń uległa zmianie.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Przyczyna**Stream Analytics: nie można odnaleźć klucza partycji określonej Cosmos DB kolekcji w bazie danych.
* **Zalecenie**: Upewnij się, że dla kolekcji w Cosmos DB jest określony prawidłowy klucz partycji.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Przyczyna**: zgłoszono, gdy klucz partycji nie jest węzłem liścia ani na najwyższego poziomu.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Przyczyna**: dane wyjściowe zapytania nie mogą zawierać \[ identyfikatora kolumny], jeśli jako właściwość klucza podstawowego wybrano inną kolumnę.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Przyczyna**: Stream Analytics nie może znaleźć bazy danych CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Przyczyna**: Stream Analytics nie może znaleźć określonej Cosmos DB kolekcji w bazie danych.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Przyczyna**: Wystąpił błąd podczas zapisywania danych z powodu ograniczenia przez Cosmos DB.
* **Zalecenie**: Uaktualnij warstwę wydajności kolekcji i Dostosuj wydajność bazy danych.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Przyczyna**: zadanie Stream Analytics napotkało błąd uwierzytelniania.
* **Zalecenie**: Upewnij się, że parametry połączenia SQL Database są poprawne.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Przyczyna**: zadanie Stream Analytics napotkało błąd uwierzytelniania. 
* **Zalecenie**: Upewnij się, że nazwa konta jest prawidłowo skonfigurowana, a zarządzana tożsamość zadania ma dostęp do SQL Database.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Przyczyna**: Stream Analytics nie może znaleźć informacji o schemacie dla określonej tabeli.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Przyczyna**: SQL Database nie jest obsługiwana.
* **Zalecenie**: Użyj Synapse puli SQL.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)
