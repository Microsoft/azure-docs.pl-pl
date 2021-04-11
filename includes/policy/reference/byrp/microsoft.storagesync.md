---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b3939094b4299ccf75e4953443f8ce9d0075c59
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031693"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure File Sync powinien korzystać z prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Utworzenie prywatnego punktu końcowego dla wskazanego zasobu usługi synchronizacji magazynu pozwala na rozwiązanie zasobu usługi synchronizacji magazynu z poziomu prywatnej przestrzeni adresów IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego w Internecie. Utworzenie prywatnego punktu końcowego nie powoduje wyłączenia publicznego punktu końcowego. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Konfigurowanie Azure File Sync z prywatnymi punktami końcowymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Dla wskazanego zasobu usługi synchronizacji magazynu wdrożono prywatny punkt końcowy. Pozwala to na adresowanie zasobu usługi synchronizacji magazynu z poziomu prywatnej przestrzeni adresowej IP sieci organizacji, a nie za pośrednictwem publicznego punktu końcowego dostępnego z Internetu. Istnienie jednego lub większej liczby prywatnych punktów końcowych nie powoduje wyłączenia publicznego punktu końcowego. |DeployIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modyfikuj — Skonfiguruj Azure File Sync, aby wyłączyć dostęp do sieci publicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Publiczny punkt końcowy dostępny do Internetu Azure File Sync jest wyłączony przez zasady organizacyjne. Dostęp do usługi synchronizacji magazynu można nadal uzyskać za pośrednictwem prywatnych punktów końcowych. |Modyfikowanie, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Dostęp do sieci publicznej powinien być wyłączony dla Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Wyłączenie publicznego punktu końcowego pozwala ograniczyć dostęp do zasobu usługi synchronizacji magazynu na żądania kierowane do zatwierdzonych prywatnych punktów końcowych w sieci organizacji. Nie ma możliwości zagwarantowania niebezpieczeństwa w zakresie zezwalania na żądania kierowane do publicznego punktu końcowego, jednak można wyłączyć go w celu spełnienia wymagań zasad wykonawczych, prawnych lub obowiązujących w organizacji. Publiczny punkt końcowy usługi synchronizacji magazynu można wyłączyć, ustawiając incomingTrafficPolicy zasobu na AllowVirtualNetworksOnly. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
