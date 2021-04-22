---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: be7f7a94953007a6dd583c0049d67ac284d537d6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873431"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stream Analytics powinny używać kluczy zarządzanych przez klienta do szyfrowania danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |Kluczy zarządzanych przez klienta należy używać, gdy chcesz bezpiecznie przechowywać wszelkie metadane i prywatne zasoby danych Stream Analytics na koncie magazynu. Daje to całkowitą kontrolę nad tym, jak dane Stream Analytics są szyfrowane. |inspekcja, odmowa, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Wdrażanie ustawień diagnostycznych dla Stream Analytics centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedf3780c-3d70-40fe-b17e-ab72013dafca) |Wdraża ustawienia diagnostyczne dla programu Stream Analytics do przesyłania strumieniowego do regionalnego centrum zdarzeń, gdy zostanie utworzona lub zaktualizowana dowolna Stream Analytics której brakuje tych ustawień diagnostycznych. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Wdrażanie ustawień diagnostycznych dla Stream Analytics w obszarze roboczym usługi Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237e0f7e-b0e8-4ec4-ad46-8c12cb66d673) |Wdraża ustawienia diagnostyczne dla usługi Stream Analytics przesyłania strumieniowego do regionalnego obszaru roboczego usługi Log Analytics, gdy zostanie utworzona lub zaktualizowana dowolna Stream Analytics, w której brakuje tych ustawień diagnostycznych. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Dzienniki zasobów w Azure Stream Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
