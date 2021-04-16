---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 56b2ae4aff99bed46fa5ad7f0947193b2f465431
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510444"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatyczne aprowizowanie agenta usługi Log Analytics powinno być włączone w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Aby monitorować luki w zabezpieczeniach i zagrożenia, Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta usługi Log Analytics, wcześniej znanego jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyny i kopiuje dane do obszaru roboczego usługi Log Analytics w celu analizy. Zalecamy włączenie automatycznego aprowizowania, aby automatycznie wdrożyć agenta na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowo utworzonych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
