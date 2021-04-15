---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 918160c96161f3452588a423cf9a39d53590ec7c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496621"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wdrażanie ustawień diagnostycznych dla Data Lake Storage Gen1 w centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8d096bc-85de-4c5f-8cfb-857bd1b9d62d) |Wdraża ustawienia diagnostyczne dla programu Data Lake Storage Gen1 przesyłać strumieniowo do regionalnego centrum zdarzeń, gdy zostanie utworzona lub zaktualizowana dowolna Data Lake Storage Gen1, w której brakuje tych ustawień diagnostycznych. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Wdrażanie ustawień diagnostycznych dla Data Lake Storage Gen1 w obszarze roboczym usługi Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25763a0a-5783-4f14-969e-79d4933eb74b) |Wdraża ustawienia diagnostyczne dla programu Data Lake Storage Gen1 do regionalnego obszaru roboczego usługi Log Analytics, gdy zostanie utworzona lub zaktualizowana dowolna Data Lake Storage Gen1, w której brakuje tych ustawień diagnostycznych. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Wymagaj szyfrowania na Data Lake Store kont](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Te zasady zapewniają, że szyfrowanie jest włączone na wszystkich Data Lake Store kont |Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Dzienniki zasobów w usłudze Azure Data Lake Store powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
