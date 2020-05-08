---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8d390256c44bc37070594596f627fe52f325c6a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966148"
---
|Nazwa |Opis |Efekt (s) |Wersja |GitHub |
|---|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Ta zasada przeprowadza inspekcję wszelkich Azure Database for MariaDB z niewłączonym geograficznie nadmiarową kopią zapasową. |Inspekcja, wyłączona |1.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Serwer MariaDB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Ta zasada przeprowadza inspekcję serwerów MariaDB, które nie są skonfigurowane do używania punktu końcowego usługi sieci wirtualnej. Aby uzyskać więcej informacji, [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork)odwiedź stronę. |AuditIfNotExists, wyłączone |1.0.1 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Ta zasada przeprowadza inspekcję serwerów MariaDB, które nie są skonfigurowane do korzystania z prywatnego punktu końcowego. Aby uzyskać więcej informacji, [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)odwiedź stronę. |AuditIfNotExists, wyłączone |1.0.1 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Publiczny dostęp do sieci powinien być wyłączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Ta zasada przeprowadza inspekcję serwerów MariaDB w środowisku z włączonym dostępem do sieci publicznej. Aby uzyskać więcej informacji, [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542)odwiedź stronę. |Inspekcja, wyłączona |1.0.0 |[Łącze](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
