---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: da4c468ed2e7756919ad11c85f11bb73fbe14161
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807967"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konto Azure Batch powinno używać kluczy zarządzanych przez klienta do szyfrowania danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |Klucze zarządzane przez klienta (CMKs) służą do zarządzania szyfrowaniem w pozostałej części danych konta programu Batch. Domyślnie dane klienta są szyfrowane przy użyciu kluczy zarządzanych przez usługę, ale CMKs są często wymagane do spełnienia standardów zgodności z przepisami. CMKs umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do użytkownika. Użytkownik ma pełną kontrolę i odpowiedzialność za kluczowy cykl życia, włącznie z rotacją i zarządzaniem. Dowiedz się więcej o szyfrowaniu CMK w [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK) . |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Batch/Batch_CustomerManagedKey_Audit.json) |
|[Wdróż ustawienia diagnostyczne dla konta usługi Batch w centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdb51110f-0865-4a6e-b274-e2e07a5b2cd7) |Wdraża ustawienia diagnostyczne dla konta usługi Batch w celu przesyłania strumieniowego do regionalnego centrum zdarzeń w przypadku utworzenia lub zaktualizowania dowolnego konta usługi Batch, w którym brakuje tych ustawień diagnostycznych. |DeployIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Wdróż ustawienia diagnostyczne dla konta usługi Batch z obszarem roboczym Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc84e5349-db6d-4769-805e-e14037dab9b5) |Wdraża ustawienia diagnostyczne dla konta usługi Batch w celu przesyłania strumieniowego do obszaru roboczego Log Analytics regionalnego, gdy zostanie utworzone lub zaktualizowane konto usługi Batch, dla którego brakuje tych ustawień diagnostycznych. |DeployIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Batch_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Należy włączyć dzienniki diagnostyczne na kontach wsadowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Inspekcja konfiguracji reguł alertów metryk na koncie wsadowym, aby włączyć wymaganą metrykę |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
