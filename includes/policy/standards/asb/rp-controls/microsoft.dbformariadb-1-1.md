---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ef6a5aaa936c3aa590ce9fb0e5efd939b0035cc
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097460"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Połączenia prywatne punktów końcowych wymuszają bezpieczną komunikację przez włączenie prywatnej łączności do Azure Database for MariaDB. Skonfiguruj połączenie prywatnego punktu końcowego, aby umożliwić dostęp do ruchu pochodzącego tylko ze znanych sieci i uniemożliwić dostęp ze wszystkich innych adresów IP, w tym na platformie Azure. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
