---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e68e49e46070827110f7abe25ce8849a0fc53a59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191324"
---
|Nazwa |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Klienci Service Bus nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki. |Inspekcja, Odmów, wyłączone |1.0.1 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Dzienniki diagnostyczne w Service Bus powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |2.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
