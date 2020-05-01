---
title: 'Szybki Start: Tworzenie Load Balancer publicznego — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Load Balancer
description: W tym samouczku przedstawiono sposób tworzenia publicznego modułu równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477769"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Szybki Start: Tworzenie usługa Load Balancer w warstwie Standardowa równoważenia obciążenia maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start przedstawiono sposób tworzenia Load Balancer publicznego. W celu przetestowania modułu równoważenia obciążenia wdrożysz dwie maszyny wirtualne z systemem Ubuntu Server i zrównoważysz obciążenie aplikacji internetowej między dwiema maszynami wirtualnymi.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

W poniższym przykładzie zostanie utworzona grupa zasobów o nazwie *myResourceGroupSLB* w lokalizacji *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) , aby utworzyć strefę Standard nadmiarowy publiczny adres IP o nazwie *myPublicIP* w *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Aby utworzyć strefowy publiczny adres IP w strefie 1, użyj:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Użyj `-SKU Basic` , aby utworzyć podstawowy publiczny adres IP. Podstawowe publiczne adresy IP nie są zgodne z usługą równoważenia obciążenia w **warstwie Standardowa** . Firma Microsoft zaleca używanie **standardu** dla obciążeń produkcyjnych.

> [!IMPORTANT]
> W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.

## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia platformy Azure

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:
  - Adres IP frontonu, który odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  - Pula adresów IP zaplecza, gdzie pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  - Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  - Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz publiczną Azure Load Balancer za pomocą [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie **myLoadBalancer** , która zawiera pulę frontonu o nazwie Moja **fronton**, pulę zaplecza o nazwie **myBackEndPool** , która jest skojarzona z publicznym adresem IP **myPublicIP** utworzonym w poprzednim kroku. Użyj `--sku basic` , aby utworzyć podstawowy publiczny adres IP. Firma Microsoft zaleca użycie standardowej jednostki SKU dla obciążeń produkcyjnych.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą wysyłać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* za pomocą polecenia [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEnd* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną o nazwie *myVnet* z podsiecią o nazwie *mySubnet* w grupie zasobów *myResourceGroup* przy użyciu polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

W przypadku usługi Load Balancer w warstwie Standardowa maszyny wirtualne w adresie zaplecza muszą mieć karty sieciowe, które należą do sieciowej grupy zabezpieczeń. Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwalać na połączenia przychodzące za pośrednictwem portu 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>Tworzenie kart sieciowych

Utwórz trzy interfejsy sieciowe za pomocą polecenia [az network nic create](/cli/azure/network/nic#az-network-nic-create) i skojarz je z publicznym adresem IP i sieciową grupą zabezpieczeń. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz trzy maszyny wirtualne, które będą używane jako serwery zaplecza dla modułu równoważenia obciążenia. Aby sprawdzić, czy moduł równoważenia obciążenia został utworzony pomyślnie, zainstalujesz także serwer NGINX na maszynach wirtualnych.

Jeśli tworzysz podstawową Load Balancer z podstawowym publicznym adresem IP, musisz utworzyć zestaw dostępności za pomocą ([AZ VM availabilityset Create](/cli/azure/network/nic) , aby dodać maszyny wirtualne do programu. Standardowe usługi równoważenia obciążenia nie wymagają tego dodatkowego kroku. Firma Microsoft zaleca używanie standardu.

### <a name="create-three-virtual-machines"></a>Tworzenie trzech maszyn wirtualnych

Aby zainstalować serwer NGINX i uruchomić aplikację Node.js „Hello World” na maszynie wirtualnej z systemem Linux, możesz użyć pliku konfiguracji cloud-init. W bieżącej powłoce utwórz plik o nazwie cloud-init.txt, a następnie skopiuj i wklej poniższą konfigurację do powłoki. Upewnij się, że kopiujesz cały plik cloud-init, a szczególnie pierwszy wiersz:

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

Utwórz maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Wdrażanie maszyn wirtualnych może potrwać kilka minut.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Aby uzyskać publiczny adres IP modułu równoważenia obciążenia, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Testowanie modułu równoważenia obciążenia](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, możesz użyć polecenia [az group delete](/cli/azure/group#az-group-delete), aby usunąć grupę zasobów, moduł równoważenia obciążenia oraz wszystkie pokrewne zasoby.

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start utworzono usługa Load Balancer w warstwie Standardowa, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu Load Balancer, sondę kondycji, a następnie przetestowano Load Balancer. Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do [Azure Load Balancer samouczków](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Dowiedz się więcej na temat [stref Load Balancer i dostępności](load-balancer-standard-availability-zones.md).