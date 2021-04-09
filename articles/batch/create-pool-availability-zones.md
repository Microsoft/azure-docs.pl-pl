---
title: Tworzenie puli w różnych strefach dostępności
description: Dowiedz się, jak utworzyć pulę wsadową z zasadami stref, aby ułatwić ochronę przed awariami.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725292"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Utwórz pulę Azure Batch w Strefy dostępności

Regiony platformy Azure, które obsługują [strefy dostępności](https://azure.microsoft.com/global-infrastructure/availability-zones/) mają co najmniej trzy oddzielne strefy, z których każdy ma własne niezależne źródło napięcia, Sieć i system chłodzenia. Podczas tworzenia puli Azure Batch przy użyciu konfiguracji maszyny wirtualnej można zainicjować obsługę administracyjną puli usługi Batch w Strefy dostępności. Tworzenie puli za pomocą tych zasad strefowych ułatwia ochronę węzłów obliczeniowych usługi Batch przed awariami poziomu centrum danych platformy Azure.

Można na przykład utworzyć pulę z zasadami stref w regionie świadczenia usługi Azure, która obsługuje trzy Strefy dostępności. Jeśli centrum danych platformy Azure w jednej strefie dostępności ma awarię infrastruktury, Pula usługi Batch nadal będzie mieć węzły w dobrej kondycji w dwóch Strefy dostępności, więc Pula pozostanie dostępna do planowania zadań.

## <a name="regional-support-and-other-requirements"></a>Wsparcie regionalne i inne wymagania

Usługa Batch zachowuje parzystość na platformie Azure na Strefy dostępności. Aby można było korzystać z opcji zona, należy utworzyć pulę w [obsługiwanym regionie platformy Azure](../availability-zones/az-region.md).

Aby Pula usługi Batch mogła zostać przypisana w strefach dostępności, region platformy Azure, w którym tworzona jest Pula, musi obsługiwać żądaną jednostkę SKU maszyny wirtualnej w więcej niż jednej strefie. Można to sprawdzić, wywołując [interfejs API listy jednostek SKU zasobów](/rest/api/compute/resourceskus/list) i sprawdzając pole **locationInfo** [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Upewnij się, że dla wymaganej jednostki SKU maszyny wirtualnej jest obsługiwana więcej niż jedna strefa.

W przypadku [kont usługi Batch w trybie subskrypcji użytkownika](accounts.md#batch-accounts)upewnij się, że subskrypcja, w której tworzysz pulę, nie ma ograniczeń oferty strefy dla wymaganej jednostki SKU maszyny wirtualnej. Aby to potwierdzić, wywołaj [interfejs API list SKU zasobów](/rest/api/compute/resourceskus/list) i sprawdź [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Jeśli istnieje ograniczenie strefy, możesz przesłać [bilet pomocy technicznej](/troubleshoot/azure/general/region-access-request-process) w celu usunięcia ograniczenia strefy.

Należy również pamiętać, że nie można utworzyć puli przy użyciu zasad strefowych, jeśli ma włączona komunikacja między węzłami i używa [jednostki SKU maszyny wirtualnej](../virtual-machines/workloads/hpc/enable-infiniband.md)obsługującej InfiniBand.

## <a name="create-a-batch-pool-across-availability-zones"></a>Utwórz pulę wsadową dla Strefy dostępności

W poniższych przykładach pokazano, jak utworzyć pulę wsadową dla Strefy dostępności.

> [!NOTE]
> Podczas tworzenia puli za pomocą zasad strefowych usługa Batch podejmie próbę przydzielenia puli między wszystkimi Strefy dostępności w wybranym regionie; nie można określić konkretnej alokacji w strefach.

### <a name="batch-management-client-net-sdk"></a>Zestaw .NET SDK klienta zarządzania partiami

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Interfejs API REST usługi Batch

ADRES URL INTERFEJSU API REST

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Treść żądania

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej na temat [tworzenia puli w podsieci sieci wirtualnej platformy Azure](batch-virtual-network.md).
- Dowiedz się więcej [na temat tworzenia puli Azure Batch bez publicznych adresów IP](./batch-pool-no-public-ip-address.md).