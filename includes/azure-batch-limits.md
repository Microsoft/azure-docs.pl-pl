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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81737013"
---
| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Konta usługi Azure Batch na region na subskrypcję | 1–3 |50 |
| Rdzenie dedykowane na konto usługi Batch | 90–900 | Kontakt z pomocą techniczną |
| Rdzenie o niskim priorytecie na konto usługi Batch | 10–100 | Kontakt z pomocą techniczną |
| **[Aktywne](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** zadania i harmonogramy zadań na konto usługi Batch (**ukończone** zadania nie mają limitu) | 100–300 | 1000<sup>1</sup> |
| Pule na konto usługi Batch | 20–100 | 500<sup>1</sup> |

<sup>1</sup>Aby poprosić o zwiększenie tego limitu, skontaktuj się z działem pomocy technicznej platformy Azure.

> [!NOTE]
> Limity domyślne różnią się w zależności od typu subskrypcji użytej do utworzenia konta usługi Batch. Pokazano limity przydziału rdzeni dla kont usługi Batch w trybie usługi Batch. [Wyświetlanie limitów przydziału na koncie usługi Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Aby pomóc nam lepiej zarządzać potencjałem w czasie globalnej pandemii, domyślne podstawowe limity przydziału rdzeni dla nowych kont usługi Batch w niektórych regionach i dla niektórych rodzajów subskrypcji zostały zmniejszone z powyższego zakresu wartości (w niektórych przypadkach do zera rdzeni). Podczas tworzenia nowego konta w usłudze Batch należy [sprawdzić limit przydziału rdzeni](../articles/batch/batch-quota-limit.md#view-batch-quotas) i [przesłać żądanie zwiększenia limitu przydziału rdzeni](../articles/batch/batch-quota-limit.md#increase-a-quota), jeśli jest to wymagane. 
