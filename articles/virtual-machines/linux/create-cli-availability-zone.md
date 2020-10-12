---
title: Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Tworzenie maszyny wirtualnej z systemem Linux w strefie dostępności przy użyciu interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 964d2b5d89fd91aca68e9b47b0049529fe284848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829003"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Tworzenie maszyny wirtualnej z systemem Linux w strefie dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano, jak utworzyć maszynę wirtualną z systemem Linux w strefie dostępności platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-region.md).

Upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się na koncie platformy Azure przy użyciu [AZ login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Sprawdzanie dostępności jednostki SKU maszyny wirtualnej
Dostępność rozmiarów maszyn wirtualnych lub jednostek SKU może różnić się w zależności od regionu i strefy. Aby uzyskać pomoc w planowaniu użycia stref dostępności, możesz wyświetlić listę dostępnych jednostek SKU maszyn wirtualnych według regionu i strefy świadczenia usługi Azure. Ta możliwość gwarantuje wybranie odpowiedniego rozmiaru maszyny wirtualnej oraz uzyskanie żądanej odporności w różnych strefach. Aby uzyskać więcej informacji o różnych typach i rozmiarach maszyn wirtualnych, zobacz [Omówienie rozmiarów maszyn wirtualnych](../sizes.md).

Dostępne jednostki SKU maszyny wirtualnej można wyświetlić za pomocą polecenia [AZ VM list-SKU](/cli/azure/vm) . Następujący przykład zawiera listę dostępnych jednostek SKU maszyn wirtualnych w regionie *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Rezultat jest podobny do poniższego, skróconego przykładu, który pokazuje strefy dostępności, w których dostępne są poszczególne rozmiary maszyn wirtualnych:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).  

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. W tym przykładzie zostanie utworzona grupa zasobów o nazwie *myResourceGroupVM* w regionie *eastus2* . Wschodnie stany USA 2 to jeden z regionów świadczenia usługi Azure, które obsługują strefy dostępności.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Grupa zasobów jest określana podczas tworzenia lub modyfikowania maszyny wirtualnej, która może być widoczna w tym artykule.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Podczas tworzenia maszyny wirtualnej masz dostęp do kilku opcji, takich jak obraz systemu operacyjnego, ustalanie rozmiaru dysku i poświadczenia administracyjne. W tym przykładzie tworzona jest maszyna wirtualna o nazwie *myVM* z systemem Ubuntu Server. Maszyna wirtualna jest tworzona w obszarze strefa dostępności *1*. Domyślnie maszyna wirtualna jest tworzona w rozmiarze *Standard_DS1_v2* .

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Tworzenie maszyny wirtualnej może potrwać kilka minut. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetla informacje o maszynie wirtualnej. Zwróć uwagę na `zones` wartość wskazującą strefę dostępności, w której działa maszyna wirtualna. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdź strefę dla dysku zarządzanego i adresu IP

Po wdrożeniu maszyny wirtualnej w strefie dostępności dysk zarządzany dla maszyny wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie publiczny adres IP jest również tworzony w tej strefie. Poniższe przykłady pobierają informacje o tych zasobach.

Aby sprawdzić, czy dysk zarządzany maszyny wirtualnej znajduje się w strefie dostępności, użyj polecenia [AZ VM show](/cli/azure/vm) do zwrócenia identyfikatora dysku. W tym przykładzie identyfikator dysku jest przechowywany w zmiennej, która jest używana w późniejszym kroku. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Teraz możesz uzyskać informacje na temat dysku zarządzanego:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Dane wyjściowe pokazują, że dysk zarządzany znajduje się w tej samej strefie dostępności co maszyna wirtualna:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Użyj polecenia [AZ VM list-IP-addresss](/cli/azure/vm) , aby zwrócić nazwę zasobu publicznego adresu IP w *myVM*. W tym przykładzie nazwa jest przechowywana w zmiennej, która jest używana w późniejszym kroku.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Teraz możesz uzyskać informacje o adresie IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Dane wyjściowe wskazują, że adres IP znajduje się w tej samej strefie dostępności co maszyna wirtualna:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [dostępności](../availability.md) dla maszyn wirtualnych platformy Azure.