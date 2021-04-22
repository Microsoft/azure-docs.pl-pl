---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 871a648eada2e710604d5fb42ea83a8559049b6a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876025"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync powinien używać linku prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Utworzenie prywatnego punktu końcowego dla wskazanego zasobu usługi synchronizacji magazynu umożliwia adresowanie zasobu usługi synchronizacji magazynu z prywatnej przestrzeni adresowej IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego z Internetu. Samo utworzenie prywatnego punktu końcowego nie powoduje wyłączenia publicznego punktu końcowego. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurowanie Azure File Sync z prywatnymi punktami końcowymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Dla wskazanego zasobu usługi synchronizacji magazynu jest wdrażany prywatny punkt końcowy. Dzięki temu można adresować zasób usługi synchronizacji magazynu z prywatnej przestrzeni adresowej IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego z Internetu. Istnienie co najmniej jednego prywatnego punktu końcowego samodzielnie nie wyłącza publicznego punktu końcowego. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modyfikowanie — konfigurowanie Azure File Sync w celu wyłączenia dostępu do sieci publicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Publiczny Azure File Sync punktu końcowego dostępnego z Internetu jest wyłączony przez zasady organizacji. Nadal możesz uzyskać dostęp do usługi synchronizacji magazynu za pośrednictwem jej prywatnych punktów końcowych. |Modyfikowanie, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Dostęp do sieci publicznej powinien być wyłączony dla Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Wyłączenie publicznego punktu końcowego umożliwia ograniczenie dostępu do zasobu usługi synchronizacji magazynu do żądań przeznaczonych do zatwierdzonych prywatnych punktów końcowych w sieci organizacji. Zezwalanie na żądania do publicznego punktu końcowego nie jest z założenia niezabezpieczone, ale możesz go wyłączyć, aby spełniał wymagania prawne, prawne lub organizacyjne. Można wyłączyć publiczny punkt końcowy dla usługi synchronizacji magazynu, ustawiając parametr incomingTrafficPolicy zasobu na AllowVirtualNetworksOnly. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
