---
title: Kody błędów dostępności zewnętrznej — Azure Stream Analytics
description: Rozwiązywanie problemów Azure Stream Analytics z zewnętrznymi kodami błędów dostępności.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045284"
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
