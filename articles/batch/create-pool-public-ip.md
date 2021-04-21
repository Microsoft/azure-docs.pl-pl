---
title: Tworzenie puli z określonymi publicznymi adresami IP
description: Dowiedz się, jak utworzyć pulę usługi Batch, która używa Własnych publicznych adresów IP.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768399"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Tworzenie puli Azure Batch z określonymi publicznymi adresami IP

Podczas tworzenia puli Azure Batch można aprowizować pulę w podsieci sieci wirtualnej platformy [Azure,](batch-virtual-network.md) która jest określana. Dostęp do maszyn wirtualnych w puli usługi Batch uzyskuje się za pośrednictwem publicznych adresów IP utworzonych przez usługę Batch. Te publiczne adresy IP mogą ulec zmianie w okresie istnienia puli, co oznacza, że ustawienia sieciowe mogą stać się nieaktualne, jeśli adresy IP nie zostaną odświeżone.

Możesz utworzyć listę statycznych publicznych adresów IP do użycia z maszynami wirtualnymi w puli. Dzięki temu możesz kontrolować listę publicznych adresów IP i upewnić się, że nie zmienią się one nieoczekiwanie. Może to być szczególnie przydatne, jeśli pracujesz z dowolną usługą zewnętrzną, taką jak baza danych, która ogranicza dostęp do określonych adresów IP.

Aby uzyskać informacje na temat tworzenia pul bez publicznych adresów IP, zobacz [Tworzenie puli Azure Batch bez publicznych adresów IP.](./batch-pool-no-public-ip-address.md)

## <a name="prerequisites"></a>Wymagania wstępne

- **Uwierzytelnianie**. Aby użyć publicznego adresu IP, interfejs API klienta usługi Batch musi [używać Azure Active Directory (AD).](batch-aad-auth.md)

- **Sieć wirtualna platformy Azure.** Musisz użyć sieci [wirtualnej z](batch-virtual-network.md) tej samej subskrypcji platformy Azure, w której tworzysz pulę i swoje adresy IP. Można Azure Resource Manager tylko sieci wirtualne oparte na sieciach wirtualnych. Upewnij się, że sieć wirtualna spełnia wszystkie [ogólne wymagania.](batch-virtual-network.md#vnet-requirements)

- **Co najmniej jeden publiczny adres IP platformy Azure.** Aby utworzyć co najmniej jeden publiczny adres IP, możesz użyć interfejsu [Azure Portal,](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) [interfejsu](/cli/azure/network/public-ip#az_network_public_ip_create)wiersza polecenia usługi Azure Command-Line lub [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Pamiętaj, aby spełnić wymagania wymienione poniżej.

> [!NOTE]
> Usługa Batch automatycznie przydziela dodatkowe zasoby sieciowe w grupie zasobów zawierającej publiczne adresy IP. Dla każdego 100 dedykowanych węzłów usługa Batch zazwyczaj przydziela jedną sieciową grupę zabezpieczeń i jeden usługę równoważenia obciążenia. Te zasoby są ograniczone przez limity przydziału zasobów subskrypcji. W przypadku korzystania z większych pul może być konieczne [zażądanie zwiększenia limitu](batch-quota-limit.md#increase-a-quota) przydziału dla co najmniej jednego z tych zasobów.

## <a name="public-ip-address-requirements"></a>Wymagania dotyczące publicznego adresu IP

Podczas tworzenia publicznych adresów IP należy pamiętać o następujących wymaganiach:

- Publiczne adresy IP muszą znajdować się w tej samej subskrypcji i regionie co konto usługi Batch, które jest wykorzystywane do tworzenia puli.
- Przypisanie **adresu IP musi** mieć wartość **Statyczny**.
- **Dla sku** musi być ustawiona wartość **Standardowa.**
- Należy określić nazwę DNS.
- Publiczne adresy IP muszą być używane tylko dla pul konfiguracji maszyn wirtualnych. Żadne inne zasoby nie powinny używać tych adresów IP lub w puli mogą wystąpić błędy alokacji.
- Żadne zasady zabezpieczeń ani blokady zasobów nie powinny ograniczać dostępu użytkownika do publicznego adresu IP.
- Liczba publicznych adresów IP określona dla puli musi być wystarczająco duża, aby obsłużyć liczbę maszyn wirtualnych przeznaczonych dla puli. Musi to być co najmniej suma właściwości **targetDedicatedNodes** i    **targetLowPriorityNodes**   puli. Jeśli nie ma wystarczającej ilości adresów IP, pula częściowo przydziela węzły obliczeniowe i wystąpi błąd zmiany rozmiaru. Obecnie usługa Batch używa jednego publicznego adresu IP dla każdej 100 maszyn wirtualnych.
- Zawsze mają dodatkowy bufor publicznych adresów IP. Zalecamy dodanie co najmniej jednego dodatkowego publicznego adresu IP lub około 10% całkowitej liczby publicznych adresów IP, które dodajesz do puli, w zależności od tego, która wartość jest większa. Ten dodatkowy bufor pomoże u usługi Batch w optymalizacji wewnętrznej podczas skalowania w dół, a także umożliwia szybsze skalowanie w górę po nieudanym skalowaniu w górę lub w dół.
- Po utworzeniu puli nie można dodać ani zmienić listy publicznych adresów IP używanych przez pulę. Jeśli musisz zmodyfikować listę, musisz usunąć pulę, a następnie utworzyć ją ponownie.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Tworzenie puli usługi Batch z publicznymi adresami IP

W poniższym przykładzie pokazano, jak za [pomocą Azure Batch interfejs API REST usługi](/rest/api/batchservice/pool/add) utworzyć pulę, która używa publicznych adresów IP.

### <a name="batch-service-rest-api"></a>Batch Service REST API (Interfejs API REST usługi Batch)

Identyfikator URI interfejsu API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Treść żądania

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych,](batch-service-workflow-features.md) takich jak pule, węzły, zadania i zadania podrzędne.
- Dowiedz się [więcej na temat tworzenia puli w podsieci sieci wirtualnej platformy Azure.](batch-virtual-network.md)
- Dowiedz się [więcej o tworzeniu puli Azure Batch bez publicznych adresów IP.](./batch-pool-no-public-ip-address.md)
