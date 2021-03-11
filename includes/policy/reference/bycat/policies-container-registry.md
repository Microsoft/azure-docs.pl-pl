---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 553627e0ae32aff698ac2d38f8eb22e0191739ac
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611103"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Klucze zarządzane przez klienta służą do zarządzania szyfrowaniem w pozostałej części zawartości rejestrów. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do użytkownika. Użytkownik ma pełną kontrolę i odpowiedzialność za kluczowy cykl życia, włącznie z rotacją i zarządzaniem. Dowiedz się więcej o [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Inspekcja, Odmów, wyłączone |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Rejestry kontenerów platformy Azure domyślnie akceptują połączenia przez Internet z hostów w dowolnej sieci. Aby chronić rejestry przed potencjalnymi zagrożeniami, Zezwól na dostęp tylko z określonych publicznych adresów IP lub zakresów adresów. Jeśli rejestr nie ma reguły IP/zapory lub skonfigurowano sieci wirtualnej, będzie ona wyświetlana w zasobach w złej kondycji. Dowiedz się więcej o regułach sieci Container Registry tutaj: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) i tutaj [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Rejestry kontenerów powinny używać prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Link prywatny platformy Azure umożliwia łączenie sieci wirtualnej z usługami platformy Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Platforma linków prywatnych obsługuje łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. Mapowanie prywatnych punktów końcowych do rejestrów kontenerów zamiast całej usługi spowoduje również ochronę przed ryzykiem wycieku danych. Dowiedz się więcej o: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
