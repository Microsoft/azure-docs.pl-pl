---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 355a74ded8b8d01632aff70b35f96f2502c970b7
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "107285866"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Funkcja autoaprowizacji agenta Log Analytics powinna być włączona w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |W celu monitorowania luk w zabezpieczeniach i zagrożeń Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta Log Analytics, dawniej znany jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego Log Analytics na potrzeby analizy. Zalecamy włączenie automatycznej aprowizacji, aby automatycznie wdrażać agenta na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
