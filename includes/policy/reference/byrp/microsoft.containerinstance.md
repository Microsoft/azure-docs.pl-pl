---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d72d4493b39d959c5429ed2ca9f5883831b9372a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862739"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Grupa kontenerów wystąpienia kontenera platformy Azure powinna zostać wdrożona w sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Bezpieczna komunikacja między kontenerami za pomocą sieci wirtualnych platformy Azure. Po określeniu sieci wirtualnej zasoby w tej sieci wirtualnej mogą bezpiecznie i prywatnie komunikować się ze sobą. |Inspekcja, Wyłączone, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Grupa kontenerów wystąpienia kontenera platformy Azure powinna używać klucza zarządzanego przez klienta do szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Zabezpieczanie kontenerów z większą elastycznością przy użyciu kluczy zarządzanych przez klienta. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Korzystanie z kluczy zarządzanych przez klienta zapewnia dodatkowe możliwości kontrolowania rotacji klucza szyfrowania klucza lub kryptograficznie wymazywania danych. |Inspekcja, Wyłączone, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
