---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu wielu kart sieciowych
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux z wieloma kartami sieciowymi dołączoną przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonów Menedżer zasobów.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551814"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu wielu kart interfejsu sieciowego


W tym artykule szczegółowo opisano sposób tworzenia maszyny wirtualnej z wieloma kartami sieciowymi za pomocą interfejsu wiersza polecenia platformy Azure.

## <a name="create-supporting-resources"></a>Tworzenie zasobów pomocniczych
Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów dołączone do *zasobów*, *mojekontomagazynu* i *myVM*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Utwórz podsieć dla ruchu zaplecza za pomocą [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet). Poniższy przykład tworzy podsieć o nazwie *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Tworzenie i Konfigurowanie wielu kart sieciowych
Utwórz dwie karty sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic). Poniższy przykład tworzy dwie karty sieciowe o nazwie *myNic1* i *myNic2*, połączonej sieciowej grupy zabezpieczeń z jedną kartą sieciową łączącą się z każdą podsieć:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Tworzenie maszyny wirtualnej i dołączanie kart sieciowych
Podczas tworzenia maszyny wirtualnej należy określić karty sieciowe, które zostały utworzone za pomocą programu `--nics` . Należy również zadbać o to, aby wybrać rozmiar maszyny wirtualnej. Istnieją limity dla łącznej liczby kart sieciowych, które można dodać do maszyny wirtualnej. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux](../sizes.md).

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM*:

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

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w temacie [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Dodawanie karty sieciowej do maszyny wirtualnej
W poprzednich krokach utworzono maszynę wirtualną z wieloma kartami sieciowymi. Możesz również dodać karty sieciowe do istniejącej maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Różne [rozmiary maszyn wirtualnych](../sizes.md) obsługują różną liczbę kart sieciowych, dlatego należy odpowiednio zmienić rozmiar maszyny wirtualnej. W razie konieczności można [zmienić rozmiar maszyny wirtualnej](change-vm-size.md).

Utwórz kolejną kartę [sieciową za pomocą AZ Network nic Create](/cli/azure/network/nic). Poniższy przykład tworzy kartę sieciową o nazwie *myNic3* połączony z podsiecią zaplecza i sieciową grupą zabezpieczeń utworzoną w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę sieciową do istniejącej maszyny wirtualnej, najpierw Cofnij przydział maszyny wirtualnej za pomocą polecenie [AZ VM deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodaj kartę sieciową przy użyciu [AZ VM nic Add](/cli/azure/vm/nic). Poniższy przykład dodaje *myNic3* do *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę wirtualną za pomocą [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w temacie [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Usuwanie karty sieciowej z maszyny wirtualnej
Aby usunąć kartę sieciową z istniejącej maszyny wirtualnej, najpierw Cofnij przydział maszyny wirtualnej za pomocą polecenie [AZ VM deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń kartę sieciową za pomocą [AZ VM nic Remove](/cli/azure/vm/nic). Poniższy przykład usuwa *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę wirtualną za pomocą [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Tworzenie wielu kart sieciowych przy użyciu szablonów Menedżer zasobów
Szablony Azure Resource Manager używają deklaratywnych plików JSON do definiowania środowiska. Możesz zapoznać się [z omówieniem Azure Resource Manager](../../azure-resource-manager/management/overview.md). Szablony Menedżer zasobów umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu kart sieciowych. Użyj *Kopiuj* , aby określić liczbę wystąpień do utworzenia:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Przeczytaj więcej na temat [tworzenia wielu wystąpień przy użyciu *kopii*](../../azure-resource-manager/templates/copy-resources.md). 

Można również użyć `copyIndex()` do dołączenia liczby do nazwy zasobu, co pozwala na tworzenie `myNic1` , `myNic2` itp. Poniżej przedstawiono przykład dołączania wartości indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Możesz zapoznać się z kompletnym przykładem [tworzenia wielu kart sieciowych przy użyciu szablonów Menedżer zasobów](../../virtual-network/template-samples.md).

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w temacie [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

W poprzednich krokach utworzono sieć wirtualną i podsieć, dołączone karty sieciowe, a następnie utworzono maszynę wirtualną. Nie utworzono żadnych reguł publicznego adresu IP i sieciowej grupy zabezpieczeń, które zezwalają na ruch SSH. Aby skonfigurować system operacyjny gościa dla wielu kart sieciowych, musisz zezwolić na połączenia zdalne i uruchamiać polecenia lokalnie na maszynie wirtualnej.

Aby zezwolić na ruch SSH, Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) w następujący sposób:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Utwórz publiczny adres IP za pomocą [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) i przypisz go do pierwszej karty sieciowej za pomocą [AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Aby wyświetlić publiczny adres IP maszyny wirtualnej, użyj polecenie [AZ VM show](/cli/azure/vm#az-vm-show) w następujący sposób::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teraz SSH z publicznym adresem IP maszyny wirtualnej. Domyślna nazwa użytkownika podana w poprzednim kroku została *azureuser*. Podaj własną nazwę użytkownika i publiczny adres IP:

```bash
ssh azureuser@137.117.58.232
```

Aby wysłać do lub z pomocniczego interfejsu sieciowego, należy ręcznie dodać trasy trwałe do systemu operacyjnego dla każdego pomocniczego interfejsu sieciowego. W tym artykule *eth1* jest interfejsem pomocniczym. Instrukcje dotyczące dodawania tras stałych do systemu operacyjnego różnią się w zależności od dystrybucji. Aby uzyskać instrukcje, zobacz dokumentację dotyczącą usługi dystrybucji.

W przypadku dodawania trasy do systemu operacyjnego adres bramy jest w przypadku każdej podsieci, w której *znajduje się interfejs* sieciowy. Na przykład jeśli do interfejsu sieciowego przypisano adres *10.0.2.4*, Brama określona dla trasy to *10.0.2.1*. Można zdefiniować konkretną sieć dla docelowej trasy lub określić miejsce docelowe o wartości *0.0.0.0*, jeśli chcesz, aby cały ruch interfejsu przeszedł przez określoną bramę. Brama dla każdej podsieci jest zarządzana przez sieć wirtualną.

Po dodaniu trasy dla interfejsu pomocniczego upewnij się, że trasa znajduje się w tabeli tras `route -n` . Następujące przykładowe dane wyjściowe dotyczą tabeli tras, która ma dwa interfejsy sieciowe dodawane do maszyny wirtualnej w tym artykule:

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

Upewnij się, że dodana trasa będzie trwała w ponownym uruchomieniu, sprawdzając tabelę tras ponownie po ponownym uruchomieniu. Aby przetestować łączność, możesz wprowadzić następujące polecenie, na przykład, gdzie *eth1* jest nazwą pomocniczego interfejsu sieciowego:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Następne kroki
Sprawdź [rozmiary maszyn wirtualnych z systemem Linux](../sizes.md) podczas próby utworzenia maszyny wirtualnej z wieloma kartami sieciowymi. Zwróć uwagę na maksymalną liczbę kart sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych.

Aby dodatkowo zabezpieczyć maszyny wirtualne, użyj dostępu just in Time do maszyny wirtualnej. Ta funkcja otwiera reguły sieciowej grupy zabezpieczeń dla ruchu SSH w razie potrzeby i przez określony czas. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).