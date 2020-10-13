---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906728"
---


| Cecha |[Plan Zużycie](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dedykowany](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Opcję|✅Opcję|✅Opcję|✅Opcję|✅Opcję|
|[Integracja sieci wirtualnej](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Znaleziono|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Opcję| ✅Opcję|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Znaleziono| ✅Opcję |✅Opcję|✅Opcję|✅Opcję|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Znaleziono|✅Opcję|✅Opcję|✅Opcję|✅Opcję|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Znaleziono| ✅Opcję|✅Opcję|✅Opcję|✅Opcję|