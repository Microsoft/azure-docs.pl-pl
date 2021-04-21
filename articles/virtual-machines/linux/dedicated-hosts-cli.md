---
title: Wdrażanie maszyn wirtualnych i wystąpień zestawu skalowania na dedykowanych hostach przy użyciu interfejsu wiersza polecenia
description: Wdrażanie maszyn wirtualnych i wystąpień zestawu skalowania na dedykowanych hostach przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: adc09bf2572be563ff52cf9fa3d0dea51263d032
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774417"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Wdrażanie na dedykowanych hostach przy użyciu interfejsu wiersza polecenia platformy Azure
 

W tym artykule opisano sposób tworzenia dedykowanego hosta platformy [Azure](../dedicated-hosts.md) do hostowania maszyn wirtualnych. 

Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.16.0 lub nowszej i zalogowano się do konta platformy Azure przy użyciu polecenia `az login` . 


## <a name="limitations"></a>Ograniczenia

- Rozmiary i typy sprzętu dostępne dla dedykowanych hostów różnią się w zależności od regionu. Aby dowiedzieć się [więcej, zapoznaj](https://aka.ms/ADHPricing) się ze stroną cennika hosta.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów 
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą az group create. Poniższy przykład tworzy grupę zasobów o *nazwie myDHResourceGroup* w lokalizacji *Wschodnie usa.*

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Lista dostępnych jednostki SKU hosta w regionie

Nie wszystkie jednostki SKU hosta są dostępne we wszystkich regionach i strefach dostępności. 

Przed rozpoczęciem aprowizowania dedykowanych hostów należy wyświetlić listę dostępności hosta i wszelkich ograniczeń oferty. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Tworzenie grupy hostów 

Grupa **hostów** to zasób, który reprezentuje kolekcję dedykowanych hostów. Grupę hostów tworzy się w regionie i strefie dostępności, a następnie dodaje się do niego hosty. Podczas planowania wysokiej dostępności dostępne są dodatkowe opcje. W przypadku hostów dedykowanych można użyć jednej lub obu następujących opcji: 
- Obejmują wiele stref dostępności. W takim przypadku musisz mieć grupę hostów w każdej strefie, która ma być potrzebna.
- Obejmują wiele domen błędów, które są mapowane na stojaki fizyczne. 
 
W obu przypadkach należy podać liczbę domen błędów dla grupy hostów. Jeśli nie chcesz obejmować domen błędów w grupie, użyj liczby domen błędów 1. 

Można również zdecydować się na użycie stref dostępności i domen błędów. 


W tym przykładzie użyjemy narzędzia [az vm host group create,](/cli/azure/vm/host/group#az_vm_host_group_create) aby utworzyć grupę hostów przy użyciu stref dostępności i domen błędów. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Dodaj parametr , aby maszyny wirtualne i wystąpienia zestawu skalowania były automatycznie umieszczane `--automatic-placement true` na hostach w grupie hostów. Aby uzyskać więcej informacji, zobacz [Ręczne a automatyczne umieszczanie ](../dedicated-hosts.md#manual-vs-automatic-placement).


### <a name="other-examples"></a>Inne przykłady

Możesz również użyć az [vm host group create,](/cli/azure/vm/host/group#az_vm_host_group_create) aby utworzyć grupę hostów w strefie dostępności 1 (bez domen błędów).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
W poniższym przykładzie [użyto narzędzia az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) do utworzenia grupy hostów tylko przy użyciu domen błędów (do użycia w regionach, w których strefy dostępności nie są obsługiwane). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Tworzenie hosta 

Teraz utwórzmy dedykowanego hosta w grupie hostów. Oprócz nazwy hosta należy podać dla hosta jego nazwę SKU. SKU hosta przechwytuje obsługiwane serie maszyn wirtualnych, a także generację sprzętu dla dedykowanego hosta.  

Aby uzyskać więcej informacji na temat jednostki SKU hosta i cen, [zobacz Azure Dedicated Host cennik.](https://aka.ms/ADHPricing)

Użyj [az vm host create,](/cli/azure/vm/host#az_vm_host_create) aby utworzyć hosta. Jeśli ustawisz liczbę domen błędów dla grupy hostów, zostanie poproszony o określenie domeny błędów dla hosta.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną na dedykowanym hoście za pomocą [narzędzia az vm create](/cli/azure/vm#az_vm_create). Jeśli podczas tworzenia grupy hostów określono strefę dostępności, podczas tworzenia maszyny wirtualnej należy użyć tej samej strefy.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Aby umieścić maszynę wirtualną na określonym hoście, użyj zamiast określania grupy hostów za `--host` pomocą . `--host-group`
 
> [!WARNING]
> Jeśli utworzysz maszynę wirtualną na hoście, który nie ma wystarczającej ilości zasobów, maszyna wirtualna zostanie utworzona w stanie NIEPOWODZENIE. 

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania 

Podczas wdrażania zestawu skalowania należy określić grupę hostów.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Jeśli chcesz ręcznie wybrać hosta, na którym ma zostać wdrożony zestaw skalowania, dodaj i `--host` nazwę hosta.


## <a name="check-the-status-of-the-host"></a>Sprawdzanie stanu hosta

Stan kondycji hosta i ilu maszyn wirtualnych można nadal wdrożyć na hoście, można sprawdzić za pomocą narzędzia [az vm host get-instance-view.](/cli/azure/vm/host#az_vm_host_get_instance_view)

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Dane wyjściowe będą wyglądać podobnie do tych:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Eksportowanie jako szablon 
Szablon można wyeksportować, jeśli chcesz teraz utworzyć dodatkowe środowisko projektowe o tych samych parametrach lub środowisko produkcyjne, które go dopasowuje. Resource Manager używa szablonów JSON, które definiują wszystkie parametry dla środowiska. Możesz tworzyć całe środowiska, odwołując się do tego szablonu JSON. Możesz ręcznie utworzyć szablony JSON lub wyeksportować istniejące środowisko, aby utworzyć szablon JSON. Użyj [az group export,](/cli/azure/group#az_group_export) aby wyeksportować grupę zasobów.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

To polecenie tworzy `myDHResourceGroup.json` plik w bieżącym katalogu roboczy. Podczas tworzenia środowiska na podstawie tego szablonu zostanie wyświetlony monit o wszystkie nazwy zasobów. Możesz wypełnić te nazwy w pliku szablonu, dodając `--include-parameter-default-value` parametr do `az group export` polecenia . Edytuj szablon JSON, aby określić nazwy zasobów, lub utwórz parameters.jspliku, który określa nazwy zasobów.
 
Aby utworzyć środowisko na podstawie szablonu, użyj [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create).

```azurecli-interactive
az deployment group create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Czyszczenie 

Opłaty są naliczane za dedykowane hosty, nawet jeśli nie wdrożono żadnych maszyn wirtualnych. Aby zaoszczędzić koszty, usuń wszystkie hosty, których obecnie nie używasz.  

Hosta można usunąć tylko wtedy, gdy nie są już używane maszyny wirtualne. Usuń maszyny wirtualne za pomocą narzędzia [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Po usunięciu maszyn wirtualnych możesz usunąć hosta za pomocą narzędzia [az vm host delete](/cli/azure/vm/host#az_vm_host_delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Po usunięciu wszystkich hostów możesz usunąć grupę hostów za pomocą narzędzia [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Możesz również usunąć całą grupę zasobów za pomocą jednego polecenia. Spowoduje to usunięcie wszystkich zasobów utworzonych w grupie, w tym wszystkich maszyn wirtualnych, hostów i grup hostów.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz Omówienie [hostów dedykowanych.](../dedicated-hosts.md)

- Możesz również utworzyć dedykowane hosty przy użyciu [Azure Portal](../dedicated-hosts-portal.md).

- W tym miejscu znajduje [się](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)przykładowy szablon, który używa zarówno stref, jak i domen błędów w celu zapewnienia maksymalnej odporności w regionie.
