---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8920752a6adfe5f3259c4bf701173d2d817aa7da
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277321"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Inspekcja rejestrów kontenerów, dla których szyfrowanie nie jest włączone z kluczami zarządzanymi przez klienta (CMK). Aby uzyskać więcej informacji na temat szyfrowania CMK, odwiedź stronę: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Przeprowadź inspekcję rejestrów kontenerów, w których nie skonfigurowano żadnych reguł sieci (IP lub VNET) i domyślnie Zezwól na cały dostęp do sieci. Rejestry kontenerów z co najmniej jedną regułą IP/zaporą lub skonfigurowaną siecią wirtualną będą uznawane za zgodne. Aby uzyskać więcej informacji na temat Container Registry reguł sieci, odwiedź stronę: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Rejestry kontenerów powinny używać linków prywatnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Przeprowadź inspekcję rejestrów kontenerów, które nie mają co najmniej jednego zatwierdzonego prywatnego połączenia z punktem końcowym. Klienci w sieci wirtualnej mogą bezpiecznie uzyskiwać dostęp do zasobów, które mają prywatne połączenia punktów końcowych za pomocą linków prywatnych. Aby uzyskać więcej informacji, odwiedź stronę: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
