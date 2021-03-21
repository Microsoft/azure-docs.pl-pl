---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564c07c3a59e13dda5fbbc992855d5ca6019dad6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586888"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Serwery SQL powinny zachować dane inspekcji przez co najmniej 90 dni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |W celu zbadania zdarzeń zalecamy ustawienie przechowywania danych dla danych inspekcji serwerów SQL na co najmniej 90 dni. Upewnij się, że spełniasz niezbędne reguły przechowywania dla regionów, w których pracujesz. Jest to czasami wymagane do zgodności ze standardami prawnymi. |AuditIfNotExists, wyłączone |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
