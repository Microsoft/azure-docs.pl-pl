---
title: Analiza Azure Batch
description: Tematy w usłudze Batch Analytics zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849515"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch

Tematy w tej sekcji zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.

Aby uzyskać więcej informacji na temat włączania i używania dzienników diagnostycznych usługi Batch, zobacz [Azure Batch rejestrowanie diagnostyczne](./batch-diagnostics.md) .

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Azure Batch emituje następujące zdarzenia dziennika diagnostycznego w okresie istnienia niektórych zasobów usługi Batch.

### <a name="service-log-events"></a>Zdarzenia dziennika usługi

- [Tworzenie puli](batch-pool-create-event.md)
- [Początek usuwania puli](batch-pool-delete-start-event.md)
- [Ukończono Usuwanie puli](batch-pool-delete-complete-event.md)
- [Początek zmiany rozmiaru puli](batch-pool-resize-start-event.md)
- [Ukończono Zmienianie rozmiaru puli](batch-pool-resize-complete-event.md)
- [Automatyczne skalowanie puli](batch-pool-autoscale-event.md)
- [Rozpoczęcie zadania](batch-task-start-event.md)
- [Zadanie zakończone](batch-task-complete-event.md)
- [Niepowodzenie zadania](batch-task-fail-event.md)
- [Niepowodzenie harmonogramu zadań](batch-task-schedule-fail-event.md)
