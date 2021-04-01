---
title: Tworzenie puli z określonymi publicznymi adresami IP
description: Dowiedz się, jak utworzyć pulę wsadową korzystającą z własnych publicznych adresów IP.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849332"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Utwórz pulę Azure Batch z określonymi publicznymi adresami IP

Podczas tworzenia puli Azure Batch można [zainicjować obsługę administracyjną puli w podsieci określonej przez użytkownika sieci wirtualnej platformy Azure](batch-virtual-network.md) . Do maszyn wirtualnych w puli wsadowej uzyskuje się dostęp za pomocą publicznych adresów IP, które są tworzone przez program Batch. Te publiczne adresy IP mogą ulec zmianie w okresie istnienia puli, co oznacza, że ustawienia sieci mogą stać się nieaktualne, jeśli adresy IP nie są odświeżane.

Można utworzyć listę statycznych publicznych adresów IP do użycia z maszynami wirtualnymi w puli. Dzięki temu można kontrolować listę publicznych adresów IP i upewnić się, że nie zmieniają się nieoczekiwanie. Może to być szczególnie przydatne w przypadku pracy z dowolnymi usługami zewnętrznymi, takimi jak baza danych, która ogranicza dostęp do określonych adresów IP.

Aby uzyskać informacje na temat tworzenia pul bez publicznych adresów IP, przeczytaj artykuł [Tworzenie puli Azure Batch bez publicznych adresów IP](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Uwierzytelnianie**. Aby korzystać z publicznego adresu IP, interfejs API klienta usługi Batch musi korzystać z [uwierzytelniania Azure Active Directory (AD)](batch-aad-auth.md).

- **Sieć wirtualna platformy Azure**. Musisz użyć [sieci wirtualnej](batch-virtual-network.md) z tej samej subskrypcji platformy Azure, w której tworzysz pulę i adresy IP. Można używać tylko sieci wirtualnych opartych na Azure Resource Manager. Upewnij się, że sieć wirtualna spełnia wszystkie [Ogólne wymagania](batch-virtual-network.md#vnet-requirements).

- **Co najmniej jeden publiczny adres IP platformy Azure**. Aby utworzyć co najmniej jeden publiczny adres IP, możesz użyć [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), [interfejsu wiersza polecenia platformy Command-Line Azure](/cli/azure/network/public-ip#az-network-public-ip-create)lub [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Pamiętaj, aby postępować zgodnie z wymaganiami wymienionymi poniżej.

> [!NOTE]
> Funkcja Batch automatycznie przydziela dodatkowe zasoby sieciowe do grupy zasobów zawierającej publiczne adresy IP. W przypadku każdego dedykowanego węzła 100 usługa Batch zazwyczaj przydziela jedną siećową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) i jeden moduł równoważenia obciążenia. Te zasoby są ograniczone przez przydziały zasobów subskrypcji. W przypadku korzystania z większych pul może być konieczne [zażądanie zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota) dla co najmniej jednego z tych zasobów.

## <a name="public-ip-address-requirements"></a>Wymagania dotyczące publicznego adresu IP

Podczas tworzenia publicznych adresów IP należy wziąć pod uwagę następujące wymagania:

- Publiczne adresy IP muszą znajdować się w tej samej subskrypcji i regionie co konto usługi Batch, którego używasz do tworzenia puli.
- **Przypisanie adresu IP** musi być ustawione na wartość **static**.
- **Jednostka SKU** musi być ustawiona na wartość **standardowa**.
- Należy określić nazwę DNS.
- Publiczne adresy IP muszą być używane tylko dla pul konfiguracji maszyny wirtualnej. Żadne inne zasoby nie powinny korzystać z tych adresów IP, ponieważ mogą wystąpić błędy alokacji puli.
- Żadne zasady zabezpieczeń lub blokady zasobów nie powinny ograniczać dostępu użytkownika do publicznego adresu IP.
- Liczba publicznych adresów IP określona dla puli musi być wystarczająco duża, aby pomieścić liczbę maszyn wirtualnych przeznaczonych dla puli. Ta wartość musi być co najmniej sumą właściwości **targetDedicatedNodes**   i **targetLowPriorityNodes**   puli. Jeśli nie ma wystarczającej liczby adresów IP, Pula częściowo przydzieli węzły obliczeniowe, a wystąpi błąd zmiany rozmiaru. Obecnie w usłudze Batch jest stosowany jeden publiczny adres IP dla każdej maszyny wirtualnej 100.
- Zawsze mają dodatkowy bufor publicznych adresów IP. Zalecamy dodanie co najmniej jednego dodatkowego publicznego adresu IP lub około 10% łącznej liczby publicznych adresów IP dodawanych do puli, w zależności od tego, która z nich jest większa. Ten dodatkowy bufor pomaga w przetwarzaniu wsadowym podczas skalowania w dół, a także umożliwia szybsze skalowanie w górę po nieudanym skalowaniu w górę lub w dół.
- Po utworzeniu puli nie można dodać ani zmienić listy publicznych adresów IP używanych przez pulę. Jeśli musisz zmodyfikować listę, musisz usunąć pulę, a następnie utworzyć ją ponownie.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Tworzenie puli wsadowej z publicznymi adresami IP

W poniższym przykładzie pokazano, jak za pomocą [interfejsu API REST usługi Azure Batch](/rest/api/batchservice/pool/add) utworzyć pulę korzystającą z publicznych adresów IP.

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

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się więcej na temat [tworzenia puli w podsieci sieci wirtualnej platformy Azure](batch-virtual-network.md).
- Dowiedz się więcej [na temat tworzenia puli Azure Batch bez publicznych adresów IP](./batch-pool-no-public-ip-address.md).

