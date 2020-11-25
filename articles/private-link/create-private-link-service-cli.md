---
title: Tworzenie usługi Azure Private link przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć usługę prywatnego połączenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfffafaab2e2d4ef6b165ef03beb827342c94608
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96018056"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Tworzenie prywatnej usługi linkowej przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano sposób tworzenia usługi linku prywatnego na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- W tym artykule jest wymagana Najnowsza wersja interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego
### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów, która będzie hostowała tę sieć wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Ten przykład tworzy domyślną sieć wirtualną o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie Moja *podsieć*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Tworzenie podsieci
Utwórz podsieć dla sieci wirtualnej za pomocą [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Ten przykład tworzy podsieć o nazwie Moja *podsieć* w sieci wirtualnej *myVirtualNetwork* :

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Tworzenie Load Balancer wewnętrznej 
Utwórz wewnętrzny moduł równoważenia obciążenia za pomocą właściwości [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Ten przykład powoduje utworzenie wewnętrznego modułu równoważenia obciążenia o nazwie *myILB* w grupie zasobów o nazwie Moja *resourceName*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Tworzenie sondy kondycji modułu równoważenia obciążenia

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą one odbierać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myHTTPRule* za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest) w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEnd* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie nie obejmujemy tworzenia maszyn wirtualnych. Można wykonać kroki opisane w [przewodniku szybki start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) w celu utworzenia dwóch maszyn wirtualnych, które będą używane jako serwery zaplecza dla modułu równoważenia obciążenia. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Wyłączanie zasad sieciowych usługi linku prywatnego w podsieci 
Usługa link prywatny wymaga adresu IP z dowolnej podsieci wybranej w ramach sieci wirtualnej. Obecnie nie obsługujemy zasad sieciowych w tych adresach IP.  W związku z tym musimy wyłączyć zasady sieciowe w podsieci. Zaktualizuj podsieć, aby wyłączyć zasady sieciowe usługi linku prywatnego za pomocą [aktualizacji AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Tworzenie usługi linku prywatnego przy użyciu usługa Load Balancer w warstwie Standardowa 
 
Utwórz usługę linku prywatnego przy użyciu konfiguracji adresu IP frontonu usługa Load Balancer w warstwie Standardowa za pomocą polecenia [AZ Network Private-Link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create). W tym przykładzie tworzona jest usługa linku prywatnego o nazwie *myPLS* przy użyciu usługa Load Balancer w warstwie Standardowa o nazwie *myLoadBalancer* w grupie zasobów o nazwie Moja *resourceName*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Po utworzeniu należy zwrócić uwagę na identyfikator usługi linku prywatnego. Będzie potrzebne później do żądania połączenia z tą usługą.  
 
Na tym etapie usługa linku prywatnego została pomyślnie utworzona i będzie gotowa do odbierania ruchu. Należy pamiętać, że powyższy przykład dotyczy tylko tworzenia usługi link prywatny przy użyciu interfejsu wiersza polecenia platformy Azure.  Nie skonfigurowano pul zaplecza modułu równoważenia obciążenia ani żadnej aplikacji w pulach zaplecza do nasłuchiwania ruchu. Jeśli chcesz zobaczyć kompleksowe przepływy ruchu, stanowczo zalecamy skonfigurowanie aplikacji za usługa Load Balancer w warstwie Standardowa.  
 
Następnie przedstawimy sposób mapowania tej usługi do prywatnego punktu końcowego w innej sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Ponownie Przykładowo można utworzyć prywatny punkt końcowy i połączyć się z usługą linków prywatnych utworzoną powyżej przy użyciu interfejsu wiersza polecenia platformy Azure. Ponadto można tworzyć maszyny wirtualne w sieci wirtualnej w celu wysyłania/odbierania ruchu do prywatnego punktu końcowego.        
 
## <a name="private-endpoints"></a>Prywatne punkty końcowe

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej 
Utwórz sieć wirtualną za pomocą [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create). Ten przykład umożliwia utworzenie sieci wirtualnej o nazwie  *myPEVNet*   w grupie zasobów o nazwie Moja *zasobów*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Utwórz podsieć 
Utwórz podsieć w sieci wirtualnej za pomocą [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Ten przykład tworzy podsieć o nazwie Moja  *podsieć*   w sieci wirtualnej o nazwie *myPEVnet* w grupie zasobów o nazwie Moja *zasobów*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Wyłącz zasady sieci prywatnego punktu końcowego w podsieci 
Prywatny punkt końcowy można utworzyć w dowolnej podsieci w sieci wirtualnej. Obecnie nie obsługujemy zasad sieciowych dla prywatnych punktów końcowych.  W związku z tym musimy wyłączyć zasady sieciowe w podsieci. Zaktualizuj podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego za pomocą [aktualizacji AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Tworzenie prywatnego punktu końcowego i nawiązywanie połączenia z usługą linku prywatnego 
Utwórz prywatny punkt końcowy do korzystania z usługi linku prywatnego utworzonego powyżej w sieci wirtualnej:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Możesz uzyskać *połączenie Private-Connection-Resource-ID* z `az network private-link-service show` usługą link prywatny. Identyfikator będzie wyglądać następująco:   
/subscriptions/subID/resourceGroups/*ResourceGroupName*/Providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Pokaż połączenia usługi link prywatny 
 
Zobacz żądania połączeń w usłudze linku prywatnym przy użyciu polecenia [AZ Network Private-Link-Service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Private Link Service](private-link-service-overview.md)
