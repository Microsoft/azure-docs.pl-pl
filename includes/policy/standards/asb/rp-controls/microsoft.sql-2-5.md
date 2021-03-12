---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60ddf7a90f478fa0b286f14a1cba4d609b02e321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021374"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Serwery SQL powinny zachować dane inspekcji przez co najmniej 90 dni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |W celu zbadania zdarzeń zalecamy ustawienie przechowywania danych dla danych inspekcji serwerów SQL na co najmniej 90 dni. Upewnij się, że spełniasz niezbędne reguły przechowywania dla regionów, w których pracujesz. Jest to czasami wymagane do zgodności ze standardami prawnymi. |AuditIfNotExists, wyłączone |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
