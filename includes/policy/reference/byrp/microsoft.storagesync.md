---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0478e606cd5eb6652b992b647d5f69b347753341
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504275"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync używać linku prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Utworzenie prywatnego punktu końcowego dla wskazanego zasobu usługi synchronizacji magazynu umożliwia adresowanie zasobu usługi synchronizacji magazynu z prywatnej przestrzeni adresowej IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego z Internetu. Samo utworzenie prywatnego punktu końcowego nie powoduje wyłączenia publicznego punktu końcowego. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurowanie Azure File Sync z prywatnymi punktami końcowymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Prywatny punkt końcowy jest wdrażany dla wskazanego zasobu usługi synchronizacji magazynu. Dzięki temu można adresować zasób usługi synchronizacji magazynu z poziomu prywatnej przestrzeni adresowej IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego z Internetu. Istnienie co najmniej jednego prywatnego punktu końcowego samodzielnie nie powoduje wyłączenia publicznego punktu końcowego. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modyfikowanie — konfigurowanie Azure File Sync w celu wyłączenia dostępu do sieci publicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Publiczny Azure File Sync punktu końcowego dostępnego z Internetu jest wyłączony przez zasady organizacji. Nadal możesz uzyskać dostęp do usługi synchronizacji magazynu za pośrednictwem jej prywatnych punktów końcowych. |Modyfikowanie, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Dostęp do sieci publicznej powinien być wyłączony dla Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Wyłączenie publicznego punktu końcowego umożliwia ograniczenie dostępu do zasobu usługi synchronizacji magazynu do żądań przeznaczonych do zatwierdzonych prywatnych punktów końcowych w sieci organizacji. Zezwalanie na żądania do publicznego punktu końcowego nie jest z założenia niezabezpieczone, można jednak wyłączyć go w celu spełnienia wymagań prawnych, prawnych lub organizacyjnych dotyczących zasad. Publiczny punkt końcowy dla usługi synchronizacji magazynu można wyłączyć, ustawiając parametr incomingTrafficPolicy zasobu na allowVirtualNetworksOnly. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
