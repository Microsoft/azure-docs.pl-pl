---
title: Używanie wewnętrznego systemu DNS dla rozpoznawania nazw maszyn wirtualnych z interfejsu wiersza polecenia platformy Azure
description: Jak utworzyć karty interfejsu sieci wirtualnej i używać wewnętrznego dns rozpoznawania nazw maszyn wirtualnych na platformie Azure z interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 07a78e4987a844627824ac5034046cf6a393ad8d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757845"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Tworzenie kart interfejsu sieci wirtualnej i używanie wewnętrznego rozpoznawania nazw maszyn wirtualnych w systemie DNS na platformie Azure

W tym artykule pokazano, jak ustawić statyczne wewnętrzne nazwy DNS dla maszyn wirtualnych z systemem Linux przy użyciu kart interfejsu sieci wirtualnej (vNics) i nazw etykiet DNS za pomocą interfejsu wiersza polecenia platformy Azure. Statyczne nazwy DNS są używane dla usług infrastruktury trwałej, takich jak serwer kompilacji jenkinsa, który jest używany dla tego dokumentu lub serwer Git.

Wymagania są następujące:

* [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli musisz szybko wykonać zadanie, w poniższej sekcji opisano potrzebne polecenia. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, [zaczynając tutaj](#detailed-walkthrough). Aby wykonać te kroki, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

Wymagania wstępne: Grupa zasobów, sieć wirtualna i podsieć, grupa zabezpieczeń sieci z przychodzącym SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Tworzenie wirtualnej karty interfejsu sieciowego o statycznej wewnętrznej nazwie DNS
Tworzenie vNic z [az sieci nic utworzyć](/cli/azure/network/nic). Flaga interfejsu wiersza `--internal-dns-name` polecenia służy do ustawiania etykiety DNS, która zawiera statyczną nazwę DNS karty interfejsu sieci wirtualnej (vNic). Poniższy przykład tworzy vNic o nazwie `myNic` `myVnet` , łączy go z siecią `jenkins`wirtualną i tworzy wewnętrzny rekord nazwy DNS o nazwie:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Wdrażanie maszyny wirtualnej i łączenie vNic
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Flaga `--nics` łączy vNic do maszyny Wirtualnej podczas wdrażania na platformie Azure. Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` z dyskami zarządzanymi platformy Azure i dołącza vNic o nazwie `myNic` z poprzedniego kroku:

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

Pełna ciągła integracja i ciągła infrastruktura wdrażania (CiCd) na platformie Azure wymaga, aby niektóre serwery były serwerami statycznymi lub długotrwałymi. Zaleca się, aby zasoby platformy Azure, takie jak sieci wirtualne i sieciowe grupy zabezpieczeń, były statycznymi i długotrwałymi zasobami, które rzadko są wdrażane. Po wdrożeniu sieci wirtualnej może być ponownie wykorzystana przez nowe wdrożenia bez żadnego negatywnego wpływu na infrastrukturę. Później można dodać serwer repozytorium Git lub serwer automatyzacji usługi Jenkins dostarcza CiCd do tej sieci wirtualnej dla środowisk deweloperskich lub testowych.  

Wewnętrzne nazwy DNS można rozwiązywać tylko w sieci wirtualnej platformy Azure. Ponieważ nazwy DNS są wewnętrzne, nie można ich rozwiązać w internecie zewnętrznym, zapewniając dodatkowe zabezpieczenia infrastruktury.

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy `myResourceGroup` `myNic`parametrów `myVM`obejmują , i .

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów
Najpierw utwórz grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Następnym krokiem jest zbudowanie sieci wirtualnej, aby uruchomić maszyny wirtualne. Sieć wirtualna zawiera jedną podsieć dla tego przewodnika. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnej](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Utwórz sieć wirtualną za pomocą [sieci wirtualnej AZ create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć `myVnet` wirtualną `mySubnet`o nazwie i podsieci o nazwie:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Grupy zabezpieczeń sieci platformy Azure są równoważne zapory w warstwie sieciowej. Aby uzyskać więcej informacji na temat grup zabezpieczeń sieciowych, zobacz [Jak utworzyć sieciowe grupy zabezpieczeń w interfejsie wiersza polecenia platformy Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy grupę `myNetworkSecurityGroup`zabezpieczeń sieci o nazwie:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Dodawanie reguły przychodzącej w celu zezwolenia na ssh
Dodaj regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń z [regułą nsg sieci az .](/cli/azure/network/nsg/rule) Poniższy przykład tworzy `myRuleAllowSSH`regułę o nazwie:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Kojarzenie podsieci z sieciową grupą zabezpieczeń
Aby skojarzyć podsieć z sieciową grupą zabezpieczeń, użyj [aktualizacji podsieci sieci AZ](/cli/azure/network/vnet/subnet). Poniższy przykład kojarzy nazwę `mySubnet` podsieci `myNetworkSecurityGroup`z sieciową grupą zabezpieczeń o nazwie:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Tworzenie karty interfejsu sieci wirtualnej i statycznych nazw DNS
Platforma Azure jest bardzo elastyczna, ale aby używać nazw DNS dla rozpoznawania nazw maszyn wirtualnych, należy utworzyć karty interfejsu sieci wirtualnej (vNics), które zawierają etykietę DNS. VNics są ważne, jak można ich ponownie użyć, łącząc je z różnymi maszynami wirtualnymi w całym cyklu życia infrastruktury. Takie podejście zachowuje vNic jako zasób statyczny, podczas gdy maszyny wirtualne mogą być tymczasowe. Za pomocą dns etykietowania na vNic, jesteśmy w stanie włączyć proste rozpoznawanie nazw z innych maszyn wirtualnych w sieci wirtualnej. Używanie rozpoznawanych nazw umożliwia innym maszynom wirtualnym dostęp do `Jenkins` serwera automatyzacji `gitrepo`za pomocą nazwy DNS lub serwera Git jako .  

Tworzenie vNic z [az sieci nic utworzyć](/cli/azure/network/nic). Poniższy przykład tworzy vNic `myNic`o nazwie `myVnet` , łączy `myVnet`go z siecią wirtualną o nazwie i tworzy wewnętrzny rekord nazwy DNS o nazwie: `jenkins`

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Wdrażanie maszyny wirtualnej w infrastrukturze sieci wirtualnej
Mamy teraz sieć wirtualną i podsieć, grupę zabezpieczeń sieci działającą jako zapora do ochrony naszej podsieci, blokując cały ruch przychodzący z wyjątkiem portu 22 dla SSH i vNic. Teraz można wdrożyć maszynę wirtualną wewnątrz tej istniejącej infrastruktury sieciowej.

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie `myVM` z dyskami zarządzanymi platformy Azure i dołącza vNic o nazwie `myNic` z poprzedniego kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Za pomocą flagi interfejsu wiersza polecenia, aby wywołać istniejące zasoby, możemy poinstruować platformę Azure do wdrożenia maszyny Wirtualnej wewnątrz istniejącej sieci. Aby powtórzyć, po wdrożeniu sieci wirtualnej i podsieci można pozostawić jako statyczne lub stałe zasoby wewnątrz regionu platformy Azure.  

## <a name="next-steps"></a>Następne kroki
* [Tworzenie własnego środowiska niestandardowego dla maszyny Wirtualnej systemu Linux przy użyciu bezpośrednio poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu szablonów](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
