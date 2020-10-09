---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9f352c840dc18c3155b80b57701b42d1ecffa794
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91820633"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konfiguracja aplikacji powinna używać klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Klucze zarządzane przez klienta zapewniają ulepszoną ochronę danych, umożliwiając zarządzanie kluczami szyfrowania. Jest to często wymagane w celu spełnienia wymagań dotyczących zgodności. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[W przypadku konfiguracji aplikacji należy użyć prywatnego linku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Połączenia prywatnego punktu końcowego umożliwiają klientom w sieci wirtualnej bezpieczne uzyskiwanie dostępu do konfiguracji aplikacji platformy Azure za pośrednictwem prywatnego linku. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
