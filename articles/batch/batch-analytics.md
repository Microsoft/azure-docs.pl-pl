---
title: Analiza Azure Batch
description: Tematy w usłudze Batch Analytics zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113072"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch
Tematy w usłudze Batch Analytics zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.

Aby uzyskać więcej informacji na temat włączania i używania dzienników diagnostycznych usługi Batch, zobacz [Azure Batch rejestrowanie diagnostyczne](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Azure Batch emituje następujące zdarzenia dziennika diagnostycznego w okresie istnienia niektórych zasobów usługi Batch.

**Zdarzenia dziennika usługi**
* [Tworzenie puli](batch-pool-create-event.md)
* [Początek usuwania puli](batch-pool-delete-start-event.md)
* [Ukończono Usuwanie puli](batch-pool-delete-complete-event.md)
* [Początek zmiany rozmiaru puli](batch-pool-resize-start-event.md)
* [Ukończono Zmienianie rozmiaru puli](batch-pool-resize-complete-event.md)
* [Rozpoczęcie zadania](batch-task-start-event.md)
* [Zadanie zakończone](batch-task-complete-event.md)
* [Niepowodzenie zadania](batch-task-fail-event.md)