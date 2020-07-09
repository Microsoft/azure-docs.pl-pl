---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906728"
---


| Cecha |[Plan Zużycie](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dedykowany](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Opcję|✅Opcję|✅Opcję|✅Opcję|✅Opcję|
|[Integracja z siecią wirtualną](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nie|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Opcję| ✅Opcję|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nie| ✅Opcję |✅Opcję|✅Opcję|✅Opcję|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Nie|✅Opcję|✅Opcję|✅Opcję|✅Opcję|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nie| ✅Opcję|✅Opcję|✅Opcję|✅Opcję|