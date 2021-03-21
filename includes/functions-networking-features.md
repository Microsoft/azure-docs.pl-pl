---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936768"
---


| Cecha |[Plan Zużycie](../articles/azure-functions/consumption-plan.md)|[Plan Premium](../articles/azure-functions/functions-premium-plan.md)|[Dedykowany plan](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Tak|✅Tak|✅Tak|✅Tak|✅Tak|
|[Integracja z siecią wirtualną](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nie|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Tak| ✅Tak|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nie| ✅Tak |✅Tak|✅Tak|✅Tak|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Nie|✅Tak|✅Tak|✅Tak|✅Tak|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nie| ✅Tak|✅Tak|✅Tak|✅Opcję|