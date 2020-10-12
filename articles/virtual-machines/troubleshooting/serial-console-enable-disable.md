---
title: Włączanie i wyłączanie konsoli szeregowej platformy Azure | Microsoft Docs
description: Jak włączyć i wyłączyć usługę konsoli szeregowej platformy Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451303"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Włączanie i wyłączanie konsoli szeregowej platformy Azure

Podobnie jak w przypadku każdego innego zasobu, można włączyć i wyłączyć konsolę szeregową platformy Azure. Konsola szeregowa jest domyślnie włączona dla wszystkich subskrypcji na globalnym platformie Azure. Obecnie wyłączenie konsoli szeregowej spowoduje wyłączenie usługi dla całej subskrypcji. Wyłączenie lub ponowne włączenie konsoli szeregowej dla subskrypcji wymaga dostępu na poziomie współautora lub wyższego w subskrypcji.

Możesz również wyłączyć konsolę szeregową dla pojedynczej maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, wyłączając diagnostykę rozruchu. Musisz mieć dostęp do poziomu współautora lub wyższy zarówno w zestawie skalowania maszyn wirtualnych, jak i na koncie magazynu diagnostyki rozruchu.

## <a name="vm-level-disable"></a>Wyłączenie na poziomie maszyny wirtualnej
Konsolę szeregową można wyłączyć dla określonej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, wyłączając ustawienie diagnostyki rozruchu. Wyłącz diagnostykę rozruchu z Azure Portal, aby wyłączyć konsolę seryjną dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. Jeśli używasz konsoli szeregowej w zestawie skalowania maszyn wirtualnych, upewnij się, że Twoje wystąpienia zestawu skalowania maszyn wirtualnych zostały uaktualnione do najnowszego modelu.


## <a name="subscription-level-enabledisable"></a>Włączanie/wyłączanie poziomu subskrypcji

> [!NOTE]
> Przed uruchomieniem tego polecenia upewnij się, że jesteś w odpowiedniej chmurze (chmura publiczna Azure, chmura dla instytucji rządowych Stanów Zjednoczonych). Możesz sprawdzić `az cloud list` i ustawić chmurę za pomocą usługi `az cloud set -n <Name of cloud>` .

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Konsola szeregowa można wyłączyć i ponownie włączyć dla całej subskrypcji przy użyciu następujących poleceń w interfejsie wiersza polecenia platformy Azure (można użyć przycisku "Wypróbuj", aby uruchomić wystąpienie Azure Cloud Shell, w którym można uruchomić polecenia):

Aby wyłączyć konsolę szeregowa dla subskrypcji, użyj następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Aby włączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Aby uzyskać bieżący stan włączenia/wyłączenia konsoli szeregowej dla subskrypcji, użyj następujących poleceń:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

Konsola szeregowa można również włączyć i wyłączyć przy użyciu programu PowerShell.

Aby wyłączyć konsolę szeregowa dla subskrypcji, użyj następujących poleceń:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Aby włączyć konsolę szeregową dla subskrypcji, użyj następujących poleceń:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md)
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)
* Informacje o [opcjach zarządzania zużyciem w konsoli szeregowej platformy Azure](./serial-console-power-options.md)