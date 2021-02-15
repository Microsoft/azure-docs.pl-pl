---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3cd3b12a22742b696b0f7b511a47d16b911a3b68
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096513"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Definicja aplikacji dla aplikacji zarządzanej powinna korzystać z konta magazynu dostarczonego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Użyj własnego konta magazynu, aby kontrolować dane definicji aplikacji, gdy jest to wymaganie prawne lub zgodne. Możesz zdecydować się na przechowywanie definicji aplikacji zarządzanej w ramach konta magazynu dostarczonego przez użytkownika podczas tworzenia, tak aby jego lokalizacja i dostęp mogły być w pełni zarządzane przez użytkownika w celu spełnienia wymagań dotyczących zgodności z przepisami. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Wdróż skojarzenia dla aplikacji zarządzanej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Wdraża zasób skojarzenia, który kojarzy wybrane typy zasobów z określoną aplikacją zarządzaną.  To wdrożenie zasad nie obsługuje zagnieżdżonych typów zasobów. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
