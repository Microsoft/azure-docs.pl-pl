---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 44ece75ffeabc11bc047ee7ae3886fd5e263e81c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284290"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
