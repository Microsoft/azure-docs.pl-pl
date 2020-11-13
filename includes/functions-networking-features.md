---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578845"
---


| Obiekt feature |[Plan Zużycie](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dedykowany](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Tak|✅Tak|✅Tak|✅Tak|✅Tak|
|[Integracja z siecią wirtualną](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nie|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Tak| ✅Tak|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nie| ✅Tak |✅Tak|✅Tak|✅Tak|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Nie|✅Tak|✅Tak|✅Tak|✅Tak|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nie| ✅Tak|✅Tak|✅Tak|✅Opcję|