---
title: Zarządzanie Virtual Machine Scale Sets za pomocą interfejsu wiersza polecenia platformy Azure
description: Typowe polecenia interfejsu wiersza polecenia platformy Azure Virtual Machine Scale Sets, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie pojemności zestawu skalowania.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9c2b2217fc6b32e5191bb67ffdaa10b796adf84b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762771"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zarządzanie zestawem skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ponadto może pojawić się potrzeba tworzenia skryptów automatyzujących różne zadania cyklu życia. Ten artykuł zawiera szczegółowe informacje o niektórych typowych poleceniach interfejsu wiersza polecenia platformy Azure, które umożliwiają wykonywanie tych zadań.

Do wykonania tych zadań zarządzania jest potrzebny najnowszy interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Jeśli musisz utworzyć zestaw skalowania maszyn wirtualnych, możesz utworzyć zestaw skalowania za pomocą interfejsu [wiersza polecenia platformy Azure.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Wyświetlanie informacji o zestawie skalowania
Aby wyświetlić ogólne informacje o zestawie skalowania, użyj [az vmss show](/cli/azure/vmss). Poniższy przykład pobiera informacje o zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *myResourceGroup.* Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Wyświetlanie maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj [az vmss list-instances](/cli/azure/vmss). Poniższy przykład zawiera listę wszystkich wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* w grupie zasobów *myResourceGroup.* Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Aby wyświetlić dodatkowe informacje o konkretnym wystąpieniu maszyny wirtualnej, dodaj parametr do az `--instance-id` [vmss get-instance-view](/cli/azure/vmss) i określ wystąpienie do wyświetlenia. W poniższym przykładzie przedstawiono informacje o wystąpieniu *maszyny wirtualnej 0* w zestawie skalowania o nazwach *myScaleSet* i grupie zasobów *myResourceGroup.* Wprowadź własne nazwy w następujący sposób:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Możesz również uzyskać szczegółowe informacje *instanceView* dla wszystkich wystąpień w jednym wywołaniu interfejsu API, co może pomóc uniknąć ograniczania przepustowości interfejsu API w przypadku dużych instalacji. Podaj własne wartości dla `--resource-group` `--subscription` , i `--name` .

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

## <a name="list-connection-information-for-vms"></a>Lista informacji o połączeniu dla maszyn wirtualnych
Aby nawiązać połączenie z maszynami wirtualnych w zestawie skalowania, należy nawiązać połączenie SSH lub RDP z przypisanym publicznym adresem IP i numerem portu. Domyślnie reguły translatora adresów sieciowych (NAT) są dodawane do usługi równoważenia obciążenia platformy Azure, która przekazuje ruch połączeń zdalnych do każdej maszyny wirtualnej. Aby wyświetlić listę adresów i portów w celu nawiązania połączenia z wystąpieniami maszyn wirtualnych w zestawie skalowania, użyj [az vmss list-instance-connection-info](/cli/azure/vmss). Poniższy przykład zawiera listę informacji o połączeniu dla wystąpień maszyn wirtualnych w zestawie skalowania o nazwie *myScaleSet* i w grupie zasobów *myResourceGroup.* Podaj własne wartości dla tych nazw:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Poprzednie polecenia pokazywały informacje o zestawie skalowania i wystąpieniach maszyn wirtualnych. Aby zwiększyć lub zmniejszyć liczbę wystąpień w zestawie skalowania, można zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę maszyn wirtualnych, a następnie konfiguruje maszyny wirtualne do odbierania ruchu aplikacji.

Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i zapytania *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Następnie możesz ręcznie zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania za pomocą polecenia [az vmss scale](/cli/azure/vmss). W poniższym przykładzie liczba maszyn wirtualnych w zestawie skalowania jest ustawiana na *5:*

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Jeśli zmniejszysz pojemność zestawu skalowania, najpierw zostaną usunięte maszyny wirtualne o najwyższym identyfikatorze wystąpienia.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Zatrzymywanie i uruchamianie maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss stop](/cli/azure/vmss#az_vmss_stop). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie maszyny wirtualne w zestawie skalowania. Aby zatrzymać wiele maszyn wirtualnych, należy oddzielić identyfikator każdego wystąpienia spacją.

Poniższy przykład zatrzymuje wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Zatrzymane maszyny wirtualne pozostają przydzielone i nadal są naliczane opłaty za obliczenia. Jeśli zamiast tego chcesz cofnić alokację maszyn wirtualnych i ponosić tylko opłaty za magazyn, użyj [az vmss deallocate](/cli/azure/vmss). Aby cofnić alokację wielu maszyn wirtualnych, należy oddzielić identyfikator każdego wystąpienia spacją. Poniższy przykład zatrzymuje i cofa przydział wystąpienia *0* w zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Uruchamianie maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss start](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie maszyny wirtualne w zestawie skalowania. Aby uruchomić wiele maszyn wirtualnych, należy oddzielić identyfikator każdego wystąpienia spacją.

Poniższy przykład uruchamia wystąpienie *0 w* zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Ponowne uruchamianie maszyn wirtualnych w zestawie skalowania
Aby ponownie uruchomić co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss restart](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną uruchomione ponownie. Aby ponownie uruchomić wiele maszyn wirtualnych, należy oddzielić identyfikator każdego wystąpienia spacją.

Poniższy przykład ponownie uruchamia wystąpienie *0 w* zestawie skalowania o nazwie *myScaleSet* i grupie zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Usuwanie maszyn wirtualnych z zestawu skalowania
Aby usunąć co najmniej jedną maszynę wirtualną w zestawie skalowania, użyj [az vmss delete-instances](/cli/azure/vmss). Parametr umożliwia określenie co najmniej jednej maszyny `--instance-ids` wirtualnej do usunięcia. Jeśli określisz * dla identyfikatora wystąpienia, wszystkie maszyny wirtualne w zestawie skalowania zostaną usunięte. Aby usunąć wiele maszyn wirtualnych, należy oddzielić identyfikator każdego wystąpienia spacją.

Poniższy przykład usuwa wystąpienie *0* w zestawie skalowania o nazwie *myScaleSet* i grupę zasobów *myResourceGroup.* Podaj własne wartości w następujący sposób:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Następne kroki
Inne typowe zadania dotyczące zestawów skalowania obejmują [wdrażanie aplikacji](virtual-machine-scale-sets-deploy-app.md)i [uaktualnianie wystąpień maszyn wirtualnych.](virtual-machine-scale-sets-upgrade-scale-set.md) Do konfigurowania reguł skalowania automatycznego można również użyć [interfejsu wiersza polecenia platformy Azure.](virtual-machine-scale-sets-autoscale-overview.md)
