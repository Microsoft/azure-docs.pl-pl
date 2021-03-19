---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8e9f2354755a7141d7a156cec4ece73577cfb0cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587703"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Definicja aplikacji dla aplikacji zarządzanej powinna korzystać z konta magazynu dostarczonego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Użyj własnego konta magazynu, aby kontrolować dane definicji aplikacji, gdy jest to wymaganie prawne lub zgodne. Możesz zdecydować się na przechowywanie definicji aplikacji zarządzanej w ramach konta magazynu dostarczonego przez użytkownika podczas tworzenia, tak aby jego lokalizacja i dostęp mogły być w pełni zarządzane przez użytkownika w celu spełnienia wymagań dotyczących zgodności z przepisami. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Wdróż skojarzenia dla aplikacji zarządzanej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Wdraża zasób skojarzenia, który kojarzy wybrane typy zasobów z określoną aplikacją zarządzaną.  To wdrożenie zasad nie obsługuje zagnieżdżonych typów zasobów. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
