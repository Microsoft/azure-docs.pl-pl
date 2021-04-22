---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 39d8bb0d61a49a5a4818f944adaace93cd0f85e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880344"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cały ruch internetowy powinien być przekierowynyny za pośrednictwem wdrożonego Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center, że niektóre podsieci nie są chronione za pomocą zapory nowej generacji. Chroń podsieci przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą Azure Firewall lub obsługiwanej zapory nowej generacji |AuditIfNotExists, Disabled |[3.0.0 —wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieci zawierają listę reguł listy Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Sieci wirtualne powinny używać określonej bramy sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Te zasady przeprowadza inspekcję dowolnej sieci wirtualnej, jeśli trasa domyślna nie wskaże określonej bramy sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
