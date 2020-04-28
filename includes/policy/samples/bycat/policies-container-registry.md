---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8de55ced9d75573c49d4cc069c2c3db24e7932d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191198"
---
|Nazwa |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Inspekcja rejestrów kontenerów, dla których szyfrowanie nie jest włączone z kluczami zarządzanymi przez klienta (CMK). Aby uzyskać więcej informacji na temat szyfrowania CMK, odwiedź [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)stronę:. |Inspekcja, wyłączona |1.0.0 — wersja zapoznawcza |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Przeprowadź inspekcję rejestrów kontenerów, w których nie skonfigurowano żadnych reguł sieci (IP lub VNET) i domyślnie Zezwól na cały dostęp do sieci. Rejestry kontenerów z co najmniej jedną regułą IP/zaporą lub skonfigurowaną siecią wirtualną będą uznawane za zgodne. Aby uzyskać więcej informacji na temat Container Registry reguł sieci, odwiedź [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)stronę:. |Inspekcja, wyłączona |1.0.0 — wersja zapoznawcza |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
