---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fc2bdd31f0dd2649c4f8a7a38e161f04f0e80260
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097907"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Domeny Azure Event Grid powinny używać prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Link prywatny platformy Azure umożliwia łączenie sieci wirtualnej z usługami platformy Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Platforma linków prywatnych obsługuje łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. Mapowanie prywatnych punktów końcowych do domen Event Grid, a nie całej usługi, będzie również chronione przed ryzykiem wycieku danych. Dowiedz się więcej o: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Tematy Azure Event Grid powinny używać prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Link prywatny platformy Azure umożliwia łączenie sieci wirtualnej z usługami platformy Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Platforma linków prywatnych obsługuje łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. Mapowanie prywatnych punktów końcowych do tematów zamiast całej usługi spowoduje również ochronę przed ryzykiem wycieku danych. Dowiedz się więcej o: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
