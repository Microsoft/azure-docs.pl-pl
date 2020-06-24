---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906728"
---


| Cecha |[Plan Zużycie](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dedykowany](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Tak|✅Tak|✅Tak|✅Tak|✅Tak|
|[Integracja z siecią wirtualną](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nie|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Tak| ✅Tak|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nie| ✅Tak |✅Tak|✅Tak|✅Tak|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Nie|✅Tak|✅Tak|✅Tak|✅Tak|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nie| ✅Tak|✅Tak|✅Tak|✅Opcję|