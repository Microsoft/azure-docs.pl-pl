---
title: Uzyskiwanie powiadomień dotyczących konserwacji przy użyciu interfejsu wiersza polecenia
description: Wyświetlanie powiadomień o konserwacji maszyn wirtualnych działających na platformie Azure i uruchamianie konserwacji samoobsługowej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777873"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Obsługa powiadomień dotyczących planowanej konserwacji przy użyciu interfejsu wiersza polecenia platformy Azure

**Ten artykuł dotyczy maszyn wirtualnych z systemami Linux i Windows.**

Możesz użyć interfejsu wiersza polecenia, aby sprawdzić, kiedy zaplanowano konserwację maszyn [wirtualnych.](maintenance-notifications.md) Informacje o planowanej konserwacji są dostępne za pomocą widoku [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view).
 
Informacje o konserwacji są zwracane tylko w przypadku planowanej konserwacji. 

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

## <a name="start-maintenance"></a>Rozpoczynanie konserwacji

Następujące wywołanie spowoduje rozpoczęcie konserwacji maszyny wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` ustawiono wartość true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasyczne wdrożenia

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Jeśli nadal masz starsze maszyny wirtualne, które zostały wdrożone przy użyciu klasycznego modelu wdrażania, możesz użyć klasycznego interfejsu wiersza polecenia platformy Azure do wykonywania zapytań o maszyny wirtualne i inicjowania konserwacji.

Upewnij się, że jesteś w prawidłowym trybie, aby pracować z klasyczną maszyną wirtualną, wpisując:

```
azure config mode asm
```

Aby uzyskać stan konserwacji maszyny wirtualnej o nazwie *myVM,* wpisz:

```
azure vm show myVM 
``` 

Aby rozpocząć konserwację klasycznej maszyny wirtualnej o nazwie *myVM* we wdrożeniu *usługi myService* i *wdrożenia myDeployment,* wpisz:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Następne kroki

Planowaną konserwację można również obsługiwać przy [użyciu Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu.](maintenance-notifications-portal.md)
