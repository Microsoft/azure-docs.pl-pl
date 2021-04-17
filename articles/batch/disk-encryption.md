---
title: Tworzenie puli z włączonym szyfrowaniem dysku
description: Dowiedz się, jak za pomocą konfiguracji szyfrowania dysków szyfrować węzły przy użyciu klucza zarządzanego przez platformę.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 40281211e5eb70089f4168dcb02720c912120a35
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576112"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Tworzenie puli z włączonym szyfrowaniem dysku

Podczas tworzenia puli Azure Batch przy [](nodes-and-pools.md#virtual-machine-configuration)użyciu konfiguracji maszyny wirtualnej można szyfrować węzły obliczeniowe w puli przy użyciu klucza zarządzanego przez platformę, określając konfigurację szyfrowania dysków.

W tym artykule wyjaśniono, jak utworzyć pulę usługi Batch z włączonym szyfrowaniem dysków.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Dlaczego warto używać puli z konfiguracją szyfrowania dysków?

Pula usługi Batch umożliwia uzyskiwanie dostępu do danych i przechowywanie ich na dyskach systemu operacyjnego i dyskach tymczasowych węzła obliczeniowego. Szyfrowanie dysku po stronie serwera za pomocą klucza zarządzanego przez platformę chroni te dane przy niskim narzucie i wygodzie.

Usługa Batch zastosuje jedną z tych technologii szyfrowania dysków w węzłach obliczeniowych w oparciu o konfigurację puli i regionalną obsługę.

- [Szyfrowanie dysków zarządzanych w spoczynku przy użyciu kluczy zarządzanych przez platformę](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Szyfrowanie na hoście przy użyciu klucza zarządzanego przez platformę](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Usługa Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Nie będzie można określić, która metoda szyfrowania zostanie zastosowana do węzłów w puli. Zamiast tego należy podać dyski docelowe, które chcesz zaszyfrować w węzłach, a usługa Batch może wybrać odpowiednią metodę szyfrowania, zapewniając szyfrowanie określonych dysków w węźle obliczeniowym.

> [!IMPORTANT]
> Jeśli tworzysz pulę przy użyciu obrazu [niestandardowego,](batch-sig-images.md)szyfrowanie dysków można włączyć tylko w przypadku korzystania z maszyn wirtualnych z systemem Windows.

## <a name="azure-portal"></a>Azure Portal

Podczas tworzenia puli usługi Batch w Azure Portal wybierz pozycję **TemporaryDisk** lub **OsAndTemporaryDisk** w obszarze **Konfiguracja szyfrowania dysków.**

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Zrzut ekranu przedstawiający opcję Konfiguracja szyfrowania dysków w Azure Portal.":::

Po utworzeniu puli obiekty docelowe konfiguracji szyfrowania dysków można zobaczyć w sekcji **Właściwości** puli.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Zrzut ekranu przedstawiający obiekty docelowe konfiguracji szyfrowania dysków w Azure Portal.":::

## <a name="examples"></a>Przykłady

Poniższe przykłady pokazują, jak zaszyfrować system operacyjny i dyski tymczasowe w puli usługi Batch przy użyciu zestawu .NET SDK usługi Batch, interfejsu API REST usługi Batch i interfejsu wiersza polecenia platformy Azure.

### <a name="batch-net-sdk"></a>Zestaw SDK platformy .NET usługi Batch

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

Adres URL interfejsu API REST:

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

- Dowiedz się więcej [o szyfrowaniu po stronie serwera Azure Disk Storage](../virtual-machines/disk-encryption.md).
- Aby uzyskać szczegółowe omówienie usługi Batch, zobacz Batch [service workflow and resources (Przepływ](batch-service-workflow-features.md)pracy i zasoby usługi Batch).
