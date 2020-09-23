---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 45d0516811c4391146803dc244be51e0b32ac58f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982650"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szablony konstruktora obrazów maszyn wirtualnych powinny używać linku prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Inspekcja szablonów konstruktora obrazów maszyn wirtualnych, dla których nie skonfigurowano sieci wirtualnej. Gdy sieć wirtualna nie jest skonfigurowana, zostanie utworzony i użyty publiczny adres IP, który może ujawnić zasoby bezpośrednio do Internetu i zwiększyć potencjalną powierzchnię ataku. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
