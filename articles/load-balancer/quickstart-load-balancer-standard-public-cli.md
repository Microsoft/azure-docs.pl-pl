---
title: 'Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: W tym samouczku przedstawiono sposób tworzenia publicznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: c66ecceea770ec32e907a9bdc21fff29cf6aa453
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698516"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Szybki start: tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć publiczny moduł równoważenia obciążenia i trzy maszyny wirtualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* O nazwie **CreatePubLBQS-RG**. 
* W lokalizacji **wschodniego** .

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* O nazwie **myVNet**.
* Prefiks adresu **10.1.0.0/16**.
* Podsieć o nazwie **myBackendSubnet**.
* Prefiks podsieci **10.1.0.0/24**.
* W grupie zasobów **CreatePubLBQS-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* O nazwie **myNSGRuleHTTP**.
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce docelowe **(*)**.
* Port portu docelowego **80**.
* **Zezwalaj na** dostęp.
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* O nazwie **myNicVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Plik konfiguracji chmury o nazwie **cloud-init.txt** dla konfiguracji serwera.
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.

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

Utwórz maszyny wirtualne za pomocą [AZ VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>Maszyna wirtualna 1
* O nazwie **myVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **Strefa 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O nazwie **myVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* O nazwie **myVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM3**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Wdrożenie maszyn wirtualnych może potrwać kilka minut.

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myPublicIP**.
* W **CreatePubLBQS-RG**.

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

## <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* O nazwie **myLoadBalancer**.
* Pula frontonu o nazwie Moja **fronton**.
* Pula zaplecza o nazwie **myBackEndPool**.
* Skojarzone z publicznym adresem IP **myPublicIP** utworzonym w poprzednim kroku. 

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

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.
* Włącz Resetowanie protokołu TCP.


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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):

#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM3** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Utwórz konfigurację reguły ruchu wychodzącego
Reguły ruchu wychodzącego modułu równoważenia obciążenia Skonfiguruj wychodzące pliki zasad sieciowych dla maszyn wirtualnych w puli zaplecza. 

Aby uzyskać więcej informacji na temat połączeń wychodzących, zobacz [połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Tworzenie wychodzącego publicznego adresu IP lub publicznego prefiksu adresu IP.

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby utworzyć pojedynczy adres IP dla połączenia wychodzącego.  

Użyj [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) , aby utworzyć publiczny prefiks IP dla połączenia wychodzącego.

Aby uzyskać więcej informacji na temat skalowania ruchu wychodzącego NAT i łączności wychodzącej, zobacz [skalowanie ruchu NAT z wieloma adresami IP](load-balancer-outbound-connections.md).

#### <a name="public-ip"></a>Publiczny adres IP

* O nazwie **myPublicIPOutbound**.
* W **CreatePubLBQS-RG**.

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
#### <a name="public-ip-prefix"></a>Prefiks publicznego adresu IP

* O nazwie **myPublicIPPrefixOutbound**.
* W **CreatePubLBQS-RG**.
* Długość prefiksu **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Aby utworzyć strefowo nadmiarowy publiczny adres IP w Strefa 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Utwórz konfigurację wychodzącego adresu IP frontonu

Utwórz nową konfigurację adresu IP frontonu za pomocą [AZ Network lb fronton-IP Create ](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Wybierz polecenia publicznego adresu IP lub publicznego prefiksu adresu IP w oparciu o decyzję w poprzednim kroku.

#### <a name="public-ip"></a>Publiczny adres IP

* O nazwie **myFrontEndOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzone z publicznym adresem IP **myPublicIPOutbound**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefiks publicznego adresu IP

* O nazwie **myFrontEndOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzone z publicznym prefiksem adresu IP **myPublicIPPrefixOutbound**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Utwórz pulę wychodzącą

Utwórz nową pulę wychodzącą za pomocą [AZ Network lb Address-Pool Create](/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create):

* O nazwie **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Utwórz regułę ruchu wychodzącego

Utwórz nową regułę ruchu wychodzącego dla puli zaplecza wychodzącego za pomocą [AZ Network lb wychodzącej-Rule Create](/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* O nazwie **myOutboundRule**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**
* Skojarzone z **myFrontEndOutbound** frontonu.
* **Wszystkie** protokoły.
* Limit czasu bezczynności równy **15**.
* **10000** portów wychodzących.
* Skojarzona z pulą zaplecza **myBackEndPoolOutbound**.

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
### <a name="add-virtual-machines-to-outbound-pool"></a>Dodawanie maszyn wirtualnych do puli wychodzącej

Dodaj maszyny wirtualne do puli wychodzącej za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM3** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* O nazwie **myVNet**.
* Prefiks adresu **10.1.0.0/16**.
* Podsieć o nazwie **myBackendSubnet**.
* Prefiks podsieci **10.1.0.0/24**.
* W grupie zasobów **CreatePubLBQS-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi równoważenia obciążenia w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć interfejsy sieciowe należące do sieciowej grupy zabezpieczeń. 

Utwórz sieciową grupę zabezpieczeń za pomocą polecenia [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* O nazwie **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu polecenia [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* O nazwie **myNSGRuleHTTP**.
* W sieciowej grupie zabezpieczeń utworzonej w poprzednim kroku **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Źródło **(*)**.
* Miejsce docelowe **(*)**.
* Port portu docelowego **80**.
* **Zezwalaj na** dostęp.
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

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Utwórz interfejsy sieciowe dla maszyn wirtualnych

Utwórz trzy interfejsy sieciowe za pomocą [AZ Network nic Create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* O nazwie **myNicVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz:

* Plik konfiguracji chmury o nazwie **cloud-init.txt** dla konfiguracji serwera. 
* Zestaw dostępności dla maszyn wirtualnych
* Trzy maszyny wirtualne, które mają być używane jako serwery zaplecza dla modułu równoważenia obciążenia.


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

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* O nazwie **myAvSet**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Lokalizacja **Wschodnie**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne za pomocą [AZ VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>Maszyna wirtualna 1
* O nazwie **myVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W obszarze zestaw dostępności **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>Maszyna wirtualna 2
* O nazwie **myVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* O nazwie **myVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM3**.
* **UbuntuLTS** obrazu maszyny wirtualnej.
* Plik konfiguracji **cloud-init.txt** utworzony w kroku powyżej.
* W **strefa 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Wdrożenie maszyn wirtualnych może potrwać kilka minut.


## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myPublicIP**.
* W **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer-resource"></a>Tworzenie zasobu modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą [AZ Network lb Create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* O nazwie **myLoadBalancer**.
* Pula frontonu o nazwie Moja **fronton**.
* Pula zaplecza o nazwie **myBackEndPool**.
* Skojarzone z publicznym adresem IP **myPublicIP** utworzonym w poprzednim kroku. 

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

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą [AZ Network lb Probe Create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Utwórz regułę modułu równoważenia obciążenia za pomocą [AZ Network lb Rule Create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.

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

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza modułu równoważenia obciążenia

Dodaj maszyny wirtualne do puli zaplecza za pomocą [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* W puli adresów zaplecza **myBackEndPool**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM3** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Aby uzyskać publiczny adres IP modułu równoważenia obciążenia, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testowanie modułu równoważenia obciążenia" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) , aby usunąć grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start

* Utworzono standardowy lub publiczny moduł równoważenia obciążenia
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do..
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)