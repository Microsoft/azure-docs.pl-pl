---
title: Pobieranie powiadomień konserwacyjnych przy użyciu interfejsu wiersza polecenia
description: Wyświetl powiadomienia dotyczące konserwacji maszyn wirtualnych działających na platformie Azure i uruchom konserwację samoobsługową przy użyciu interfejsu wiersza polecenia platformy Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4bd5c8ae7b4f2ba2d057f61712ce799814e19050
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202151"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Obsługa powiadomień dotyczących planowanej konserwacji przy użyciu interfejsu wiersza polecenia platformy Azure

**Ten artykuł ma zastosowanie do maszyn wirtualnych z systemami Linux i Windows.**

Możesz użyć interfejsu wiersza polecenia, aby zobaczyć, kiedy maszyny wirtualne są zaplanowane do [konserwacji](maintenance-notifications.md). Informacje o planowanej konserwacji są dostępne z [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view).
 
Informacje o konserwacji są zwracane tylko wtedy, gdy jest planowana konserwacja. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Dane wyjściowe
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Rozpocznij konserwację

Następujące wywołanie rozpocznie konserwację na maszynie wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` ma wartość true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Jeśli nadal masz starsze maszyny wirtualne wdrożone przy użyciu klasycznego modelu wdrażania, możesz użyć klasycznego interfejsu wiersza polecenia platformy Azure, aby wykonywać zapytania dotyczące maszyn wirtualnych i inicjować konserwację.

Upewnij się, że Pracujesz w prawidłowym trybie do pracy z klasyczną maszyną wirtualną, wpisując:

```
azure config mode asm
```

Aby uzyskać stan konserwacji maszyny wirtualnej o nazwie *myVM*, wpisz:

```
azure vm show myVM 
``` 

Aby rozpocząć konserwację klasycznej maszyny wirtualnej o nazwie *myVM* w usłudze *WebService* *i wdrożeniu wdrażania,* wpisz:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Następne kroki

Możesz również obsługiwać planowaną konserwację przy użyciu [Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md).
