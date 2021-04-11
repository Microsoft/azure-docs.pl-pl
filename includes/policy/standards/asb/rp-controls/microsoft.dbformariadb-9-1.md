---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a602d126f99a3e667ed1f0d8e3457aa68eb8dd3b
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097069"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
