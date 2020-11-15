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
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 51f02d9f059ef87f7c1627102508b8b2aa7f537a
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636303"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć publiczny moduł równoważenia obciążenia i trzy maszyny wirtualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

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

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

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
### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

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

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Interfejsy sieciowe dla serwerów wewnętrznej bazy danych.
* Plik konfiguracji chmury o nazwie **cloud-init.txt** dla konfiguracji serwera.
* Dwie maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz dwa interfejsy sieciowe za pomocą [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

### <a name="create-cloud-init-configuration-file"></a>Tworzenie pliku konfiguracji Cloud-init

Użyj pliku konfiguracji Cloud-init, aby zainstalować NGINX i uruchomić aplikację Node.js "Hello world" na maszynie wirtualnej z systemem Linux. 

W bieżącej powłoce Utwórz plik o nazwie cloud-init.txt. Skopiuj i wklej następującą konfigurację do powłoki. Upewnij się, że cały plik Cloud-init został skopiowany prawidłowo, szczególnie w pierwszym wierszu:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>Maszyna wirtualna 1
* O nazwie **myVM1**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **Strefa 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O nazwie **myVM2**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Wdrożenie maszyn wirtualnych może potrwać kilka minut.

## <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

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

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

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

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

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
>Maszyny wirtualne w puli zaplecza nie będą miały wychodzącej łączności z Internetem przy użyciu tej konfiguracji. </br> Aby uzyskać więcej informacji na temat zapewniania łączności wychodzącej, zobacz: </br> **[Połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md)**</br> Opcje zapewniania łączności: </br> **[Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](egress-only.md)** </br> **[Co to jest Virtual Network translator adresów sieciowych?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i wdrożeniem modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

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
### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz dwa interfejsy sieciowe za pomocą [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreateIntLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Plik konfiguracji chmury o nazwie **cloud-init.txt** dla konfiguracji serwera. 
* Zestaw dostępności dla maszyn wirtualnych
* Dwie maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.

Aby sprawdzić, czy moduł równoważenia obciążenia został pomyślnie utworzony, należy zainstalować NGINX na maszynach wirtualnych.

### <a name="create-cloud-init-configuration-file"></a>Tworzenie pliku konfiguracji Cloud-init

Użyj pliku konfiguracji Cloud-init, aby zainstalować NGINX i uruchomić aplikację Node.js "Hello world" na maszynie wirtualnej z systemem Linux. 

W bieżącej powłoce Utwórz plik o nazwie cloud-init.txt. Skopiuj i wklej następującą konfigurację do powłoki. Upewnij się, że cały plik Cloud-init został skopiowany prawidłowo, szczególnie w pierwszym wierszu:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-availability-set-for-virtual-machines"></a>Utwórz zestaw dostępności dla maszyn wirtualnych

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* O nazwie **myAvSet**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Lokalizacja **Wschodnie**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>Maszyna wirtualna 1
* O nazwie **myVM1**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W obszarze zestaw dostępności **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O nazwie **myVM2**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Wdrożenie maszyn wirtualnych może potrwać kilka minut.

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

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

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

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

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

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

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

### <a name="create-azure-bastion-public-ip"></a>Utwórz publiczny adres IP usługi Azure bastionu

Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myBastionIP**.
* W **CreateIntLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Utwórz podsieć usługi Azure bastionu

Użyj [AZ Network VNET Subnet Create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) , aby utworzyć podsieć:

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

### <a name="create-azure-bastion-host"></a>Tworzenie hosta usługi Azure bastionu
Użyj [AZ Network bastionu Create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) , aby utworzyć hosta bastionu:

* O nazwie **myBastionHost**
* W **CreateIntLBQS — RG**
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
Wdrożenie hosta bastionu może potrwać kilka minut.

### <a name="create-test-virtual-machine"></a>Utwórz testową maszynę wirtualną

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

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
Utwórz maszynę wirtualną za pomocą [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* O nazwie **myTestVM**.
* W grupie zasobów **CreateIntLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicTestVM**.
* **Win2019Datacenter** obrazu maszyny wirtualnej.
* Wybierz wartości dla **\<adminpass>** i **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Wdrożenie maszyny wirtualnej może potrwać kilka minut.

### <a name="test"></a>Testowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź prywatny adres IP dla modułu równoważenia obciążenia na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby** , a następnie wybierz pozycję **myLoadBalancer**.

2. Zanotuj lub skopiuj adres obok **prywatnego adresu IP** w **omówieniu** **myLoadBalancer**.

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby** , a następnie na liście zasobów wybierz pozycję **myTestVM** , która znajduje się w grupie zasobów **CreateIntLBQS-RG** .

4. Na stronie **Przegląd** wybierz opcję **Połącz** , a następnie **bastionu**.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Tworzenie standardowego wewnętrznego modułu równoważenia obciążenia" border="true":::
   
Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS na maszynie wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) , aby usunąć grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start

* Utworzono standardowy lub publiczny moduł równoważenia obciążenia
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do 
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)