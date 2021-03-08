---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 093c0cbb0a7bb95e99b468c56f04bfb2b84b6301
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444828"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Cały ruch internetowy powinien być kierowany przy użyciu wdrożonej zapory platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center zidentyfikował, że niektóre podsieci nie są chronione przez zaporę nowej generacji. Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą zapory platformy Azure lub obsługiwanej zapory nowej generacji |AuditIfNotExists, wyłączone |[3.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Sieci wirtualne powinny używać określonej bramy sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Ta zasada przeprowadza inspekcję dowolnej sieci wirtualnej, jeśli trasa domyślna nie wskazuje określonej bramy sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
