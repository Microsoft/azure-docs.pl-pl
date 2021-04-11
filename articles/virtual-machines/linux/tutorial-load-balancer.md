---
title: Samouczek — Równoważenie obciążenia maszyn wirtualnych z systemem Linux na platformie Azure
description: Z tego samouczka dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure można utworzyć moduł równoważenia obciążenia dla bezpiecznej aplikacji o wysokiej dostępności na trzech maszynach wirtualnych z systemem Linux
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 433bbd51618cfb5624c8ed2c549e1793488f0e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553769"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Samouczek: równoważenie obciążenia maszyn wirtualnych z systemem Linux na platformie Azure w celu utworzenia aplikacji o wysokiej dostępności za pomocą interfejsu wiersza polecenia platformy Azure

Równoważenie obciążenia zwiększa dostępność dzięki rozdzieleniu żądań przychodzących pomiędzy wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki modułu równoważenia obciążenia platformy Azure, które dystrybuują ruch i zapewniają wysoką dostępność. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie modułu równoważenia obciążenia na platformie Azure
> * Tworzenie sondy kondycji modułu równoważenia obciążenia
> * Tworzenie reguł ruchu modułu równoważenia obciążenia
> * Używanie pliku cloud-init do tworzenia podstawowej aplikacji Node.js
> * Tworzenie maszyn wirtualnych i dołączanie ich do modułu równoważenia obciążenia
> * Wyświetlanie działającego modułu równoważenia obciążenia
> * Dodawanie i usuwanie maszyn wirtualnych w module równoważenia obciążenia

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Omówienie modułu równoważenia obciążenia platformy Azure
Moduł równoważenia obciążenia platformy Azure jest modułem w warstwie 4 (TCP, UDP), który zapewnia wysoką dostępność, rozkładając ruch przychodzący na maszyny wirtualne w dobrej kondycji. Sonda kondycji modułu równoważenia obciążenia monitoruje określony port na każdej maszynie wirtualnej i dystrybuuje ruch tylko do działającej maszyny wirtualnej.

Zdefiniuj konfigurację IP frontonu z co najmniej jednym publicznym adresem IP. Ta konfiguracja frontonu zapewnia dostęp do aplikacji i modułu równoważenia obciążenia za pośrednictwem Internetu. 

Maszyny wirtualne łączą się z modułem równoważenia obciążenia za pośrednictwem wirtualnej karty sieciowej. Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia.

Aby sterować przepływem ruchu, zdefiniuj reguły równoważenia obciążenia dla poszczególnych portów i protokołów mapowanych na Twoje maszyny wirtualne.

Jeśli został wykonany poprzedni samouczek omawiający [tworzenie zestawu skalowania maszyn wirtualnych](tutorial-create-vmss.md), masz już utworzony moduł równoważenia obciążenia. Wszystkie te składniki zostały już skonfigurowane jako część zestawu skalowania.


## <a name="create-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure
W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania poszczególnych składników modułu równoważenia obciążenia. Zanim będzie można utworzyć własny moduł równoważenia obciążenia, należy utworzyć grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie zostanie utworzona grupa zasobów o nazwie *myResourceGroupLoadBalancer* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). W poniższym przykładzie zostanie utworzony publiczny adres IP o nazwie *myPublicIP* w grupie zasobów *myResourceGroupLoadBalancer*:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Utwórz moduł równoważenia obciążenia za pomocą polecenia [az network lb create](/cli/azure/network/lb). Poniższy przykład tworzy moduł równoważenia obciążenia o nazwie *myLoadBalancer* i przypisuje adres *myPublicIP* do konfiguracji protokołu IP frontonu:

```azurecli-interactive
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Domyślnie maszyna wirtualna jest wykluczana z dystrybucji ruchu przez moduł równoważenia obciążenia, jeśli dwie kolejne próby podejmowane w 15-sekundowych odstępach zakończą się niepowodzeniem. Sonda kondycji jest tworzona z użyciem protokołu lub konkretnej strony kontroli kondycji aplikacji. 

W poniższym przykładzie zostanie utworzona sonda TCP. Możesz także tworzyć niestandardowe sondy HTTP na potrzeby bardziej szczegółowych kontroli kondycji. W przypadku użycia niestandardowej sondy HTTP należy utworzyć stronę kontroli kondycji, na przykład *healthcheck.js*. Aby dany host pozostał w rotacji, sonda musi zwrócić do modułu równoważenia obciążenia kod odpowiedzi **HTTP 200 OK**.

Aby utworzyć sondę kondycji TCP, należy użyć polecenia [az network lb probe create](/cli/azure/network/lb/probe). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby mieć pewność, że ruch będzie kierowany tylko do maszyn wirtualnych w dobrej kondycji, zdefiniuj również sondę kondycji do użycia.

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie zostanie utworzona reguła o nazwie *myLoadBalancerRule*, która używa sondy kondycji o nazwie *myHealthProbe* i równoważy ruch na porcie *80*:

```azurecli-interactive
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej
Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej. Więcej informacji na temat sieci wirtualnych zawiera samouczek [Manage Azure Virtual Networks (Zarządzanie sieciami wirtualnymi platformy Azure)](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią o nazwie *mySubnet*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Aby dodać sieciową grupę zabezpieczeń, użyj polecenia [az network nsg create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli-interactive
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Utwórz regułę sieciowej grupy zabezpieczeń za pomocą polecenia [az network nsg rule create](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę sieciowej grupy zabezpieczeń o nazwie *myNetworkSecurityGroupRule*:

```azurecli-interactive
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Wirtualne karty sieciowe są tworzone za pomocą polecenia [az network nic create](/cli/azure/network/nic). W poniższym przykładzie zostaną utworzone trzy wirtualne karty sieciowe. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurecli
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Gdy wszystkie trzy wirtualne karty sieciowe zostaną utworzone, przejdź do kolejnego kroku


## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

### <a name="create-cloud-init-config"></a>Tworzenie pliku konfiguracji cloud-init
W poprzednim samouczku dotyczącym [sposobu dostosowywania maszyny wirtualnej systemu Linux przy pierwszym uruchomieniu](tutorial-automate-vm-deployment.md) przedstawiono proces automatyzowania dostosowywania maszyny wirtualnej przy użyciu pliku cloud-init. Tego samego pliku konfiguracji cloud-init można użyć do zainstalowania aparatu NGINX i uruchomienia prostej aplikacji Node.js: „Hello World” w następnym kroku. Aby zobaczyć moduł równoważenia obciążenia w akcji, na końcu samouczka uzyskasz dostęp do tej prostej aplikacji w przeglądarce internetowej.

W bieżącej powłoce utwórz plik o nazwie *cloud-init.txt* i wklej poniższą konfigurację. Na przykład utwórz plik w usłudze Cloud Shell, a nie na maszynie lokalnej. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

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
Aby poprawić wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności. Aby uzyskać więcej informacji na temat zestawów dostępności, zobacz poprzedni samouczek [How to create highly available virtual machines (Tworzenie maszyn wirtualnych o wysokiej dostępności)](tutorial-availability-sets.md).

Utwórz zestaw dostępności za pomocą [AZ VM Availability-Set Create](/cli/azure/vm/availability-set). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Teraz możesz utworzyć maszyny wirtualne za pomocą polecenia [az vm create](/cli/azure/vm). W poniższym przykładzie pokazano tworzenie trzech maszyn wirtualnych i generowanie kluczy SSH, jeśli jeszcze nie istnieją:

```azurecli
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Parametr `--no-wait` nie czeka na zakończenie wszystkich zadań. Może upłynąć kilka minut, zanim będzie można uzyskać dostęp do aplikacji. Sonda kondycji modułu równoważenia obciążenia automatycznie wykrywa, gdy aplikacja jest uruchomiona na każdej maszynie wirtualnej. Gdy aplikacja jest uruchomiona, reguła modułu równoważenia obciążenia rozpoczyna dystrybucję ruchu.


## <a name="test-load-balancer"></a>Testowanie modułu równoważenia obciążenia
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Pamiętaj — kilka minut trwa przygotowanie maszyn wirtualnych zanim moduł równoważenia obciążenia rozpocznie dystrybucję ruchu do nich. Zostanie wyświetlona aplikacja z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Uruchamianie aplikacji Node.js](./media/tutorial-load-balancer/running-nodejs-app.png)

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między trzy maszyny wirtualne używane przez aplikację, możesz wymusić odświeżenie w przeglądarce internetowej.


## <a name="add-and-remove-vms"></a>Dodawanie i usuwanie maszyn wirtualnych
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. W tej sekcji pokazano, jak usunąć lub dodać maszyny wirtualne w module równoważenia obciążenia.

### <a name="remove-a-vm-from-the-load-balancer"></a>Usuwanie maszyny wirtualnej z modułu równoważenia obciążenia
Maszynę wirtualną możesz usunąć z puli adresów zaplecza za pomocą polecenia [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool). Poniższy przykład usuwa wirtualną kartę sieciową dla maszyny **myVM2** z modułu *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między pozostałe dwie maszyny wirtualne używane przez aplikację, możesz wymusić odświeżenie w przeglądarce internetowej. Możesz teraz wykonać czynności konserwacyjne na maszynie wirtualnej, na przykład zainstalować aktualizacje systemu operacyjnego lub ponownie uruchomić maszynę wirtualną.

Aby wyświetlić listę maszyn wirtualnych z wirtualnymi kartami sieciowymi podłączonymi do modułu równoważenia obciążenia, użyj polecenia [az network lb address-pool show](/cli/azure/network/lb/address-pool). Zapytania i filtrowanie według identyfikatora wirtualnej karty sieciowej należy wykonać w następujący sposób:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

Dane wyjściowe są podobne do poniższego przykładu, który pokazuje, że wirtualna karta sieciowa dla maszyny wirtualnej 2 nie jest już częścią puli adresów zaplecza:

```output
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Dodawanie maszyny wirtualnej do modułu równoważenia obciążenia
Po przeprowadzeniu konserwacji maszyny wirtualnej lub jeśli trzeba zwiększyć pojemność, możesz dodać maszynę wirtualną do puli adresów zaplecza za pomocą polecenia [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool). Poniższy przykład dodaje wirtualną kartę sieciową dla maszyny **myVM2** do modułu *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Aby sprawdzić, czy wirtualna karta sieciowa jest włączona do puli adresów zaplecza, ponownie użyj polecenia [az network lb address-pool show](/cli/azure/network/lb/address-pool) z poprzedniego kroku.


## <a name="next-steps"></a>Następne kroki
Podczas pracy z tym samouczkiem utworzono moduł równoważenia obciążenia i dołączono do niego maszyny wirtualne. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie modułu równoważenia obciążenia na platformie Azure
> * Tworzenie sondy kondycji modułu równoważenia obciążenia
> * Tworzenie reguł ruchu modułu równoważenia obciążenia
> * Używanie pliku cloud-init do tworzenia podstawowej aplikacji Node.js
> * Tworzenie maszyn wirtualnych i dołączanie ich do modułu równoważenia obciążenia
> * Wyświetlanie działającego modułu równoważenia obciążenia
> * Dodawanie i usuwanie maszyn wirtualnych w module równoważenia obciążenia

Przejdź do następnego samouczka, aby dowiedzieć się więcej o składnikach sieci wirtualnej platformy Azure.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi i sieciami wirtualnymi](tutorial-virtual-network.md)
