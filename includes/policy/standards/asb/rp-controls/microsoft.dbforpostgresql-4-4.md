---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3421674323f9a6b97152f0a264c4b219b88465b4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880595"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W przypadku serwerów baz danych PostgreSQL należy włączyć wymuszanie połączenia SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
