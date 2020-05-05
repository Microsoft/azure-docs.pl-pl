---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f1700c6c9679f4fb02f3b0fd86d5ad2bb8ea57fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82783409"
---
|Nazwa |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Azure Cosmos DB dozwolone lokalizacje](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Te zasady umożliwiają ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów Azure Cosmos DB. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych. |[parametry ("policyEffect")] |1.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ta zasada przeprowadza inspekcję wszelkich Cosmos DB nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |1.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Wdróż zaawansowaną ochronę przed zagrożeniami dla kont Cosmos DB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Te zasady umożliwiają zaawansowaną ochronę przed zagrożeniami na kontach Cosmos DB. |DeployIfNotExists, wyłączone |1.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
