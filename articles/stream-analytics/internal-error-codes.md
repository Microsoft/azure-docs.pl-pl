---
title: Rozwiązywanie problemów z Azure Stream Analytics kodami błędów
description: Rozwiązywanie problemów z Azure Stream Analyticsmi przy użyciu wewnętrznych kodów błędów.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9dc3d873ddfef9a729f583cd914ca4700d562ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045233"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics wewnętrzne kody błędów

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. Ten artykuł zawiera opis każdego wewnętrznego kodu błędu. Błędy wewnętrzne to błędy ogólne, które są zgłaszane w ramach platformy Stream Analytics, gdy Stream Analytics nie można rozróżnić, jeśli błąd jest wewnętrznym błędem dostępności lub usterką w systemie.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Przyczyna**: rozmiar wsadu używany do zapisu w Cosmos DB jest zbyt duży. 
* **Zalecenie**: spróbuj ponownie, używając mniejszego rozmiaru partii.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)