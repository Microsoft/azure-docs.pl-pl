---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure z wieloma karty sieciowe
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux z wieloma dołączonymi do niej karty sieciowe przy użyciu interfejsu wiersza polecenia platformy Azure Resource Manager szablonów.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b08e8ebbba3ba91c1c1aa0f135c4cba37ba038b1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769917"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu wielu kart sieciowych


W tym artykule szczegółowo opisano sposób tworzenia maszyny wirtualnej z wieloma karty sieciowe za pomocą interfejsu wiersza polecenia platformy Azure.

## <a name="create-supporting-resources"></a>Tworzenie zasobów pomocy technicznej
Zainstaluj najnowszy interfejs wiersza [polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do konta platformy Azure przy użyciu polecenia az [login](/cli/azure/reference-index).

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów *obejmowały myResourceGroup,* *mystorageaccount* i *myVM.*

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i *podsieć o nazwie mySubnetFrontEnd:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Utwórz podsieć dla ruchu za pomocą az [network vnet subnet create](/cli/azure/network/vnet/subnet). Poniższy przykład tworzy podsieć o *nazwie mySubnetBackEnd:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Utwórz sieciową grupę zabezpieczeń za pomocą [az network nsg create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Tworzenie i konfigurowanie wielu karty sieciowe
Utwórz dwie karty sieciowe za pomocą [az network nic create](/cli/azure/network/nic). Poniższy przykład tworzy dwie karty sieciowe o nazwach *myNic1* i *myNic2*, połączone sieciową grupą zabezpieczeń z jedną kartą sieciową łączącą się z każdą podsiecią:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Tworzenie maszyny wirtualnej i dołączanie karty sieciowe
Podczas tworzenia maszyny wirtualnej określ karty sieciowe utworzone za pomocą `--nics` . Należy również zadbać o to po wybraniu rozmiaru maszyny wirtualnej. Istnieją limity całkowitej liczby karty sieciowej, które można dodać do maszyny wirtualnej. Przeczytaj więcej na temat rozmiarów [maszyn wirtualnych z systemem Linux.](../sizes.md)

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną *o nazwie myVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w tece Konfigurowanie systemu operacyjnego gościa [dla wielu karty sieciowe.](#configure-guest-os-for-multiple-nics)

## <a name="add-a-nic-to-a-vm"></a>Dodawanie karty sieciowej do maszyny wirtualnej
W poprzednich krokach utworzono maszynę wirtualną z wieloma karty sieciowe. Karty sieciowe można również dodać do istniejącej maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure. Różne [rozmiary maszyn wirtualnych](../sizes.md) obsługują różną liczbę karty sieciowej, dlatego należy odpowiednio rozmiarować maszynę wirtualną. W razie potrzeby możesz zmienić [rozmiar maszyny wirtualnej.](change-vm-size.md)

Utwórz kolejną kartę sieciową za pomocą [az network nic create.](/cli/azure/network/nic) W poniższym przykładzie tworzona jest karta sieciowa o nazwie *myNic3* połączona z podsiecią wewnętrznej sieci i sieciową grupą zabezpieczeń utworzoną w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę sieciową do istniejącej maszyny wirtualnej, najpierw cofń jej alokację za pomocą [az vm deallocate](/cli/azure/vm). W poniższym przykładzie cofniesz alokację maszyny wirtualnej o *nazwie myVM:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodaj kartę sieciową za pomocą [az vm nic add](/cli/azure/vm/nic). W poniższym przykładzie *dodano myNic3* do *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę wirtualną za pomocą [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w tece Konfigurowanie systemu operacyjnego gościa [dla wielu karty sieciowe.](#configure-guest-os-for-multiple-nics)

## <a name="remove-a-nic-from-a-vm"></a>Usuwanie karty sieciowej z maszyny wirtualnej
Aby usunąć kartę sieciową z istniejącej maszyny wirtualnej, najpierw cofń jej alokację za pomocą [az vm deallocate](/cli/azure/vm). W poniższym przykładzie cofniesz alokację maszyny wirtualnej o *nazwie myVM:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń kartę sieciową za pomocą [az vm nic remove](/cli/azure/vm/nic). Poniższy przykład usuwa *myNic3* z *myVM:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę wirtualną za pomocą [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Tworzenie wielu karty sieciowe przy użyciu Resource Manager szablonów
Azure Resource Manager szablony definiują środowisko przy użyciu deklaratywnych plików JSON. Możesz zapoznać się z [omówieniem Azure Resource Manager](../../azure-resource-manager/management/overview.md). Resource Manager szablony zapewniają sposób tworzenia wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu karty sieciowe. Użyj funkcji *kopiowania,* aby określić liczbę wystąpień do utworzenia:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Przeczytaj więcej na [temat tworzenia wielu wystąpień przy użyciu *kopii*](../../azure-resource-manager/templates/copy-resources.md). 

Możesz również użyć funkcji , aby następnie dołączyć liczbę do nazwy zasobu, co pozwala utworzyć `copyIndex()` `myNic1` , `myNic2` itp. Poniżej przedstawiono przykład dołączania wartości indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Możesz zapoznać się z kompletnym przykładem tworzenia [wielu karty sieciowych przy użyciu Resource Manager szablonów.](../../virtual-network/template-samples.md)

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w te [tematu Configure the guest OS for multiple NICs](#configure-guest-os-for-multiple-nics)(Konfigurowanie systemu operacyjnego gościa dla wielu karty sieciowych).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu karty sieciowe

W poprzednich krokach utworzono sieć wirtualną i podsieć, dołączono karty sieciowe, a następnie utworzono maszynę wirtualną. Nie utworzono publicznego adresu IP i reguł sieciowej grupy zabezpieczeń, które zezwalają na ruch SSH. Aby skonfigurować system operacyjny gościa dla wielu karty sieciowe, należy zezwolić na połączenia zdalne i uruchomić polecenia lokalnie na maszynie wirtualnej.

Aby zezwolić na ruch SSH, utwórz regułę sieciowej grupy zabezpieczeń za pomocą [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) w następujący sposób:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Utwórz publiczny adres IP za pomocą az [network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) i przypisz go do pierwszej karty sieciowej za pomocą az [network nic ip-config update:](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Aby wyświetlić publiczny adres IP maszyny wirtualnej, użyj [az vm show](/cli/azure/vm#az_vm_show) w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teraz za pomocą protokołu SSH nakieruj na publiczny adres IP maszyny wirtualnej. Domyślną nazwą użytkownika podaną w poprzednim kroku był *azureuser*. Podaj własną nazwę użytkownika i publiczny adres IP:

```bash
ssh azureuser@137.117.58.232
```

Aby wysyłać do lub z pomocniczego interfejsu sieciowego, należy ręcznie dodać trasy trwałe do systemu operacyjnego dla każdego pomocniczego interfejsu sieciowego. W tym artykule *eth1* jest interfejsem pomocniczym. Instrukcje dotyczące dodawania trwałych tras do systemu operacyjnego różnią się w zależności od dystrybucji. Instrukcje można znaleźć w dokumentacji dystrybucji.

Podczas dodawania trasy do systemu operacyjnego adres bramy to *.1* dla podsieci, w której znajduje się interfejs sieciowy. Jeśli na przykład interfejs sieciowy ma przypisany adres *10.0.2.4,* brama określana dla trasy to *10.0.2.1.* Możesz zdefiniować określoną sieć dla miejsca docelowego trasy lub określić lokalizację docelową *0.0.0.0,* jeśli chcesz, aby cały ruch dla interfejsu przechodził przez określoną bramę. Brama dla każdej podsieci jest zarządzana przez sieć wirtualną.

Po dodaniu trasy dla interfejsu pomocniczego sprawdź, czy trasa znajduje się w tabeli tras za `route -n` pomocą . Następujące przykładowe dane wyjściowe są dla tabeli tras, która ma dwa interfejsy sieciowe dodane do maszyny wirtualnej w tym artykule:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Upewnij się, że dodana trasa jest zachowywana po ponownym uruchomieniu, ponownie sprawdzając tabelę tras po ponownym uruchomieniu. Aby przetestować łączność, możesz wprowadzić następujące polecenie, na przykład gdzie *eth1* jest nazwą pomocniczego interfejsu sieciowego:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Następne kroki
Przejrzyj [rozmiary maszyn wirtualnych z systemem Linux](../sizes.md) podczas próby utworzenia maszyny wirtualnej z wieloma karty sieciowe. Zwróć uwagę na maksymalną liczbę kart sieciowych, które obsługuje każdy rozmiar maszyny wirtualnej.

Aby dodatkowo zabezpieczyć maszyny wirtualne, użyj dostępu just in time do maszyny wirtualnej. Ta funkcja otwiera reguły sieciowej grupy zabezpieczeń dla ruchu SSH w razie potrzeby i przez zdefiniowany czas. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).