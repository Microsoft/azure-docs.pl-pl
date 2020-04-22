---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737013"
---
| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1-3 |50 |
| Dedykowane rdzenie na konto usługi Batch | 90-900 | Kontakt z pomocą techniczną |
| Rdzenie o niskim priorytecie na konto usługi Batch | 10-100 | Kontakt z pomocą techniczną |
| **[Aktywne](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** zadania i harmonogramy zadań na konto usługi Batch **(ukończone** zadania nie mają limitu) | 100-300 | 1 000<sup>1</sup> |
| Pule na konto usługi Batch | 20-100 | 500<sup>1</sup> |

<sup>1.</sup> Aby zażądać zwiększenia poza tym limitem, skontaktuj się z pomocą techniczną platformy Azure.

> [!NOTE]
> Domyślne limity różnią się w zależności od typu subskrypcji używanej do tworzenia konta usługi Batch. Wyświetlane przydziały rdzeni są wyświetlane dla kont usługi Batch w trybie usługi wsadowej. [Służy do wyświetlania przydziałów na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Aby pomóc nam lepiej zarządzać zdolnościami podczas globalnej pandemii kondycji, domyślne przydziały podstawowe dla nowych kont usługi Batch w niektórych regionach i dla niektórych typów subskrypcji zostały zmniejszone z powyższego zakresu wartości, w niektórych przypadkach do zera rdzeni. Podczas tworzenia nowego konta usługi Batch [sprawdź podstawowy przydział](../articles/batch/batch-quota-limit.md#view-batch-quotas) i [zażądaj zwiększenia przydziału podstawowego,](../articles/batch/batch-quota-limit.md#increase-a-quota)jeśli jest to wymagane. 
