---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 25011470c85338fcc20565130c2d6765d90e74ad
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879465"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
