---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0f48d14174a880d0de0072cb39cc6853ced1db03
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879904"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dla serwerów PostgreSQL należy włączyć prywatny punkt końcowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |Połączenia prywatnego punktu końcowego wymuszają bezpieczną komunikację przez włączenie prywatnej łączności Azure Database for PostgreSQL. Skonfiguruj połączenie prywatnego punktu końcowego, aby umożliwić dostęp do ruchu wychodzącego tylko ze znanych sieci i uniemożliwić dostęp ze wszystkich innych adresów IP, w tym z platformy Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
