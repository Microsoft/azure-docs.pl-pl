---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90142fbe2d6feb2a6299a765c563b48388a8fa5f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513495"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dla serwerów PostgreSQL należy włączyć prywatny punkt końcowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |Prywatne połączenia punktu końcowego wymuszają bezpieczną komunikację, umożliwiając łączność prywatną Azure Database for PostgreSQL. Skonfiguruj połączenie prywatnego punktu końcowego, aby umożliwić dostęp do ruchu wychodzącego tylko ze znanych sieci i uniemożliwić dostęp ze wszystkich innych adresów IP, w tym z platformy Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
