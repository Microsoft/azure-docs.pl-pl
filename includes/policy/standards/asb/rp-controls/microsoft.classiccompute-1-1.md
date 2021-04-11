---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c54b7f2bcad81b8113e35b115dd66e395c333da
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284751"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Włączenie przekazywania adresów IP na karcie sieciowej maszyny wirtualnej umożliwia komputerowi odbieranie ruchu kierowanego do innych miejsc docelowych. Przekazywanie adresów IP jest rzadko wymagane (np. w przypadku używania maszyny wirtualnej jako sieciowego urządzenia wirtualnego), dlatego należy je sprawdzić przez zespół ds. zabezpieczeń sieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwieranie portów zarządzania zdalnego umożliwia wystawianie maszyn wirtualnych na wysokim poziomie ryzyka związanego z atakami internetowymi. Ataki te podejmują próbę nawiązania poświadczeń w celu uzyskania dostępu administratora do komputera. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
