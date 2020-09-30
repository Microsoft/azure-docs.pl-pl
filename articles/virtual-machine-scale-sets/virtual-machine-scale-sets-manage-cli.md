---
title: Zarządzanie Virtual Machine Scale Sets przy użyciu interfejsu wiersza polecenia platformy Azure
description: Typowe polecenia platformy Azure umożliwiające zarządzanie Virtual Machine Scale Sets, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie pojemności zestawu skalowania.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570528"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zarządzanie zestawem skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł zawiera szczegółowe informacje na temat typowych poleceń interfejsu wiersza polecenia platformy Azure, które umożliwiają wykonywanie tych zadań.

Do wykonania tych zadań zarządzania potrzebny jest najnowszy interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli musisz utworzyć zestaw skalowania maszyn wirtualnych, możesz [utworzyć zestaw skalowania za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Wyświetlanie informacji o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj [AZ VMSS show](/cli/azure/vmss). Poniższy przykład pobiera informacje o zestawie skalowania o nazwie *myScaleSet* w grupie *zasobów zasobu* . Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj [AZ VMSS list-Instances](/cli/azure/vmss). Poniższy przykład wyświetla listę wszystkich wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* w *grupie zasobów zasobu* . Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Aby wyświetlić dodatkowe informacje dotyczące określonego wystąpienia maszyny wirtualnej, Dodaj `--instance-id` parametr do [AZ VMSS Get-instance-View](/cli/azure/vmss) i Określ wystąpienie do wyświetlenia. Poniższy przykład wyświetla informacje o wystąpieniu maszyny wirtualnej *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Można również uzyskać szczegółowe informacje *instanceView* dla wszystkich wystąpień w jednym wywołaniu interfejsu API, co może pomóc w uniknięciu ograniczenia przepustowości interfejsu API w przypadku dużych instalacji. Podaj własne wartości dla `--resource-group` , `--subscription` i `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Wyświetl informacje o połączeniu dla maszyn wirtualnych
Aby nawiązać połączenie z maszynami wirtualnymi w zestawie skalowania, Użyj protokołu SSH lub RDP do przypisanego publicznego adresu IP i numeru portu. Domyślnie reguły translacji adresów sieciowych (NAT) są dodawane do modułu równoważenia obciążenia platformy Azure, który przekazuje ruch połączeń zdalnych do każdej maszyny wirtualnej. Aby wyświetlić listę adresów i portów, które mają być połączone z wystąpieniami maszyn wirtualnych w zestawie skalowania, użyj [AZ VMSS list-instance-Connection-Info](/cli/azure/vmss). Poniższy przykład zawiera listę informacji o połączeniu dla wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* i w *grupie zasobów zasobu* . Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Powyższe polecenia pokazały informacje o zestawie skalowania i wystąpieniach maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszyny wirtualne pod kątem odbierania ruchu aplikacji.

Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i zapytania *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss). W poniższym przykładzie liczba maszyn wirtualnych w zestawie skalowania jest ustawiana na *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszy się pojemność zestawu skalowania, maszyny wirtualne z najwyższymi identyfikatorami wystąpienia zostaną najpierw usunięte.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AZ VMSS Stop](/cli/azure/vmss#az-vmss-stop). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

Poniższy przykład powoduje zatrzymanie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zatrzymane maszyny wirtualne pozostają przydzieloną i nadal będą naliczane opłaty za zasoby obliczeniowe. Jeśli zamiast tego chcesz cofnąć alokację maszyn wirtualnych i ponieść tylko opłaty za magazyn, użyj polecenia [AZ VMSS deallocate](/cli/azure/vmss). Aby cofnąć alokację wielu maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją. Poniższy przykład powoduje zatrzymanie i cofnięcie przydziału wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Uruchamianie maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AZ VMSS Start](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby rozpocząć pracę z wieloma maszynami wirtualnymi, należy oddzielić każdy identyfikator wystąpienia spacją.

W poniższym przykładzie zostanie uruchomione wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AZ VMSS restart](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

Poniższy przykład powoduje ponowne uruchomienie wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i *grupie zasobów zasobu* . Podaj własne wartości w następujący sposób:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych z zestawu skalowania
Aby usunąć co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [AZ VMSS Delete-Instances](/cli/azure/vmss). `--instance-ids`Parametr umożliwia określenie co najmniej jednej maszyny wirtualnej do usunięcia. Jeśli określisz * dla identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, należy oddzielić każdy identyfikator wystąpienia spacją.

Poniższy przykład usuwa wystąpienie *0* z zestawu skalowania o nazwie *myScaleSet* i grupę *zasobów zasobu* . Podaj własne wartości w następujący sposób:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Następne kroki
Inne typowe zadania związane z zestawami skalowania obejmują sposób [wdrażania aplikacji](virtual-machine-scale-sets-deploy-app.md)i [uaktualniania wystąpień maszyn wirtualnych](virtual-machine-scale-sets-upgrade-scale-set.md). Aby [skonfigurować reguły automatycznego skalowania](virtual-machine-scale-sets-autoscale-overview.md), można również użyć interfejsu wiersza polecenia platformy Azure.
