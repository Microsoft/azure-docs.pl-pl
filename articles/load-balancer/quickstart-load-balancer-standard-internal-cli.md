---
title: 'Szybki start: tworzenie wewnętrznego równoważenia obciążenia — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: W tym przewodniku Szybki start przedstawiono sposób tworzenia wewnętrznego równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788839"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Szybki start: tworzenie wewnętrznego równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpoczynanie pracy z Azure Load Balancer za pomocą interfejsu wiersza polecenia platformy Azure w celu utworzenia wewnętrznego równoważenia obciążenia i trzech maszyn wirtualnych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz usługi Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Nadaj grupie zasobów **nazwę CreateIntLBQS-rg** i określ lokalizację jako **eastus.**

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>W przypadku obciążeń produkcyjnych zalecany jest standardowy SKU równoważenia obciążenia. Aby uzyskać więcej informacji na temat jednostki SKU, **[zobacz Azure Load Balancer SKU](skus.md)**.

W tej sekcji utworzysz równoważenie obciążenia, które równoważy obciążenie maszyn wirtualnych. Podczas tworzenia wewnętrznego równoważenia obciążenia sieć wirtualna jest konfigurowana jako sieć dla tego równoważenia obciążenia. Na poniższym diagramie przedstawiono zasoby utworzone w tym przewodniku Szybki start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Zasoby standardowego równoważenia obciążenia utworzone na podstawie przewodnika Szybki start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem usługi równoważenia obciążenia utwórz zasoby sieci wirtualnej.

#### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą [narzędzia az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create) Określ następujące ustawienia:

* O **nazwie myVNet**
* Prefiks adresu **10.1.0.0/16**
* Podsieć **o nazwie myBackendSubnet**
* Prefiks podsieci **10.1.0.0/24**
* W grupie **zasobów CreateIntLBQS-rg**
* Lokalizacja **regionu eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP Azure Bastion hosta. Określ następujące ustawienia:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o nazwie **myBastionIP**
* W **jęz. CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Tworzenie Azure Bastion podsieci

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć. Określ następujące ustawienia:

* O **nazwie AzureBastionSubnet**
* Prefiks adresu **10.1.1.0/24**
* W sieci **wirtualnej myVNet**
* W grupie zasobów **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure Bastion

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta. Określ następujące ustawienia:

* O **nazwie myBastionHost**
* W **jęz. CreateIntLBQS-rg**
* Skojarzone z publicznym adresem IP **myBastionIP**
* Skojarzone z siecią **wirtualną myVNet**
* W **lokalizacji eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta może potrwać Azure Bastion kilka minut.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku standardowego równoważenia obciążenia upewnij się, że maszyny wirtualne mają interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. Utwórz sieciową grupę zabezpieczeń za pomocą [narzędzia az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Określ następujące ustawienia:

* O **nazwie myNSG**
* W grupie zasobów **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [narzędzia az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Określ następujące ustawienia:

* O **nazwie myNSGRuleHTTP**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku jest to **grupa myNSG**
* W grupie zasobów **CreateIntLBQS-rg**
* Protokół **(*)**
* Kierunek **ruchu przychodzącego**
* Źródło **(*)**
* Miejsce **docelowe (*)**
* Port docelowy **80**
* Zezwalaj na **dostęp**
* Priorytet **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Trzy maszyny wirtualne, które mają być używane jako serwery dla usługi równoważenia obciążenia.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Tworzenie interfejsów sieciowych dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create). Określ następujące ustawienia:

* Nazwy **myNicVM1,** **myNicVM2** i **myNicVM3**
* W grupie zasobów **CreateIntLBQS-rg**
* W sieci **wirtualnej myVNet**
* W podsieci **myBackendSubnet**
* W sieciowej grupie zabezpieczeń **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Określ następujące ustawienia:

* Nazwy **myVM1,** **myVM2** i **myVM3**
* W grupie zasobów **CreateIntLBQS-rg**
* Dołączono do **interfejsu sieciowego myNicVM1,** **myNicVM2** i **myNicVM3**
* Obraz maszyny **wirtualnej win2019datacenter**
* W **Strefa 1**, **Strefa 2** i **Strefa 3**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Wdrożenie maszyn wirtualnych może potrwać kilka minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

* Pula adresów IP, która odbiera przychodzący ruch sieciowy w równoważeniu obciążenia.
* Druga pula adresów IP, w której pierwsza pula wysyła ruch sieciowy ze zrównoważonym obciążeniem.
* Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych.
* Reguła równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

#### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz publiczny równoważenie obciążenia za pomocą [az network lb create](/cli/azure/network/lb#az_network_lb_create). Określ następujące ustawienia:

* O **nazwie myLoadBalancer**
* Pula o nazwie **myFrontEnd**
* Pula o nazwie **myBackEndPool**
* Skojarzone z siecią wirtualną **myVNet**
* Skojarzone z podsiecią **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Maszyna wirtualna z nieudanym sprawdzaniem sondy jest usuwana z usługi równoważenia obciążenia. Maszyna wirtualna jest dodawana z powrotem do usługi równoważenia obciążenia, gdy awaria zostanie rozwiązana.

Utwórz sondę kondycji za pomocą [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Określ następujące ustawienia:

* Monitoruje kondycję maszyn wirtualnych
* O **nazwie myHealthProbe**
* Protokół **TCP**
* Monitorowanie **portu 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP dla ruchu przychodzącego.
* Pula adresów IP do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Określ następujące ustawienia:

* O **nazwie myHTTPRule**
* Nasłuchuje **na porcie 80** w puli **myFrontEnd**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów **myBackEndPool** przy użyciu **portu 80** 
* Korzystanie z sondy **kondycji myHealthProbe**
* Protokół **TCP**
* Limit czasu bezczynności: **15 minut**
* Włączanie resetowania protokołu TCP

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Dodawanie maszyn wirtualnych do puli równoważenia obciążenia

Dodaj maszyny wirtualne do puli back-end, a następnie dodaj polecenie [az network nic ip-config address-pool.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Określ następujące ustawienia:

* W puli adresów **myBackEndPool**
* W grupie zasobów **CreateIntLBQS-rg**
* Skojarzone z interfejsem **sieciowym myNicVM1,** **myNicVM2** i **myNicVM3**
* Skojarzone z równoważeniem obciążenia **myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>W przypadku obciążeń produkcyjnych zalecany jest standardowy sku równoważenia obciążenia. Aby uzyskać więcej informacji na temat jednostki SKU, **[zobacz Azure Load Balancer SKU](skus.md)**.

W tej sekcji utworzysz równoważenie obciążenia, które równoważy obciążenie maszyn wirtualnych. Podczas tworzenia wewnętrznego równoważenia obciążenia sieć wirtualna jest konfigurowana jako sieć dla tego usługi. Na poniższym diagramie przedstawiono zasoby utworzone w tym przewodniku Szybki start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Podstawowe zasoby równoważenia obciążenia utworzone na podstawie przewodnika Szybki start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem usługi równoważenia obciążenia utwórz zasoby sieci wirtualnej.

#### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą [az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt). Określ następujące ustawienia:

* O **nazwie myVNet**
* Prefiks adresu **10.1.0.0/16**
* Podsieć **o nazwie myBackendSubnet**
* Prefiks podsieci **10.1.0.0/24**
* W grupie **zasobów CreateIntLBQS-rg**
* Lokalizacja **regionu eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP Azure Bastion hosta. Określ następujące ustawienia:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o nazwie **myBastionIP**
* W **jęz. CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Tworzenie Azure Bastion podsieci

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć. Określ następujące ustawienia:

* O **nazwie AzureBastionSubnet**
* Prefiks adresu **10.1.1.0/24**
* W sieci **wirtualnej myVNet**
* W grupie zasobów **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure Bastion

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta. Określ następujące ustawienia:

* O **nazwie myBastionHost**
* W **jęz. CreateIntLBQS-rg**
* Skojarzone z publicznym adresem IP **myBastionIP**
* Skojarzone z siecią **wirtualną myVNet**
* W **lokalizacji eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta może potrwać Azure Bastion kilka minut.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku standardowego równoważenia obciążenia upewnij się, że maszyny wirtualne mają interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. Utwórz sieciową grupę zabezpieczeń za pomocą [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Określ następujące ustawienia:

* O **nazwie myNSG**
* W grupie zasobów **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń za pomocą [narzędzia az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Określ następujące ustawienia:

* O **nazwie myNSGRuleHTTP**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku jest to **grupa myNSG**
* W grupie zasobów **CreateIntLBQS-rg**
* Protokół **(*)**
* Kierunek **ruchu przychodzącego**
* Źródło **(*)**
* Miejsce **docelowe (*)**
* Port docelowy **80**
* Zezwalaj na **dostęp**
* Priorytet **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Zestaw dostępności dla maszyn wirtualnych.
* Trzy maszyny wirtualne, które mają być używane jako serwery dla usługi równoważenia obciążenia.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Tworzenie interfejsów sieciowych dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create). Określ następujące ustawienia:

* Nazwy **myNicVM1,** **myNicVM2** i **myNicVM3**
* W grupie zasobów **CreateIntLBQS-rg**
* W sieci **wirtualnej myVNet**
* W podsieci **myBackendSubnet**
* W sieciowej grupie zabezpieczeń **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Tworzenie zestawu dostępności dla maszyn wirtualnych

Utwórz zestaw dostępności za pomocą az [vm availability-set create.](/cli/azure/vm/availability-set#az_vm_availability_set_create) Określ następujące ustawienia:

* O **nazwie myAvailabilitySet**
* W grupie zasobów **CreateIntLBQS-rg**
* Lokalizacja **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Określ następujące ustawienia:

* Nazwy **myVM1,** **myVM2** i **myVM3**
* W grupie zasobów **CreateIntLBQS-rg**
* Dołączono do **interfejsu sieciowego myNicVM1,** **myNicVM2** i **myNicVM3**
* Obraz maszyny **wirtualnej win2019datacenter**
* W **myAvailabilitySet**


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Wdrożenie maszyn wirtualnych może potrwać kilka minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

* Pula adresów IP, która odbiera przychodzący ruch sieciowy w równoważeniu obciążenia.
* Druga pula adresów IP, w której pierwsza pula wysyła ruch sieciowy ze zrównoważonym obciążeniem.
* Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych.
* Reguła równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

#### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu równoważenia obciążenia

Utwórz publiczny równoważenie obciążenia za pomocą [az network lb create](/cli/azure/network/lb#az_network_lb_create). Określ następujące ustawienia:

* O **nazwie myLoadBalancer**
* Pula o **nazwie myFrontEnd**
* Pula o **nazwie myBackEndPool**
* Skojarzone z siecią wirtualną **myVNet**
* Skojarzone z podsiecią **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Maszyna wirtualna z nieudanym sprawdzaniem sondy jest usuwana z usługi równoważenia obciążenia. Maszyna wirtualna jest dodawana z powrotem do usługi równoważenia obciążenia, gdy awaria zostanie rozwiązana.

Utwórz sondę kondycji za pomocą [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Określ następujące ustawienia:

* Monitoruje kondycję maszyn wirtualnych
* O **nazwie myHealthProbe**
* Protokół **TCP**
* Monitorowanie **portu 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła równoważenia obciążenia definiuje:

* Konfiguracja adresu IP dla ruchu przychodzącego.
* Pula adresów IP do odbierania ruchu.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Określ następujące ustawienia:

* O **nazwie myHTTPRule**
* Nasłuchuje **na porcie 80** w puli **myFrontEnd**
* Wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów **myBackEndPool** przy użyciu **portu 80** 
* Korzystanie z sondy **kondycji myHealthProbe**
* Protokół **TCP**
* Limit czasu bezczynności **15 minut**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Dodawanie maszyn wirtualnych do puli równoważenia obciążenia

Dodaj maszyny wirtualne do puli back-end, a następnie dodaj polecenie [az network nic ip-config address-pool.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Określ następujące ustawienia:

* W puli adresów **myBackEndPool**
* W grupie zasobów **CreateIntLBQS-rg**
* Skojarzone z interfejsem **sieciowym myNicVM1,** **myNicVM2** i **myNicVM3**
* Skojarzone z równoważeniem obciążenia **myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Utwórz interfejs sieciowy za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create). Określ następujące ustawienia:

* O **nazwie myNicTestVM**
* W grupie zasobów **CreateIntLBQS-rg**
* W sieci **wirtualnej myVNet**
* W podsieci **myBackendSubnet**
* W sieciowej grupie zabezpieczeń **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Utwórz maszynę wirtualną za pomocą [az vm create](/cli/azure/vm#az_vm_create). Określ następujące ustawienia:

* O **nazwie myTestVM**
* W grupie zasobów **CreateIntLBQS-rg**
* Dołączone do interfejsu **sieciowego myNicTestVM**
* Obraz maszyny **wirtualnej Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Może być konieczne odczekaj kilka minut na wdrożenie maszyny wirtualnej.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Testowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na stronie **Przegląd** znajdź prywatny adres IP dla usługi równoważenia obciążenia. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**  >  **Wszystkie zasoby**  >  **myLoadBalancer.**

3. W przeglądzie **myLoadBalancer skopiuj** adres obok **prywatnego adresu IP.**

4. W menu po lewej stronie wybierz pozycję **Wszystkie usługi**  >  **Wszystkie zasoby.** Na liście zasobów w grupie zasobów **CreateIntLBQS-rg** wybierz **pozycję myTestVM.**

5. Na stronie **Przegląd** wybierz pozycję **Połącz**  >  **z bastionem.**

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Na **myTestVM** otwórz **Internet Explorer**.

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. Domyślna strona serwera internetowego usług IIS jest wyświetlana w przeglądarce.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Zrzut ekranu przedstawiający adres IP na pasku adresu przeglądarki." border="true":::
   
Aby zobaczyć, jak równoważenie obciążenia dystrybuuje ruch między wszystkie trzy maszyny wirtualne, możesz dostosować domyślną stronę serwera internetowego usług IIS każdej maszyny wirtualnej. Następnie ręcznie odśwież przeglądarkę internetową z komputera klienckiego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby nie będą już potrzebne, użyj [polecenia az group delete,](/cli/azure/group#az_group_delete) aby usunąć grupę zasobów, usługę równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi Azure Load Balancer.
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
