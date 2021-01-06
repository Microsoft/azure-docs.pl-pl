---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936870"
---
[Prywatny punkt końcowy platformy Azure](../articles/private-link/private-endpoint-overview.md) to interfejs sieciowy, który nawiązuje prywatne i bezpieczne połączenie z usługą obsługiwaną przez usługę Azure Private Link.  Prywatny punkt końcowy używa prywatnego adresu IP z Twojej sieci wirtualnej, skutecznie przenosząc usługę do sieci wirtualnej.

Możesz użyć prywatnego punktu końcowego dla funkcji hostowanych w planach [Premium](../articles/azure-functions/functions-premium-plan.md) i [App Service](../articles/azure-functions/dedicated-plan.md) .

Podczas tworzenia przychodzącego połączenia prywatnego punktu końcowego dla funkcji należy również zarejestrować rekord DNS w celu rozpoznania adresu prywatnego.  Domyślnie podczas tworzenia prywatnego punktu końcowego przy użyciu Azure Portal zostanie utworzony prywatny rekord DNS.

Aby dowiedzieć się więcej, zobacz [Używanie prywatnych punktów końcowych dla Web Apps](../articles/app-service/networking/private-endpoint.md).