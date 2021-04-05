---
title: Używanie wewnętrznego serwera DNS do rozpoznawania nazw maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak utworzyć karty interfejsu sieci wirtualnej i używać wewnętrznego serwera DNS do rozpoznawania nazw maszyn wirtualnych na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 3d68ac7aa9927e62011c58b17139d7232ce4a10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200757"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Tworzenie kart interfejsu sieci wirtualnej i używanie wewnętrznego serwera DNS do rozpoznawania nazw maszyn wirtualnych na platformie Azure

W tym artykule opisano sposób ustawiania statycznych wewnętrznych nazw DNS dla maszyn wirtualnych z systemem Linux przy użyciu kart interfejsu sieci wirtualnej (vNics) i nazw etykiet DNS w interfejsie wiersza polecenia platformy Azure. Statyczne nazwy DNS są używane w przypadku trwałych usług infrastruktury, takich jak serwer kompilacji Jenkins, który jest używany dla tego dokumentu lub serwera git.

Wymagania są następujące:

* [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Szybkie polecenia
Aby szybko wykonać zadanie, w poniższej sekcji znajdują się szczegółowe informacje dotyczące wymaganych poleceń. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, [Zaczynając od tego miejsca](#detailed-walkthrough). Aby wykonać te kroki, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

Wymagania wstępne: Grupa zasobów, Sieć wirtualna i podsieć, sieciowa Grupa zabezpieczeń z protokołem SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Tworzenie karty interfejsu sieci wirtualnej ze statyczną wewnętrzną nazwą DNS
Utwórz wirtualnej karty sieciowej za pomocą [AZ Network nic Create](/cli/azure/network/nic). `--internal-dns-name`Flaga interfejsu wiersza polecenia służy do ustawiania etykiety DNS, która zapewnia statyczną nazwę DNS karty interfejsu sieci wirtualnej (wirtualnej karty sieciowej). Poniższy przykład tworzy wirtualnej karty sieciowej o nazwie `myNic` , łączy go z `myVnet` siecią wirtualną i tworzy wewnętrzny rekord nazwy DNS o nazwie `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Wdróż maszynę wirtualną i Połącz wirtualnej karty sieciowej
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). `--nics`Flaga łączy wirtualnej karty sieciowej z maszyną wirtualną podczas wdrażania na platformie Azure. Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` przy użyciu usługi Azure Managed disks i dołącza wirtualnej karty sieciowej o nazwie `myNic` z poprzedniego kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Infrastruktura z pełną integracją ciągłej integracji i ciągłego wdrażania (CiCd) na platformie Azure wymaga, aby niektóre serwery były serwerami statycznymi lub długotrwałymi. Zaleca się, aby zasoby platformy Azure, takie jak sieci wirtualne i sieciowe grupy zabezpieczeń, były zasobami statycznymi i długotrwałymi, które są rzadko wdrażane. Po wdrożeniu sieci wirtualnej może ona być ponownie używana przez nowe wdrożenia bez żadnego niekorzystnego wpływu na infrastrukturę. Później można dodać serwer repozytorium Git lub serwer Jenkins Automation CiCd do tej sieci wirtualnej w środowiskach programistycznych lub testowych.  

Wewnętrzne nazwy DNS są rozpoznawane tylko w sieci wirtualnej platformy Azure. Ponieważ nazwy DNS są wewnętrzne, nie można ich rozpoznać do spoza Internetu, zapewniając dodatkowe zabezpieczenia infrastruktury.

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmują `myResourceGroup` , `myNic` , i `myVM` .

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów
Najpierw utwórz grupę zasobów za pomocą polecenie [AZ Group Create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Następnym krokiem jest skompilowanie sieci wirtualnej w celu uruchomienia maszyn wirtualnych. Sieć wirtualna zawiera jedną podsieć dla tego przewodnika. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnej](../../virtual-network/manage-virtual-network.md#create-a-virtual-network). 

Utwórz sieć wirtualną za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` i podsieci o nazwie `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Sieciowe grupy zabezpieczeń platformy Azure są równoważne zaporze w warstwie sieciowej. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [jak utworzyć sieciowych grup zabezpieczeń w interfejsie wiersza polecenia platformy Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md). 

Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Dodawanie reguły ruchu przychodzącego zezwalającej na używanie protokołu SSH
Dodaj regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę o nazwie `myRuleAllowSSH` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Kojarzenie podsieci z grupą zabezpieczeń sieci
Aby skojarzyć podsieć z grupą zabezpieczeń sieci, użyj polecenie [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet). Poniższy przykład kojarzy nazwę podsieci `mySubnet` z grupą zabezpieczeń sieci o nazwie `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Tworzenie karty interfejsu sieci wirtualnej i statycznych nazw DNS
System Azure jest bardzo elastyczny, ale aby używać nazw DNS do rozpoznawania nazw maszyn wirtualnych, należy utworzyć karty interfejsu sieci wirtualnej (vNics), które zawierają etykietę DNS. vNics są ważne, ponieważ można je ponownie wykorzystać, łącząc je z różnymi maszynami wirtualnymi za pośrednictwem cyklu życia infrastruktury. Takie podejście utrzymuje wirtualnej karty sieciowej jako zasób statyczny, a maszyny wirtualne mogą być tymczasowe. Korzystając z etykiet DNS na wirtualnej karty sieciowej, możemy włączyć proste rozpoznawanie nazw z innych maszyn wirtualnych w sieci wirtualnej. Użycie nazw rozpoznawalnych umożliwia innym maszynom wirtualnym dostęp do serwera automatyzacji przez nazwę DNS `Jenkins` lub serwer git jako `gitrepo` .  

Utwórz wirtualnej karty sieciowej za pomocą [AZ Network nic Create](/cli/azure/network/nic). Poniższy przykład tworzy wirtualnej karty sieciowej o nazwie `myNic` , łączy go z `myVnet` siecią wirtualną o nazwie `myVnet` i tworzy wewnętrzny rekord nazwy DNS o nazwie `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Wdróż MASZYNę wirtualną w infrastrukturze sieci wirtualnej
Mamy już sieć wirtualną i podsieć, czyli grupę zabezpieczeń sieci działającą jako Zapora do ochrony naszej podsieci, blokując cały ruch przychodzący z wyjątkiem portu 22 dla protokołu SSH i wirtualnej karty sieciowej. Teraz można wdrożyć maszynę wirtualną w ramach istniejącej infrastruktury sieciowej.

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` przy użyciu usługi Azure Managed disks i dołącza wirtualnej karty sieciowej o nazwie `myNic` z poprzedniego kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Korzystając z flag interfejsu wiersza polecenia do wywoływania istniejących zasobów, firma Microsoft instruuje platformę Azure, aby wdrożyć maszynę wirtualną wewnątrz istniejącej sieci. Aby ponownie wykonać iterację, po wdrożeniu sieci wirtualnej i podsieci można je pozostawić jako zasoby statyczne lub stałe w regionie świadczenia usługi Azure.  

## <a name="next-steps"></a>Następne kroki
* [Tworzenie własnego środowiska niestandardowego dla maszyny wirtualnej z systemem Linux przy użyciu poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md)
* [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu szablonów](create-ssh-secured-vm-from-template.md)
