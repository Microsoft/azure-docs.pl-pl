---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648829"
---
Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak sieć wirtualna platformy Azure.

* Dostęp do lokacji prywatnej jest dostępny w planach [Premium](../articles/azure-functions/functions-premium-plan.md), [zużycia](../articles/azure-functions/functions-scale.md#consumption-plan)i [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) w przypadku skonfigurowania punktów końcowych usługi.
    * Punkty końcowe usługi można skonfigurować dla poszczególnych aplikacji w obszarze **funkcje platformy**  >  **Networking**  >  **Konfigurowanie ograniczeń dostępu**  >  **Dodaj regułę**. Sieci wirtualne można teraz wybrać jako typ reguły.
    * Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Należy pamiętać, że dzięki punktom końcowym usługi funkcja nadal ma pełny dostęp wychodzący do Internetu, nawet z skonfigurowaną integracją sieci wirtualnej.
* Dostęp do lokacji prywatnej jest również dostępny w ramach App Service Environment, który jest skonfigurowany przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB). Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z App Service Environment](../articles/app-service/environment/create-ilb-ase.md).

Aby dowiedzieć się, jak skonfigurować dostęp do lokacji prywatnej, zobacz [ustanawianie Azure Functions dostępu do lokacji prywatnej](../articles/azure-functions/functions-create-private-site-access.md).