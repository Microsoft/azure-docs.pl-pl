---
title: Umieść wywołania dla operacji tworzenia lub aktualizacji
description: Umieść wywołania operacji tworzenia lub aktualizacji w zasobach obliczeniowych
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: c57025346a9f623e3fe5536b36820ea62f355cc0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347501"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>UMIESZCZENIE wywołań do tworzenia lub aktualizacji zasobów obliczeniowych

`Microsoft.Compute` zasoby nie obsługują konwencjonalnej definicji semantyki *Put protokołu HTTP* . Zamiast tego zasoby te używają semantyki poprawek dla czasowników PUT i PATCH.

W razie potrzeby operacje **tworzenia** są stosowane w przypadku wartości domyślnych. Jednak **aktualizacje** zasobów wykonywane przez funkcję Put lub patch nie mają zastosowania do żadnych właściwości domyślnych. Operacje **aktualizacji** dotyczą stosowania ścisłej semantyki poprawek.

Na przykład `caching` Właściwość dysk maszyny wirtualnej ma wartość domyślną, `ReadWrite` Jeśli zasób jest dyskiem systemu operacyjnego.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Jednak w przypadku operacji **aktualizacji** , gdy właściwość jest pozostawiona lub przenoszona jest wartość *null* , pozostanie bez zmian i nie będzie żadnych wartości domyślnych.

Jest to ważne w przypadku wysyłania operacji aktualizacji do zasobu z zamiarem usunięcia skojarzenia. Jeśli zasób jest `Microsoft.Compute` zasobem, odpowiadająca mu właściwość, którą chcesz usunąć, musi być jawnie wywoływana i przypisana wartość. Aby to osiągnąć, użytkownicy mogą przekazać pusty ciąg, taki jak **""**. Spowoduje to, że platforma usunie to skojarzenie.

> [!IMPORTANT]
> Nie ma żadnego wsparcia dla elementu tablicy "Poprawka". Zamiast tego klient musi wykonać żądanie PUT lub PATCH przy użyciu całej zawartości zaktualizowanej tablicy. Na przykład aby odłączyć dysk danych z maszyny wirtualnej, wykonaj żądanie GET w celu pobrania bieżącego modelu maszyny wirtualnej, Usuń dysk, z którego ma zostać odłączony `properties.storageProfile.dataDisks` , i wykonaj żądanie Put ze zaktualizowaną jednostką maszyny wirtualnej.

## <a name="examples"></a>Przykłady

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Prawidłowy ładunek, aby usunąć skojarzenie grup umieszczenia zbliżeniowe

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Nieprawidłowe ładunki, aby usunąć skojarzenie grup umieszczenia zbliżeniowe

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat tworzenia lub aktualizowania wywołań dla [Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) i [Virtual Machine Scale Sets](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

