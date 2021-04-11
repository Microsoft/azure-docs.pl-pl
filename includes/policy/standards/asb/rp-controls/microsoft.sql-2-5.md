---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 289a2fe7fecc3e28d646e98692c1d5ada4ea7fa8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097458"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Serwery SQL z inspekcją w miejscu docelowym konta magazynu powinny być skonfigurowane z zachowaniem 90 dni lub nowszym](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |W celu zbadania zdarzenia zalecamy ustawienie przechowywania danych dla inspekcji SQL Server na konto magazynu na co najmniej 90 dni. Upewnij się, że spełniasz niezbędne reguły przechowywania dla regionów, w których pracujesz. Jest to czasami wymagane do zgodności ze standardami prawnymi. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
