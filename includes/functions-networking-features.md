---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936768"
---


| Cechy |[Plan Zużycie](../articles/azure-functions/consumption-plan.md)|[Plan Premium](../articles/azure-functions/functions-premium-plan.md)|[Plan dedykowany](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Ograniczenia przychodzącego adresu IP i dostępu do lokacji prywatnej](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integracja z siecią wirtualną](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nie|✅Tak (regionalne)|✅Tak (regionalne i brama)|✅Yes| ✅Yes|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nie| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Połączenia hybrydowe](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (tylko system Windows)|❌Nie|✅Yes|✅Yes|✅Yes|✅Yes|
|[Ograniczenia wychodzącego adresu IP](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nie| ✅Yes|✅Yes|✅Yes|✅Opcję|