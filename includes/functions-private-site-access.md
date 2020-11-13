---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578833"
---
[Prywatny punkt końcowy platformy Azure](../articles/private-link/private-endpoint-overview.md) to interfejs sieciowy, który nawiązuje prywatne i bezpieczne połączenie z usługą obsługiwaną przez usługę Azure Private Link.  Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej.

Możesz użyć prywatnego punktu końcowego dla funkcji hostowanych w planach [Premium](../articles/azure-functions/functions-premium-plan.md) i [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) .

Podczas tworzenia przychodzącego połączenia prywatnego punktu końcowego dla funkcji należy również zarejestrować rekord DNS w celu rozpoznania adresu prywatnego.  Domyślnie podczas tworzenia prywatnego punktu końcowego przy użyciu Azure Portal zostanie utworzony prywatny rekord DNS.

Aby dowiedzieć się więcej, zobacz [Używanie prywatnych punktów końcowych dla Web Apps](../articles/app-service/networking/private-endpoint.md).