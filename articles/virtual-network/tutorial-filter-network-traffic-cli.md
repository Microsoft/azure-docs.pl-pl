---
title: Filtrowanie ruchu sieciowego — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule dowiesz się, jak filtrować ruch sieciowy w podsieci z grupą zabezpieczeń sieci przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2cabecfb2a70e6d325129335c066bb35aeb02ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221904"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
* Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
* Wdrażanie maszyn wirtualnych w podsieci
* Testowanie filtrów ruchu

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń. Reguły zabezpieczeń określają źródło i lokalizację docelową. Źródła i lokalizacje docelowe mogą być grupami zabezpieczeń aplikacji.

### <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów utworzonych w tym artykule za pomocą [AZ Group Create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz grupę zabezpieczeń aplikacji za pomocą [AZ Network ASG Create](/cli/azure/network/asg). Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych wymaganiach w zakresie filtrowania portów. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

Utwórz regułę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myWebServers* przez porty 80 i 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myMgmtServers* przez port 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

W tym artykule protokół SSH (port 22) został uwidoczniony w Internecie dla maszyny wirtualnej *myAsgMgmtServers* . W przypadku środowisk produkcyjnych zamiast udostępniania portu 22 do Internetu zaleca się nawiązanie połączenia z zasobami platformy Azure, którymi chcesz zarządzać za pomocą połączenia [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub sieci [prywatnej](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Dodaj podsieć do sieci wirtualnej za pomocą [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet). Poniższy przykład dodaje podsieć o nazwie *mySubnet* do sieci wirtualnej i kojarzy z nią sieciową grupę zabezpieczeń *myNsg*:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby umożliwić weryfikację filtrowania ruchu w kolejnym kroku. 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną, która będzie służyć jako serwer internetowy. `--asgs myAsgWebServers`Opcja powoduje, że platforma Azure tworzy interfejs sieciowy tworzony dla maszyny wirtualnej, która jest członkiem grupy zabezpieczeń aplikacji *myAsgWebServers* .

`--nsg ""`Opcja jest określona, aby zapobiec utworzeniu przez platformę Azure domyślnej sieciowej grupy zabezpieczeń dla interfejsu sieciowego platformy Azure tworzonego podczas tworzenia maszyny wirtualnej. W celu uproszczenia tego artykułu jest używane hasło. Klucze są zwykle używane w wdrożeniach produkcyjnych. W przypadku korzystania z kluczy należy również skonfigurować przekazywanie agentów SSH dla pozostałych kroków. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją klienta SSH. Zastąp wartość `<replace-with-your-password>` w poniższym poleceniu, wybierając wybrane hasło.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny wirtualnej zwraca dane wyjściowe podobne do następującego przykładu: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej z Internetu w późniejszym kroku.  Utwórz maszynę wirtualną, która będzie służyć jako serwer zarządzania:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny wirtualnej należy zwrócić uwagę na **publicIpAddress** w zwróconych danych wyjściowych. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej w następnym kroku. Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tworzenia maszyny wirtualnej.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

Użyj poniższego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVmMgmt* . Zastąp *\<publicIpAddress>* wartość publicznym adresem IP maszyny wirtualnej. W powyższym przykładzie adres IP to *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła wprowadź hasło wprowadzone w temacie [Tworzenie maszyn wirtualnych](#create-virtual-machines).

Połączenie powiedzie się, ponieważ port 22 jest dozwolony dla ruchu przychodzącego z Internetu do grupy zabezpieczeń aplikacji *myAsgMgmtServers* , w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmMgmt* .

Użyj następującego polecenia, aby SSH do maszyny wirtualnej *myVmWeb* z maszyny wirtualnej *myVmMgmt* :

```bash 
ssh azureuser@myVmWeb
```

Połączenie powiedzie się, ponieważ domyślne reguły zabezpieczeń w każdej sieciowej grupie zabezpieczeń zezwalają na ruch na wszystkich portach pomiędzy wszystkimi adresami IP w sieci wirtualnej. Nie można przeprowadzić połączenia SSH z maszyną wirtualną *myVmWeb* z Internetu, ponieważ reguła zabezpieczeń dla *myAsgWebServers* nie zezwala na ruch przychodzący na porcie 22 z Internetu.

Użyj następujących poleceń, aby zainstalować serwer sieci Web Nginx na maszynie wirtualnej *myVmWeb* :

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Maszyna wirtualna *myVmWeb* jest dozwolona dla ruchu wychodzącego z Internetu w celu pobrania Nginx, ponieważ domyślna reguła zabezpieczeń zezwala na cały ruch wychodzący do Internetu. Zakończ sesję SSH *myVmWeb* , która opuszcza `username@myVmMgmt:~$` monit o maszynę wirtualną *myVmMgmt* . Aby pobrać ekran powitalny Nginx z maszyny wirtualnej *myVmWeb* , wprowadź następujące polecenie:

```bash
curl myVmWeb
```

Wyloguj się z maszyny wirtualnej *myVmMgmt* . Aby upewnić się, że możesz uzyskać dostęp do serwera sieci Web *myVmWeb* spoza platformy Azure, wprowadź swój `curl <publicIpAddress>` własny komputer. Połączenie powiedzie się, ponieważ port 80 jest dozwolony dla ruchu przychodzącego z Internetu do grupy zabezpieczeń aplikacji *myAsgWebServers* , w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmWeb* .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](./network-security-groups-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie tabeli tras](tutorial-create-route-table-cli.md).