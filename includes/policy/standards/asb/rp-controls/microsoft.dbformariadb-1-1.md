---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f85027452173bc894a435a545ce4534c1729b837
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510650"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Połączenia z prywatnymi punktami końcowymi wymuszają bezpieczną komunikację, umożliwiając łączność prywatną Azure Database for MariaDB. Skonfiguruj połączenie prywatnego punktu końcowego, aby umożliwić dostęp do ruchu wychodzącego tylko ze znanych sieci i uniemożliwić dostęp ze wszystkich innych adresów IP, w tym z platformy Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
