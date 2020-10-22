---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5b1e2f52366e800a3f12e304ed0e626b300e948c
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347155"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Inspekcja rejestrów kontenerów, dla których szyfrowanie nie jest włączone z kluczami zarządzanymi przez klienta (CMK). Platforma Azure automatycznie szyfruje zawartość rejestru przy użyciu kluczy zarządzanych przez usługę. Można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu klucza tworzonego i zarządzanego w Azure Key Vault. Aby uzyskać więcej informacji na temat szyfrowania CMK, odwiedź stronę: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Inspekcja rejestrów kontenerów, dla których nie skonfigurowano żadnych reguł sieci lub zapory (IP), a więc domyślnie Zezwalaj na cały dostęp do sieci. Ograniczanie dostępu do sieci chroni rejestry kontenerów przed potencjalnymi zagrożeniami. Rejestry kontenerów z co najmniej jedną regułą IP/zaporą lub skonfigurowaną siecią wirtualną są uznawane za zgodne. Aby uzyskać więcej informacji na temat Container Registry reguł sieci, odwiedź stronę: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) i [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Rejestry kontenerów powinny używać linków prywatnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Przeprowadź inspekcję rejestrów kontenerów, które nie mają co najmniej jednego zatwierdzonego prywatnego połączenia z punktem końcowym. Klienci w sieci wirtualnej mogą bezpiecznie uzyskiwać dostęp do zasobów, które mają prywatne połączenia punktów końcowych za pomocą linków prywatnych. Dostęp publiczny można następnie wyłączyć, aby można było połączyć się z rejestrem tylko za pomocą prywatnych linków. Aby uzyskać więcej informacji, odwiedź stronę: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
