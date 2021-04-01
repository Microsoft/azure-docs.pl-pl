---
title: Kody błędów dostępności zewnętrznej — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z zewnętrznymi kodami błędów dostępności.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020557"
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
