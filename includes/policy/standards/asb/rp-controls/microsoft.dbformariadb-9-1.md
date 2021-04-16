---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4faee8c64c43b6897ed141cbbd71cbbaf5ec4909
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511673"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
