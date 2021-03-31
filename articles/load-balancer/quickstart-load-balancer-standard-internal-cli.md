---
title: 'Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 10ac477bed97d2a48344aa8ef9b570d2b6203345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702627"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć wewnętrzny moduł równoważenia obciążenia i trzy maszyny wirtualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. Jeśli używasz Azure Cloud Shell, Najnowsza wersja jest już zainstalowana.

>[!NOTE]
>W przypadku obciążeń produkcyjnych zaleca się wybór Azure Load Balancer Standard. Ten artykuł zawiera informacje na temat Azure Load Balancer Standard, a także Azure Load Balancer Basic. Aby uzyskać więcej informacji o jednostkach SKU, zobacz [Azure Load Balancer SKU](skus.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener służący do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Nazwij grupę zasobów **CreateIntLBQS-RG** i określ lokalizację jako **Wschodnie**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer Standard

W tej sekcji utworzysz moduł równoważenia obciążenia, który równoważy obciążenie maszyn wirtualnych. Podczas tworzenia wewnętrznego modułu równoważenia obciążenia Sieć wirtualna jest konfigurowana jako sieć dla modułu równoważenia obciążenia. Na poniższym diagramie przedstawiono zasoby utworzone w ramach tego przewodnika Szybki Start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Zasoby usługi równoważenia obciążenia w warstwie Standardowa utworzone w ramach szybkiego startu." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

#### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create). Określ następujące ustawienia:

* O nazwie **myVNet**
* Prefiks adresu **10.1.0.0/16**
* Podsieć o nazwie **myBackendSubnet**
* Prefiks podsieci **10.1.0.0/24**
* W grupie zasobów **CreateIntLBQS-RG**
* Lokalizacja **wschodniego**

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

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta usługi Azure bastionu. Określ następujące ustawienia:

* Tworzenie strefy standardowa — nadmiarowy publiczny adres IP o nazwie **myBastionIP**
* W **CreateIntLBQS — RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Tworzenie podsieci usługi Azure bastionu

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć. Określ następujące ustawienia:

* O nazwie **AzureBastionSubnet**
* Prefiks adresu **10.1.1.0/24**
* W sieci wirtualnej **myVNet**
* W grupie zasobów **CreateIntLBQS — RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure Bastion

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta. Określ następujące ustawienia:

* O nazwie **myBastionHost**
* W **CreateIntLBQS — RG**
* Skojarzone z publicznym adresem IP **myBastionIP**
* Skojarzone z **myVNet** sieci wirtualnej
* W lokalizacji **wschodniego**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa upewnij się, że maszyny wirtualne mają interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create). Określ następujące ustawienia:

* O nazwie **myNSG**
* W grupie zasobów **CreateIntLBQS — RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Określ następujące ustawienia:

* O nazwie **myNSGRuleHTTP**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**
* W grupie zasobów **CreateIntLBQS — RG**
* Protokół **(*)**
* Kierunek **ruchu przychodzącego**
* Źródło **(*)**
* Miejsce docelowe **(*)**
* Port portu docelowego **80**
* **Zezwalaj na** dostęp
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
* Trzy maszyny wirtualne, które mają być używane jako serwery usługi równoważenia obciążenia.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create). Określ następujące ustawienia:

* O nazwach **myNicVM1**, **myNicVM2** i **myNicVM3**
* W grupie zasobów **CreateIntLBQS — RG**
* W sieci wirtualnej **myVNet**
* W **myBackendSubnet** podsieci
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

Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Określ następujące ustawienia:

* O nazwach **myVM1**, **myVM2** i **myVM3**
* W grupie zasobów **CreateIntLBQS — RG**
* Dołączone do interfejsów sieciowych **myNicVM1**, **myNicVM2** i **myNicVM3**
* **Win2019datacenter** obrazu maszyny wirtualnej
* W **Strefa 1**, **strefa 2** i **strefa 3**

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

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

* Pula adresów IP, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
* Druga Pula adresów IP, w której pierwsza Pula wysyła ruch sieciowy o zrównoważonym obciążeniu.
* Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych.
* Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

#### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Określ następujące ustawienia:

* O nazwie **myLoadBalancer**
* Pula o nazwie Moja **fronton**
* Pula o nazwie **myBackEndPool**
* Skojarzone z **myVNet** sieci wirtualnej
* Skojarzone z **myBackendSubnetą** podsieci

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

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Określ następujące ustawienia:

* Monitoruje kondycję maszyn wirtualnych
* O nazwie **myHealthProbe**
* Protokół **TCP**
* **Port monitorowania 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja protokołu IP dla ruchu przychodzącego.
* Pula adresów IP do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create). Określ następujące ustawienia:

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w puli **frontonu**
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów **myBackEndPool** za pomocą **portu 80** 
* Korzystanie z sondy kondycji **myHealthProbe**
* Protokół **TCP**
* Limit czasu bezczynności wynoszący **15 minut**
* Włącz Resetowanie protokołu TCP

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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Dodawanie maszyn wirtualnych do puli modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Określ następujące ustawienia:

* W puli adresów **myBackEndPool**
* W grupie zasobów **CreateIntLBQS — RG**
* Skojarzona z interfejsem sieciowym **myNicVM1**, **myNicVM2** i **myNicVM3**
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**

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

## <a name="azure-load-balancer-basic"></a>Azure Load Balancer podstawowa

W tej sekcji utworzysz moduł równoważenia obciążenia, który równoważy obciążenie maszyn wirtualnych. Podczas tworzenia wewnętrznego modułu równoważenia obciążenia Sieć wirtualna jest konfigurowana jako sieć dla modułu równoważenia obciążenia. Na poniższym diagramie przedstawiono zasoby utworzone w ramach tego przewodnika Szybki Start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Zasoby podstawowych modułów równoważenia obciążenia utworzone na potrzeby szybkiego startu." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

#### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-createt). Określ następujące ustawienia:

* O nazwie **myVNet**
* Prefiks adresu **10.1.0.0/16**
* Podsieć o nazwie **myBackendSubnet**
* Prefiks podsieci **10.1.0.0/24**
* W grupie zasobów **CreateIntLBQS-RG**
* Lokalizacja **wschodniego**

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

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta usługi Azure bastionu. Określ następujące ustawienia:

* Tworzenie strefy standardowa — nadmiarowy publiczny adres IP o nazwie **myBastionIP**
* W **CreateIntLBQS — RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Tworzenie podsieci usługi Azure bastionu

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć. Określ następujące ustawienia:

* O nazwie **AzureBastionSubnet**
* Prefiks adresu **10.1.1.0/24**
* W sieci wirtualnej **myVNet**
* W grupie zasobów **CreateIntLBQS — RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure Bastion

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta. Określ następujące ustawienia:

* O nazwie **myBastionHost**
* W **CreateIntLBQS — RG**
* Skojarzone z publicznym adresem IP **myBastionIP**
* Skojarzone z **myVNet** sieci wirtualnej
* W lokalizacji **wschodniego**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa upewnij się, że maszyny wirtualne mają interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create). Określ następujące ustawienia:

* O nazwie **myNSG**
* W grupie zasobów **CreateIntLBQS — RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Określ następujące ustawienia:

* O nazwie **myNSGRuleHTTP**
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**
* W grupie zasobów **CreateIntLBQS — RG**
* Protokół **(*)**
* Kierunek **ruchu przychodzącego**
* Źródło **(*)**
* Miejsce docelowe **(*)**
* Port portu docelowego **80**
* **Zezwalaj na** dostęp
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
* Trzy maszyny wirtualne, które mają być używane jako serwery usługi równoważenia obciążenia.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create). Określ następujące ustawienia:

* O nazwach **myNicVM1**, **myNicVM2** i **myNicVM3**
* W grupie zasobów **CreateIntLBQS — RG**
* W sieci wirtualnej **myVNet**
* W **myBackendSubnet** podsieci
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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Utwórz zestaw dostępności dla maszyn wirtualnych

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create). Określ następujące ustawienia:

* O nazwie **myAvailabilitySet**
* W grupie zasobów **CreateIntLBQS — RG**
* Lokalizacja **Wschodnie**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Określ następujące ustawienia:

* O nazwach **myVM1**, **myVM2** i **myVM3**
* W grupie zasobów **CreateIntLBQS — RG**
* Dołączone do interfejsów sieciowych **myNicVM1**, **myNicVM2** i **myNicVM3**
* **Win2019datacenter** obrazu maszyny wirtualnej
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

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

* Pula adresów IP, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
* Druga Pula adresów IP, w której pierwsza Pula wysyła ruch sieciowy o zrównoważonym obciążeniu.
* Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych.
* Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

#### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Określ następujące ustawienia:

* O nazwie **myLoadBalancer**
* Pula o nazwie Moja **fronton**
* Pula o nazwie **myBackEndPool**
* Skojarzone z **myVNet** sieci wirtualnej
* Skojarzone z **myBackendSubnetą** podsieci

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

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Określ następujące ustawienia:

* Monitoruje kondycję maszyn wirtualnych
* O nazwie **myHealthProbe**
* Protokół **TCP**
* **Port monitorowania 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja protokołu IP dla ruchu przychodzącego.
* Pula adresów IP do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create). Określ następujące ustawienia:

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w puli **frontonu**
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów **myBackEndPool** za pomocą **portu 80** 
* Korzystanie z sondy kondycji **myHealthProbe**
* Protokół **TCP**
* Limit czasu bezczynności wynoszący **15 minut**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Dodawanie maszyn wirtualnych do puli modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Określ następujące ustawienia:

* W puli adresów **myBackEndPool**
* W grupie zasobów **CreateIntLBQS — RG**
* Skojarzona z interfejsem sieciowym **myNicVM1**, **myNicVM2** i **myNicVM3**
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**

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

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create). Określ następujące ustawienia:

* O nazwie **myNicTestVM**
* W grupie zasobów **CreateIntLBQS — RG**
* W sieci wirtualnej **myVNet**
* W **myBackendSubnet** podsieci
* W sieciowej grupie zabezpieczeń **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create). Określ następujące ustawienia:

* O nazwie **myTestVM**
* W grupie zasobów **CreateIntLBQS — RG**
* Dołączone do interfejsu sieciowego **myNicTestVM**
* **Win2019Datacenter** obrazu maszyny wirtualnej

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Może być konieczne odczekanie kilku minut, aż maszyna wirtualna zostanie wdrożona.

## <a name="install-iis"></a>Instalowanie usług IIS

Użyj opcji [AZ VM Extension Set](/cli/azure/vm/extension#az_vm_extension_set) , aby zainstalować usługi IIS na maszynach wirtualnych i ustawić domyślną witrynę sieci Web na nazwę komputera.

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

2. Na stronie **Przegląd** Znajdź prywatny adres IP dla modułu równoważenia obciążenia. W menu po lewej stronie wybierz pozycję **wszystkie usługi**  >  **wszystkie zasoby**  >  **myLoadBalancer**.

3. W omówieniu usługi **myLoadBalancer** Skopiuj adres obok pozycji **prywatny adres IP**.

4. W menu po lewej stronie wybierz pozycję **wszystkie usługi**  >  **wszystkie zasoby**. Z listy zasobów w grupie zasobów **CreateIntLBQS-RG** wybierz pozycję **myTestVM**.

5. Na stronie **Przegląd** wybierz pozycję **Połącz**  >  **bastionu**.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. W programie **myTestVM** Otwórz program **Internet Explorer**.

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. Domyślna strona serwera sieci Web usług IIS jest wyświetlana w przeglądarce.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Zrzut ekranu przedstawiający adres IP na pasku adresu przeglądarki." border="true":::
   
Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS każdej maszyny wirtualnej. Następnie ręcznie Odśwież przeglądarkę sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby nie są już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) , aby usunąć grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi Azure Load Balancer.
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
