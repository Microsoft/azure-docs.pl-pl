---
title: 'Szybki start: tworzenie publicznego równoważenia obciążenia — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: W tym samouczku przedstawiono sposób tworzenia publicznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9b332b18930d58ebb1d155c35a74eed69a90ce73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788785"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Szybki start: tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpoczynanie pracy z Azure Load Balancer za pomocą interfejsu wiersza polecenia platformy Azure w celu utworzenia publicznego równoważenia obciążenia i trzech maszyn wirtualnych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

* O **nazwie CreatePubLBQS-rg.** 
* W lokalizacji **eastus.**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>W przypadku obciążeń produkcyjnych zalecany jest standardowy SKU równoważenia obciążenia. Aby uzyskać więcej informacji na temat jednostki SKU, **[zobacz Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Zasoby standardowego równoważenia obciążenia utworzone na podstawie przewodnika Szybki start." border="false":::

## <a name="configure-virtual-network---standard"></a>Konfigurowanie sieci wirtualnej — standardowa

Przed wdrożeniem maszyn wirtualnych i testowaniem usługi równoważenia obciążenia utwórz zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą [narzędzia az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt):

* Nosi **nazwę myVNet.**
* Prefiks adresu **10.1.0.0/16.**
* Podsieć **o nazwie myBackendSubnet.**
* Prefiks podsieci **10.1.0.0/24.**
* W **grupie zasobów CreatePubLBQS-rg.**
* Lokalizacja **regionu eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o nazwie **myBastionIP.**
* W **CCreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Tworzenie podsieci bastionu

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć bastionu:

* O **nazwie AzureBastionSubnet.**
* Prefiks adresu **10.1.1.0/24.**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Tworzenie hosta bastionu

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta bastionu:

* O **nazwie myBastionHost.**
* W **createPubLBQS-rg**.
* Skojarzony z publicznym adresem IP **myBastionIP.**
* Skojarzone z siecią **wirtualną myVNet.**
* W **lokalizacji eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta może potrwać Azure Bastion kilka minut.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku standardowego równoważenia obciążenia maszyny wirtualne w adresie zaplecza dla usługi muszą mieć interfejsy sieciowe, które należą do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą [narzędzia az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* O **nazwie myNSG**.
* W grupie zasobów **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* O **nazwie myNSGRuleHTTP.**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku jest to **myNSG.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce **docelowe (*)**.
* Port docelowy **80.**
* Zezwalaj **na dostęp.**
* Priorytet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Tworzenie serwerów zaplecza — Standardowa

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla usługi równoważenia obciążenia.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Tworzenie interfejsów sieciowych dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create):

* Nazwy **myNicVM1,** **myNicVM2** i **myNicVM3.**
* W grupie zasobów **CreatePubLBQS-rg**.
* W sieci **wirtualnej myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą az [vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>Maszyna wirtualna 1
* O **nazwie myVM1.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączony do interfejsu **sieciowego myNicVM1.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O **nazwie myVM2.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączony do interfejsu **sieciowego myNicVM2.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>Maszyna wirtualna VM3
* Nazwa: **myVM3.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączony do interfejsu **sieciowego myNicVM3.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Wdrożenie maszyn wirtualnych może potrwać kilka minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>Tworzenie publicznego adresu IP — standardowa

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o **nazwie myPublicIP.**
* W **createPubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Aby utworzyć strefowo nadmiarowy publiczny adres IP w Strefa 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Tworzenie standardowego równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonia, która odbiera przychodzący ruch sieciowy w równoważeniu obciążenia.
  * Pula adresów IP zaplecza, do której pula frontony wysyła ruch sieciowy ze zrównoważonym obciążeniem.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz publiczny równoważenie obciążenia za pomocą [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* O **nazwie myLoadBalancer.**
* Pula frontendu o **nazwie myFrontEnd.**
* Pula zaplecza o nazwie **myBackEndPool.**
* Skojarzony z publicznym adresem IP **myPublicIP** utworzonym w poprzednim kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z nieudanym sprawdzaniem sondy jest usuwana z usługi równoważenia obciążenia. Maszyna wirtualna jest dodawana z powrotem do usługi równoważenia obciążenia, gdy błąd zostanie rozwiązany.

Utwórz sondę kondycji za pomocą [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitoruje kondycję maszyn wirtualnych.
* Nosi **nazwę myHealthProbe.**
* Protokół **TCP**.
* Port **monitorowania 80.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonia dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę usługi równoważenia obciążenia za pomocą [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* O **nazwie myHTTPRule**
* Nasłuchuje **na porcie 80** w puli frontonie **myFrontEnd.**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool** przy **użyciu portu 80.** 
* Za pomocą sondy **kondycji myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności: **15 minut.**
* Włącz resetowanie protokołu TCP.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza usługi równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza, a następnie dodaj polecenie [az network nic ip-config address-pool](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* W puli adresów zaplecza **pula adresów myBackEndPool.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzony z równoważeniem obciążenia **myLoadBalancer.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Tworzenie konfiguracji reguły ruchu wychodzącego
Reguły ruchu wychodzącego usługi Równoważenie obciążenia konfigurują wychodzące SNAT dla maszyn wirtualnych w puli zaplecza. 

Aby uzyskać więcej informacji na temat połączeń wychodzących, zobacz [Połączenia wychodzące na platformie Azure.](load-balancer-outbound-connections.md)

W przypadku konfiguracji ruchu wychodzącego można użyć publicznego adresu IP lub prefiksu.

### <a name="public-ip"></a>Publiczny adres IP

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć pojedynczy adres IP dla łączności wychodzącej.  

* Nazwane **myPublicIPOutbound.**
* W **createPubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Aby utworzyć strefowo nadmiarowy publiczny adres IP w Strefa 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Prefiks publicznego adresu IP

Użyj [az network public-ip prefix create,](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) aby utworzyć prefiks publicznego adresu IP dla łączności wychodzącej.

* O **nazwie myPublicIPPrefixOutbound.**
* W **createPubLBQS-rg**.
* Długość prefiksu **to 28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Aby utworzyć strefowy nadmiarowy prefiks publicznego adresu IP w Strefa 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Aby uzyskać więcej informacji na temat skalowania wychodzącego i wychodzącego połączenia NAT, zobacz Scale outbound NAT with multiple IP addresses (Skalowanie wychodzącego [nat nat z wieloma adresami IP).](load-balancer-outbound-connections.md)

### <a name="create-outbound-frontend-ip-configuration"></a>Tworzenie konfiguracji adresu IP frontonia ruchu wychodzącego

Utwórz nową konfigurację adresu IP frontonia za pomocą [az network lb frontend-ip create: ](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)

Wybierz polecenia publicznego adresu IP lub prefiksu publicznego adresu IP na podstawie decyzji z poprzedniego kroku.

#### <a name="public-ip"></a>Publiczny adres IP

* Nazwane **myFrontEndOutbound.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzony z publicznym adresem IP **myPublicIPOutbound.**
* Skojarzona z **równoważeniem obciążenia myLoadBalancer.**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefiks publicznego adresu IP

* Nosi **nazwę myFrontEndOutbound.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzony z prefiksem publicznego adresu IP **myPublicIPPrefixOutbound.**
* Skojarzona z **równoważeniem obciążenia myLoadBalancer.**

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Tworzenie puli ruchu wychodzącego

Utwórz nową pulę ruchu wychodzącego za pomocą [az network lb address-pool create:](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)

* Nazwa **myBackEndPoolOutbound.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzona z **równoważeniem obciążenia myLoadBalancer.**

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Tworzenie reguły ruchu wychodzącego

Utwórz nową regułę ruchu wychodzącego dla puli zaplecza dla ruchu wychodzącego za pomocą [az network lb outbound-rule create:](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create)

* Nazwane **myOutboundRule**.
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzone z równoważeniem obciążenia **myLoadBalancer**
* Skojarzony z **frontonem myFrontEndOutbound.**
* Protokół **Wszystkie**.
* Limit czasu bezczynności wynosi **15**.
* **10000 portów** wychodzących.
* Skojarzone z pulą zaplecza **myBackEndPoolOutbound.**

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Dodawanie maszyn wirtualnych do puli ruchu wychodzącego

Dodaj maszyny wirtualne do puli ruchu wychodzącego za pomocą az [network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):


* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzony z równoważeniem obciążenia **myLoadBalancer.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>W przypadku obciążeń produkcyjnych zalecany jest standardowy sku równoważenia obciążenia. Aby uzyskać więcej informacji na temat jednostki SKU, **[zobacz Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Podstawowe zasoby równoważenia obciążenia utworzone w przewodniku Szybki start." border="false"::: M

## <a name="configure-virtual-network---basic"></a>Konfigurowanie sieci wirtualnej — podstawowa

Przed wdrożeniem maszyn wirtualnych i przetestowanie usługi Load Balancer utwórz zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą [narzędzia az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Nazwane **myVNet.**
* Prefiks adresu **10.1.0.0/16.**
* Podsieć **o nazwie myBackendSubnet.**
* Prefiks podsieci **10.1.0.0/24.**
* W grupie **zasobów CreatePubLBQS-rg.**
* Lokalizacja **regionu eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP hosta bastionu:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o **nazwie myBastionIP.**
* W **createPubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Tworzenie podsieci bastionu

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć bastionu:

* O **nazwie AzureBastionSubnet.**
* Prefiks adresu **10.1.1.0/24.**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Tworzenie hosta bastionu

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta bastionu:

* Nazwane **myBastionHost**.
* W **createPubLBQS-rg**.
* Skojarzony z publicznym adresem IP **myBastionIP.**
* Skojarzone z siecią **wirtualną myVNet.**
* W **lokalizacji eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta Azure Bastion może potrwać kilka minut.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku standardowego równoważenia obciążenia maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą [narzędzia az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* Nosi **nazwę myNSG.**
* W grupie zasobów **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* O **nazwie myNSGRuleHTTP.**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku jest to **myNSG.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce **docelowe (*)**.
* Port docelowy **80.**
* Zezwalaj **na dostęp.**
* Priorytet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Tworzenie serwerów zaplecza — podstawowa

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Zestaw dostępności dla maszyn wirtualnych
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla usługi równoważenia obciążenia.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Tworzenie interfejsów sieciowych dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create):


* Nazwy **myNicVM1,** **myNicVM2** i **myNicVM3.**
* W grupie zasobów **CreatePubLBQS-rg**.
* W sieci **wirtualnej myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Tworzenie zestawu dostępności dla maszyn wirtualnych

Utwórz zestaw dostępności za pomocą az [vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create):

* Nosi **nazwę myAvSet.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Lokalizacja **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą az [vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>Maszyna wirtualna 1
* O **nazwie myVM1.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączony do interfejsu **sieciowego myNicVM1.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O **nazwie myVM2.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączony do interfejsu **sieciowego myNicVM2.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>Maszyna wirtualna VM3
* O **nazwie myVM3.**
* W grupie zasobów **CreatePubLBQS-rg**.
* Dołączono do interfejsu **sieciowego myNicVM3.**
* Obraz maszyny **wirtualnej win2019datacenter**.
* W **Strefa 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Wdrażanie maszyn wirtualnych może potrwać kilka minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>Tworzenie publicznego adresu IP — podstawowa

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o **nazwie myPublicIP.**
* W **createPubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonia, która odbiera przychodzący ruch sieciowy w równoważeniu obciążenia.
  * Pula adresów IP zaplecza, do której pula frontony wysyła ruch sieciowy ze zrównoważonym obciążeniem.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz publiczny równoważenie obciążenia za pomocą [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* O **nazwie myLoadBalancer.**
* Pula frontendu o **nazwie myFrontEnd.**
* Pula zaplecza o nazwie **myBackEndPool.**
* Skojarzony z publicznym adresem IP **myPublicIP** utworzonym w poprzednim kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z nieudanym sprawdzaniem sondy jest usuwana z usługi równoważenia obciążenia. Maszyna wirtualna jest dodawana z powrotem do usługi równoważenia obciążenia, gdy błąd zostanie rozwiązany.

Utwórz sondę kondycji za pomocą [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitoruje kondycję maszyn wirtualnych.
* O **nazwie myHealthProbe.**
* Protokół **TCP**.
* Port **monitorowania 80.**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontendu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę usługi równoważenia obciążenia za pomocą [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* O **nazwie myHTTPRule**
* Nasłuchiwanie **na porcie 80** w puli frontonie **myFrontEnd.**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool** przy **użyciu portu 80.** 
* Za pomocą sondy **kondycji myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności: **15 minut.**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza usługi load balancer

Dodaj maszyny wirtualne do puli zaplecza za pomocą az [network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-rg**.
* Skojarzony z równoważeniem obciążenia **myLoadBalancer.**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Instalowanie usług IIS

Użyj [az vm extension set,](/cli/azure/vm/extension#az_vm_extension_set) aby zainstalować usługi IIS na maszynach wirtualnych i ustawić domyślną witrynę internetową na nazwę komputera.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Aby uzyskać publiczny adres IP modułu równoważenia obciążenia, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). 

Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testowanie modułu równoważenia obciążenia" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, równoważenie obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je za pomocą polecenia [az group](/cli/azure/group#az_group_delete) delete.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start

* Utworzono standardowy lub publiczny usługę równoważenia obciążenia
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu i sondę kondycji usługi równoważenia obciążenia.
* Przetestowano równoważenie obciążenia.

Aby dowiedzieć się więcej o Azure Load Balancer, przejdź do:
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)