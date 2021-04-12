---
title: Rozwiązywanie problemów z Azure Stream Analytics kodami błędów
description: Rozwiązywanie problemów z Azure Stream Analyticsmi przy użyciu wewnętrznych kodów błędów.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: ddea74997850ff080f6c82d55d1be3cfb851deda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019384"
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