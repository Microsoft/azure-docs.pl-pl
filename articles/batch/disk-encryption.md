---
title: Tworzenie puli z włączonym szyfrowaniem dysku
description: Dowiedz się, jak za pomocą konfiguracji szyfrowania dysków szyfrować węzły z kluczem zarządzanym przez platformę.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055318"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Tworzenie puli z włączonym szyfrowaniem dysku

Podczas tworzenia puli Azure Batch przy użyciu [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration)można szyfrować węzły obliczeniowe w puli za pomocą klucza zarządzanego przez platformę, określając konfigurację szyfrowania dysku.

W tym artykule opisano sposób tworzenia puli wsadowej z włączonym szyfrowaniem dysków.

> [!IMPORTANT]
> Obsługa szyfrowania na hoście przy użyciu klucza zarządzanego przez platformę w Azure Batch jest obecnie dostępna w publicznej wersji zapoznawczej dla regionu Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, US Gov Wirginia i US Gov Arizona.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Dlaczego warto używać puli z konfiguracją szyfrowania dysków?

Za pomocą puli wsadowej można uzyskać dostęp do danych i przechowywać je w systemie operacyjnym oraz na dyskach tymczasowych w węźle obliczeniowym. Szyfrowanie dysku po stronie serwera za pomocą klucza zarządzanego przez platformę zapewnia ochronę tych danych przy niskim obciążeniu i wygodie.

Program Batch zastosuje jedną z tych technologii szyfrowania dysków w węzłach obliczeniowych na podstawie konfiguracji puli i regionalnej obsługi.

- [Zarządzane szyfrowanie dysków przy użyciu kluczy zarządzanych przez platformę](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Szyfrowanie na hoście przy użyciu klucza zarządzanego przez platformę](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Usługa Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Nie będzie można określić, która metoda szyfrowania ma być stosowana do węzłów w puli. Zamiast tego należy podać dyski docelowe, które mają zostać zaszyfrowane w swoich węzłach, a w usłudze Batch można wybrać odpowiednią metodę szyfrowania, co zapewni zaszyfrowanie określonych dysków w węźle obliczeniowym.

> [!IMPORTANT]
> W przypadku tworzenia puli za pomocą [obrazu niestandardowego](batch-sig-images.md)można włączyć szyfrowanie dysków tylko w przypadku korzystania z maszyn wirtualnych z systemem Windows.

## <a name="azure-portal"></a>Azure Portal

Podczas tworzenia puli zadań wsadowych w Azure Portal wybierz opcję **TemporaryDisk** lub **OsAndTemporaryDisk** w obszarze **Konfiguracja szyfrowania dysku**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Zrzut ekranu opcji Konfiguracja szyfrowania dysku w Azure Portal.":::

Po utworzeniu puli w sekcji **Właściwości** puli można zobaczyć cele konfiguracji szyfrowania dysków.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Zrzut ekranu przedstawiający elementy docelowe konfiguracji szyfrowania dysków w Azure Portal.":::

## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak szyfrować system operacyjny i dyski tymczasowe w puli usługi Batch przy użyciu zestawu SDK usługi Batch .NET, interfejsu API REST w usłudze Batch i wiersza polecenia platformy Azure.

### <a name="batch-net-sdk"></a>Zestaw SDK platformy .NET usługi Batch

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Interfejs API REST usługi Batch

ADRES URL INTERFEJSU API REST:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Treść żądania:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [szyfrowaniu Azure Disk Storage po stronie serwera](../virtual-machines/disk-encryption.md).
- Aby zapoznać się z szczegółowym omówieniem usługi Batch, zobacz temat [przepływ pracy i zasoby usług Batch](batch-service-workflow-features.md).
