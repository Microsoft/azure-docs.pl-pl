---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 61f42e7f4dfbcfed4b6fc41d737059916e898654
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807382"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Wdrożenie usługi Azure Virtual Network (VNet) zapewnia ulepszone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla usługi Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dodatkowego ograniczenia dostępu. Gdy usługa Azure cache for Redis jest skonfigurowana przy użyciu sieci wirtualnej, nie jest ona publicznie adresowana i można uzyskać do niej dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej. |Inspekcja, Odmów, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
