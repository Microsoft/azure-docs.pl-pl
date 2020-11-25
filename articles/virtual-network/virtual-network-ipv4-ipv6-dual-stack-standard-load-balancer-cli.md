---
title: Wdrażanie aplikacji podwójnego stosu IPv6 — usługa Load Balancer w warstwie Standardowa — interfejs wiersza polecenia
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację dwustosową protokołu IPv6 w usłudze Azure Virtual Network przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 07bd8e19fdd75cc97a9c34152ea5f06f850ef724
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95995690"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli"></a>Wdrażanie aplikacji dwustosowej IPv6 w usłudze Azure Virtual Network — interfejs wiersza polecenia

W tym artykule opisano sposób wdrażania aplikacji podwójnego stosu (IPv4 + IPv6) przy użyciu usługa Load Balancer w warstwie Standardowa na platformie Azure, która obejmuje sieć wirtualną o podwójnej stercie z podsiecią podwójnego stosu, usługa Load Balancer w warstwie Standardowa z podwójnymi konfiguracjami frontonu (IPv4 + IPv6), maszynami wirtualnymi z kartami sieciowymi, które mają dwie konfiguracje IP, dwie reguły sieciowej grupy zabezpieczeń i dwa publiczne adresy IP

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.49 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej o podwójnym stosie należy utworzyć grupę zasobów za pomocą [AZ Group Create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *DsResourceGroup01* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Tworzenie publicznych adresów IP adresów IPv4 i IPv6 dla usługi równoważenia obciążenia
Aby uzyskać dostęp do punktów końcowych protokołów IPv4 i IPv6 w Internecie, należy dysponować adresami IP dla usługi równoważenia obciążenia IPv4 i IPv6. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Poniższy przykład tworzy publiczny adres IP IPv4 i IPv6 o nazwie *dsPublicIP_v4* i *dsPublicIP_v6* w grupie zasobów *DsResourceGroup01* :

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Tworzenie publicznych adresów IP dla maszyn wirtualnych

Aby zdalnie uzyskiwać dostęp do maszyn wirtualnych za pośrednictwem Internetu, należy dysponować publicznymi adresami IP IPv4 dla maszyn wirtualnych. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji należy skonfigurować podwójny adres IP frontonu (IPv4 i IPv6) oraz pulę adresów zaplecza dla modułu równoważenia obciążenia, a następnie utworzyć usługa Load Balancer w warstwie Standardowa.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz usługa Load Balancer w warstwie Standardowa za pomocą [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie **dsLB** , która zawiera pulę frontonu o nazwie **dsLbFrontEnd_v4**, puli zaplecza o nazwie **DsLbBackEndPool_v4** skojarzonej z publicznym adresem IP IPv4 **dsPublicIP_v4** utworzonego w poprzednim kroku. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Tworzenie frontonu IPv6

Utwórz adres IP frontonu IPV6 za pomocą [AZ Network lb fronton-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Poniższy przykład umożliwia utworzenie konfiguracji adresu IP frontonu o nazwie *dsLbFrontEnd_v6* i dołączenie adresu *dsPublicIP_v6* :

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza IPv6

Utwórz pule adresów zaplecza IPv6 za pomocą [AZ Network lb Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). Poniższy przykład tworzy pulę adresów zaplecza o nazwie *dsLbBackEndPool_v6*  w celu uwzględnienia maszyn wirtualnych z konfiguracjami kart sieciowych IPv6:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). Poniższy przykład tworzy reguły modułu równoważenia obciążenia o nazwie *dsLBrule_v4* i *dsLBrule_v6* i równoważy ruch na *TCP* porcie TCP *80* do konfiguracji adresu IP frontonu IPv4 i IPv6:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem niektórych maszyn wirtualnych należy utworzyć pomocnicze zasoby sieciowe — zestaw dostępności, sieciową grupę zabezpieczeń, sieć wirtualną i wirtualne karty sieciowe. 
### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby zwiększyć dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Poniższy przykład tworzy zestaw dostępności o nazwie *dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń dla reguł, które będą zarządzać komunikacją przychodzącą i wychodzącą w sieci wirtualnej.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla połączeń przychodzących i wychodzących

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia RDP przez port 3389, połączenie internetowe przez port 80 oraz dla połączeń wychodzących za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVNET* z podsieciami *dsSubNET_v4* i *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Tworzenie kart sieciowych

Utwórz wirtualne karty sieciowe dla każdej maszyny wirtualnej za pomocą [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Poniższy przykład tworzy wirtualną kartę sieciową dla każdej maszyny wirtualnej. Każda karta sieciowa ma dwie konfiguracje IP (1 Konfiguracja IPv4, 1 konfiguracja IPv6). Konfigurację IPV6 można utworzyć za pomocą [AZ Network nic IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją. 

Utwórz *dsVM0* maszyny wirtualnej w następujący sposób:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Utwórz *dsVM1* maszyny wirtualnej w następujący sposób:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej podwójnego stosu IPv6 w Azure Portal
Sieć wirtualną o podwójnym stosie IPv6 można wyświetlić w Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *dsVnet*.
2. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej o podwójnym stosie o nazwie *dsVnet*. Sieć wirtualna o podwójnym stosie pokazuje dwie karty sieciowe z konfiguracją protokołów IPv4 i IPv6 znajdującą się w podwójnej podsieci o nazwie *dsSubnet*.

  ![Sieć wirtualna o podwójnym stosie IPv6 na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie usługa Load Balancer w warstwie Standardowa z konfiguracją dwóch adresów IP frontonu (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które zawierają karty sieciowe z dwoma konfiguracjami protokołu IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej o obsłudze protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla systemu azure Virtual Network?](ipv6-overview.md)
