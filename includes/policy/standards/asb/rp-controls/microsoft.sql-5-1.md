---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cc2cd959043a91cb90cf982fb983bc4f61ba69bf
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021578"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeprowadź inspekcję każdego wystąpienia zarządzanego SQL, które nie ma włączonych skanów oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Inspekcja serwerów SQL platformy Azure, które nie mają włączonej cyklicznej oceny ocen luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
