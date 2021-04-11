---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ca68c0a770023d0362cb9278b3742a6a136c796b
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090885"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa API Management powinna używać jednostki SKU, która obsługuje sieci wirtualne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |W przypadku obsługiwanych jednostek SKU API Management wdrożenie usługi w sieci wirtualnej odblokowuje zaawansowane funkcje sieciowe API Management sieci i zabezpieczeń, co zapewnia większą kontrolę nad konfiguracją zabezpieczeń sieci. Dowiedz się więcej o: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[Usługi API Management powinny używać sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Wdrożenie usługi Azure Virtual Network zapewnia zwiększone zabezpieczenia i izolację oraz pozwala na umieszczenie usługi API Management w sieci, w której można kontrolować dostęp. Te sieci mogą następnie być połączone z sieciami lokalnymi przy użyciu różnych technologii sieci VPN, które umożliwiają dostęp do usług zaplecza w sieci i/lub lokalnie. Portal dla deweloperów i Brama interfejsu API można skonfigurować tak, aby były dostępne zarówno z Internetu, jak i tylko w obrębie sieci wirtualnej. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
