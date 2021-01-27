---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 62581c50503cd0541c2c27671c18dd937be1efe7
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806342"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Serwer MariaDB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Reguły zapory oparte na sieci wirtualnej są używane do włączania ruchu z określonej podsieci do Azure Database for MariaDB, przy jednoczesnym zapewnieniu ruchu w ramach granicy platformy Azure. Te zasady umożliwiają inspekcję, jeśli Azure Database for MariaDB ma używany punkt końcowy usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Połączenia prywatne punktów końcowych wymuszają bezpieczną komunikację przez włączenie prywatnej łączności do Azure Database for MariaDB. Skonfiguruj połączenie prywatnego punktu końcowego, aby umożliwić dostęp do ruchu pochodzącego tylko ze znanych sieci i uniemożliwić dostęp ze wszystkich innych adresów IP, w tym na platformie Azure. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Publiczny dostęp do sieci powinien być wyłączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Wyłącz właściwość dostęp do sieci publicznej, aby zwiększyć bezpieczeństwo i upewnić się, że dostęp do Azure Database for MariaDB można uzyskać tylko z prywatnego punktu końcowego. Ta konfiguracja całkowicie wyłącza dostęp z dowolnej publicznej przestrzeni adresowej poza zakresem adresów IP platformy Azure i odmówi wszystkie nazwy logowania zgodne z regułami zapory na podstawie adresu IP lub sieci wirtualnej. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
