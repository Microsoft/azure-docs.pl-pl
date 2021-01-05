---
title: 'Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: fd85021da36d0e5ef32ce3f42c72b83016d38749
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901355"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć wewnętrzny moduł równoważenia obciążenia i trzy maszyny wirtualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az_group_create):

* O nazwie **CreateIntLBQS-RG**. 
* W lokalizacji **wschodniego** .

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network---standard"></a>Konfigurowanie sieci wirtualnej — standardowa

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create):

* O nazwie **myVNet**.
* Prefiks adresu **10.1.0.0/16**.
* Podsieć o nazwie **myBackendSubnet**.
* Prefiks podsieci **10.1.0.0/24**.
* W grupie zasobów **CreateIntLBQS-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myBastionIP**.
* W * * CCreateIntLBQS-RG * *.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Tworzenie podsieci bastionu

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć bastionu:

* O nazwie **AzureBastionSubnet**.
* Prefiks adresu **10.1.1.0/24**.
* W sieci wirtualnej **myVNet**.
* W grupie zasobów * * CreateIntLBQS-RG * *.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Utwórz hosta bastionu

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta bastionu:

* O nazwie **myBastionHost**.
* W * * CreateIntLBQS-RG * *.
* Skojarzone z publicznym adresem IP **myBastionIP**.
* Skojarzone z **myVNet** sieci wirtualnej.
* W lokalizacji **wschodniego** .

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.


### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* O nazwie **myNSGRuleHTTP**.
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce docelowe **(*)**.
* Port portu docelowego **80**.
* **Zezwalaj na** dostęp.
* Priorytet **200**.

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

## <a name="create-backend-servers---standard"></a>Tworzenie serwerów zaplecza — Standard

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create):

* Nazwane **myNicVM1**, **myNicVM2** i **myNicVM3**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

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

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create):

* Nazwane **myVM1**, **myVM2** i **myVM3**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsów sieciowych **myNicVM1**, **myNicVM2** i **myNicVM3**.
* **Win2019datacenter** obrazu maszyny wirtualnej.
* W **Strefa 1**, **strefa 2** i **strefa 3**.

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

## <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* O nazwie **myLoadBalancer**.
* Pula frontonu o nazwie Moja **fronton**.
* Pula zaplecza o nazwie **myBackEndPool**.
* Skojarzone z **myVNet** sieci wirtualnej.
* Skojarzona z podsiecią zaplecza **myBackendSubnet**.

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

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.
* Włącz Resetowanie protokołu TCP.

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
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Maszyny wirtualne w puli zaplecza nie będą miały wychodzącej łączności z Internetem przy użyciu tej konfiguracji. </br> Aby uzyskać więcej informacji na temat zapewniania łączności wychodzącej, zobacz: </br> **[Połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md)**</br> Opcje zapewniania łączności: </br> **[Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](egress-only.md)** </br> **[Co to jest Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1**, **myNicVM2** i **myNicVM3**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

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
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network---basic"></a>Konfigurowanie sieci wirtualnej — podstawowe

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-createt):

* O nazwie **myVNet**.
* Prefiks adresu **10.1.0.0/16**.
* Podsieć o nazwie **myBackendSubnet**.
* Prefiks podsieci **10.1.0.0/24**.
* W grupie zasobów **CreateIntLBQS-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myBastionIP**.
* W **CreateIntLBQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Tworzenie podsieci bastionu

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć bastionu:

* O nazwie **AzureBastionSubnet**.
* Prefiks adresu **10.1.1.0/24**.
* W sieci wirtualnej **myVNet**.
* W grupie zasobów **CreateIntLBQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Utwórz hosta bastionu

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta bastionu:

* O nazwie **myBastionHost**.
* W **CreateIntLBQS-RG**.
* Skojarzone z publicznym adresem IP **myBastionIP**.
* Skojarzone z **myVNet** sieci wirtualnej.
* W lokalizacji **wschodniego** .

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* O nazwie **myNSGRuleHTTP**.
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce docelowe **(*)**.
* Port portu docelowego **80**.
* **Zezwalaj na** dostęp.
* Priorytet **200**.

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

## <a name="create-backend-servers---basic"></a>Tworzenie serwerów zaplecza — podstawowe

W tej sekcji utworzysz:

* Trzy interfejsy sieciowe dla maszyn wirtualnych.
* Zestaw dostępności dla maszyn wirtualnych
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create):

* Nazwane **myNicVM1**, **myNicVM2** i **myNicVM3**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

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

### <a name="create-availability-set-for-virtual-machines"></a>Utwórz zestaw dostępności dla maszyn wirtualnych

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* O nazwie **myAvailabilitySet**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Lokalizacja **Wschodnie**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create):

* Nazwane **myVM1**, **myVM2** i **myVM3**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsów sieciowych **myNicVM1**, **myNicVM2** i **myNicVM3**.
* **Win2019datacenter** obrazu maszyny wirtualnej.
* W **myAvailabilitySet**.


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

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* O nazwie **myLoadBalancer**.
* Pula frontonu o nazwie Moja **fronton**.
* Pula zaplecza o nazwie **myBackEndPool**.
* Skojarzone z **myVNet** sieci wirtualnej.
* Skojarzona z podsiecią zaplecza **myBackendSubnet**.

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

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.

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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1**, **myNicVM2** i **myNicVM3**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

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

### <a name="create-test-virtual-machine"></a>Utwórz testową maszynę wirtualną

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create):

* O nazwie **myNicTestVM**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az-vm-create):

* O nazwie **myTestVM**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicTestVM**.
* **Win2019Datacenter** obrazu maszyny wirtualnej.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Wdrożenie maszyny wirtualnej może potrwać kilka minut.

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

### <a name="test"></a>Test

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź prywatny adres IP dla modułu równoważenia obciążenia na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer**.

3. Zanotuj lub skopiuj adres obok **prywatnego adresu IP** w **omówieniu** **myLoadBalancer**.

4. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myTestVM** , która znajduje się w grupie zasobów **CreateIntLBQS-RG** .

5. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Tworzenie standardowego wewnętrznego modułu równoważenia obciążenia" border="true":::
   
Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS na maszynie wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) , aby usunąć grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przyjęto następujące założenia:

* Utworzono standardowy lub publiczny moduł równoważenia obciążenia
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do:
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)