---
title: Otwieranie portów dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak otworzyć port/utworzyć punkt końcowy na maszynie wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c57ec2edf43bf0a710f3c15f30836f751c4d52b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500167"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Otwieranie portów i punktów końcowych na maszynie wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz otworzyć port lub utworzyć punkt końcowy na maszynie wirtualnej na platformie Azure, tworząc filtr sieci dla podsieci lub interfejsu sieciowego maszyny wirtualnej. Należy umieścić te filtry, które kontrolują ruch przychodzący i wychodzący, w sieciowej grupie zabezpieczeń dołączonej do zasobu, który odbiera ruch. Użyjmy typowego przykładu ruchu w sieci Web na porcie 80. W tym artykule pokazano, jak otworzyć port do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. 


Aby utworzyć sieciową grupę zabezpieczeń i reguły, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na koncie platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy *parametrów obejmują:* *MyNetworkSecurityGroup*, i *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Szybko Otwórz port dla maszyny wirtualnej
Jeśli musisz szybko otworzyć port dla maszyny wirtualnej w scenariuszu tworzenia i testowania, możesz użyć polecenia [AZ VM Open-Port](/cli/azure/vm) . To polecenie tworzy sieciową grupę zabezpieczeń, dodaje regułę i stosuje ją do maszyny wirtualnej lub podsieci. W poniższym przykładzie zostanie otwarty port *80* na maszynie wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *resourceName*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Aby uzyskać większą kontrolę nad regułami, takimi jak Definiowanie zakresu źródłowych adresów IP, wykonaj dodatkowe kroki opisane w tym artykule.


## <a name="create-a-network-security-group-and-rules"></a>Tworzenie sieciowej grupy zabezpieczeń i reguł
Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* w lokalizacji *Wschodnie* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Dodaj regułę przy użyciu [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule) , aby zezwalać na ruch HTTP do serwera WebSerwer (lub dostosowywać do własnego scenariusza, takiego jak dostęp SSH lub łączność z bazą danych). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule* , aby zezwalać na ruch TCP na porcie 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Zastosuj sieciową grupę zabezpieczeń do maszyny wirtualnej
Skojarz sieciową grupę zabezpieczeń z interfejsem sieciowym maszyny wirtualnej za pomocą przeglądarki [AZ Network nic Update](/cli/azure/network/nic). Poniższy przykład kojarzy istniejącą kartę interfejsu sieciowego o nazwie *myNic* z grupą zabezpieczeń sieci o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternatywnie można skojarzyć sieciową grupę zabezpieczeń z podsiecią sieci wirtualnej za pomocą [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet) , a nie tylko z interfejsem sieciowym na jednej maszynie wirtualnej. Poniższy przykład kojarzy istniejącą podsieć o nazwie Moja *podsieć* w sieci wirtualnej *myVnet* z grupą zabezpieczeń sieci o nazwie *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat sieciowych grup zabezpieczeń
Szybkie polecenia w tym miejscu umożliwiają rozpoczęcie pracy z ruchem skierowanym do maszyny wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele doskonałych funkcji i szczegółowości kontroli dostępu do zasobów. Więcej informacji o [tworzeniu sieciowych grup zabezpieczeń i reguł listy ACL](tutorial-virtual-network.md#secure-network-traffic)można znaleźć tutaj.

W przypadku aplikacji sieci Web o wysokiej dostępności należy umieścić maszyny wirtualne za Azure Load Balancer. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [jak równoważyć obciążenie maszyn wirtualnych z systemem Linux na platformie Azure w celu utworzenia aplikacji o wysokiej](tutorial-load-balancer.md)dostępności.

## <a name="next-steps"></a>Następne kroki
W tym przykładzie utworzono prostą regułę zezwalającą na ruch HTTP. Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:

* [Przegląd Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)
