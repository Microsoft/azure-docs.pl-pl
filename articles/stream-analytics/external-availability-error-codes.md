---
title: Kody błędów dostępności zewnętrznej — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z zewnętrznymi kodami błędów dostępności.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 1ce867d60c53b9befe5b4480693b54d2c9eba018
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650050"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Azure Stream Analytics zewnętrzne kody błędów dostępności

Dzienników aktywności i dzienników zasobów można używać do debugowania nieoczekiwanych zachowań z zadania Azure Stream Analytics. Ten artykuł zawiera opis każdego zewnętrznego kodu błędu dostępności. Zewnętrzne błędy dostępności występują, gdy usługa zależna jest niedostępna.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Przyczyna**: usługa jest tymczasowo niedostępna.
* **Zalecenie**: Stream Analytics nadal podejmie próbę nawiązania połączenia z usługą.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Przyczyna**: Wystąpił błąd Stream Analytics podczas komunikowania się z centrum EventHub. 


## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md)
* [Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Rozwiązywanie problemów z zapytaniami Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)
* [Błędy danych Azure Stream Analytics](data-errors.md)
