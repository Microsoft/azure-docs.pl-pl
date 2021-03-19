---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d307f34cc899f59e528f0fbc8090bdd50838bb9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605643"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szablony konstruktora obrazów maszyn wirtualnych powinny używać linku prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Inspekcja szablonów konstruktora obrazów maszyn wirtualnych, dla których nie skonfigurowano sieci wirtualnej. Gdy sieć wirtualna nie jest skonfigurowana, zostanie utworzony i użyty publiczny adres IP, który może bezpośrednio uwidaczniać zasoby w Internecie i zwiększyć potencjalną powierzchnię ataku. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
