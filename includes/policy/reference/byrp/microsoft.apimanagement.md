---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866254"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management powinna używać sku, który obsługuje sieci wirtualne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Dzięki obsługiwanym jednostkom SKU API Management wdrażanie usługi w sieci wirtualnej odblokowuje zaawansowane funkcje sieci API Management i zabezpieczeń, które zapewniają większą kontrolę nad konfiguracją zabezpieczeń sieci. Dowiedz się więcej na stronie: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management usługi powinny używać sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Wdrożenie usługi Azure Virtual Network zapewnia zwiększone zabezpieczenia, izolację i pozwala umieścić usługę API Management w sieci, do których kontrolujesz dostęp, w sieci nienadajnej do obsługi routowania w Internecie. Te sieci można następnie połączyć z sieciami lokalnymi przy użyciu różnych technologii sieci VPN, co umożliwia dostęp do usług zaplecza w sieci i/lub lokalnie. Portal dla deweloperów i brama interfejsu API można skonfigurować tak, aby były dostępne z Internetu lub tylko w sieci wirtualnej. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
