---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d1ccfe9dfd2f90572dd7b391275e955dc4128af5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499118"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Definicja aplikacji dla aplikacji zarządzanej powinna używać konta magazynu dostarczonego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Użyj własnego konta magazynu, aby kontrolować dane definicji aplikacji, gdy jest to wymaganie prawne lub zgodność. Definicję aplikacji zarządzanej można przechowywać w ramach konta magazynu dostarczonego podczas tworzenia, dzięki czemu jego lokalizacja i dostęp mogą być w pełni zarządzane przez użytkownika w celu spełnienia wymagań dotyczących zgodności z przepisami. |inspekcja, odmowa, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Wdrażanie skojarzeń dla aplikacji zarządzanej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Wdraża zasób skojarzenia, który kojarzy wybrane typy zasobów z określoną aplikacją zarządzaną.  To wdrożenie zasad nie obsługuje zagnieżdżonych typów zasobów. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
