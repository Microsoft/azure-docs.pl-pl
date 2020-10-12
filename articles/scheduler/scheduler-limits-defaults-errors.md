---
title: Limity, przydziały i progi w usłudze Azure Scheduler
description: Dowiedz się więcej na temat limitów, przydziałów, wartości domyślnych i progów ograniczania dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78898523"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limity, przydziały i progi ograniczania w usłudze Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe. 
>
> Usługa Scheduler nie jest już dostępna w Azure Portal, ale polecenia cmdlet programu PowerShell dla [interfejsu API REST](/rest/api/scheduler) i [usługi Azure Scheduler](scheduler-powershell-reference.md) pozostają dostępne w tym momencie, aby można było zarządzać zadaniami i kolekcjami zadań.

## <a name="limits-quotas-and-thresholds"></a>Limity, przydziały i progi

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Nagłówek x-MS-Request-ID

Każde żądanie wykonywane względem usługi Scheduler zwraca nagłówek odpowiedzi o nazwie **x-MS-Request-ID**. Ten nagłówek zawiera nieprzezroczystą wartość, która jednoznacznie identyfikuje żądanie. Dlatego jeśli żądanie nie powiedzie się, a żądanie jest prawidłowo sformatowane, możesz zgłosić błąd do firmy Microsoft, podając wartość nagłówka odpowiedzi **x-MS-Request-ID** i uwzględniając następujące szczegóły: 

* Wartość **x-MS-Request-ID**
* Przybliżony czas wprowadzenia żądania 
* Identyfikatory dla subskrypcji platformy Azure, kolekcji zadań i zadania 
* Typ operacji, którą próbowano wykonać żądanie

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)