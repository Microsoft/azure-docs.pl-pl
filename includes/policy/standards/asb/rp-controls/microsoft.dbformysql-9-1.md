---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e2e12cdd06a21d1838ad939a2cebb908bf0452a4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512782"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale także są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
