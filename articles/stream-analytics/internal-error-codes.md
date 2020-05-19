---
title: Rozwiązywanie problemów z Azure Stream Analytics kodami błędów
description: Rozwiązywanie problemów z Azure Stream Analyticsmi przy użyciu wewnętrznych kodów błędów.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597737"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics wewnętrzne kody błędów

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. Ten artykuł zawiera opis każdego wewnętrznego kodu błędu. Błędy wewnętrzne to błędy ogólne, które są zgłaszane w ramach platformy Stream Analytics, gdy Stream Analytics nie można rozróżnić, jeśli błąd jest wewnętrznym błędem dostępności lub usterką w systemie.

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **Przyczyna**: żądanie wysłane do serwera Kafka jest nieprawidłowe.

## <a name="kafkainputerror"></a>KafkaInputError

* **Przyczyna**: dane wejściowe Kafka napotkały problem.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Przyczyna**: rozmiar wsadu używany do zapisu w Cosmos DB jest zbyt duży. 
* **Zalecenie**: spróbuj ponownie, używając mniejszego rozmiaru partii.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)