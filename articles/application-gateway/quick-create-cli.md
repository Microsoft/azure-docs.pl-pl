---
title: 'Szybki Start: bezpośredni ruch internetowy przy użyciu interfejsu wiersza polecenia'
titleSuffix: Azure Application Gateway
description: W tym przewodniku szybki start dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Application Gateway platformy Azure, która kieruje ruch sieci Web do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1a691d8ab212dd436b0dc9f7aafbc19a406b12b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98601787"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure

W tym przewodniku szybki start utworzysz bramę aplikacji za pomocą interfejsu wiersza polecenia platformy Azure. Następnie przetestuj go, aby upewnić się, że działa prawidłowo. 

Aplikacja Application Gateway kieruje ruch sieci Web aplikacji do określonych zasobów w puli zaplecza. Można przypisywać odbiorniki do portów, tworzyć reguły i dodawać zasoby do puli zaplecza. Dla uproszczenia w tym artykule użyto prostej konfiguracji z publicznym adresem IP frontonu, podstawowym odbiornikiem do hostowania pojedynczej lokacji bramy aplikacji, podstawowej reguły routingu żądań i dwóch maszyn wirtualnych w puli zaplecza.

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure PowerShell](quick-create-powershell.md) lub [Azure Portal](quick-create-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia `az group create` . 

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Można utworzyć nową podsieć dla Application Gateway lub użyć istniejącej. W tym przykładzie utworzysz dwie podsieci: jeden dla bramy Application Gateway i drugi dla serwerów wewnętrznej bazy danych. Można skonfigurować adres IP frontonu Application Gateway publiczny lub prywatny zgodnie z Twoim przypadkiem użycia. W tym przykładzie wybrano publiczny adres IP frontonu.

Aby utworzyć sieć wirtualną i podsieć, użyj programu `az network vnet create` . Uruchom `az network public-ip create` , aby utworzyć publiczny adres IP.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>Tworzenie serwerów zaplecza

Zaplecze mogą mieć karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowane nazwy domen (FQDN) i wielodostępne zaplecza, takie jak Azure App Service. W tym przykładzie utworzysz dwie maszyny wirtualne, które mają być używane jako serwery zaplecza dla bramy aplikacji. Usługi IIS są również instalowane na maszynach wirtualnych w celu przetestowania bramy aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

Zainstaluj serwer internetowy NGINX na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona. Aby zainstalować serwer NGINX i uruchomić aplikację Node.js „Hello World” na maszynie wirtualnej z systemem Linux, możesz użyć pliku konfiguracji cloud-init. Aby uzyskać więcej informacji na temat pliku cloud-init, zobacz [Cloud-init support for virtual machines in Azure (Obsługa pliku cloud-init na potrzeby maszyn wirtualnych na platformie Azure)](../virtual-machines/linux/using-cloud-init.md).

W usłudze Azure Cloud Shell skopiuj i wklej następującą konfigurację do pliku o nazwie *cloud-init.txt*. Wprowadź ciąg *editor cloud-init.txt* w celu utworzenia pliku.

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

Utwórz interfejsy sieciowe za pomocą programu `az network nic create` . Aby utworzyć maszyny wirtualne, użyj programu `az vm create` .

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji przy użyciu programu `az network application-gateway create` . Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Następnie platforma Azure dodaje prywatne adresy IP interfejsów sieciowych jako serwery w puli zaplecza bramy aplikacji.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Tworzenie bramy aplikacji na platformie Azure może potrwać do 30 minut. Po jej utworzeniu można wyświetlić następujące ustawienia w sekcji **Ustawienia** strony **Application Gateway**:

- **appGatewayBackendPool**: znajduje się na stronie **Pule zaplecza** . Określa wymaganą pulę zaplecza.
- **appGatewayBackendHttpSettings**: znajduje się na stronie **ustawień http** . Określa, że brama aplikacji używa portu 80 i protokołu HTTP na potrzeby komunikacji.
- **appGatewayHttpListener**: znajduje się na **stronie odbiorników**. Określa domyślny odbiornik skojarzony z pulą **appGatewayBackendPool**.
- **appGatewayFrontendIP**: znajduje się na stronie **konfiguracje adresów IP frontonu** . Przypisuje *myAGPublicIPAddress* do **appGatewayHttpListener**.
- **RULE1**: znajduje się na stronie **reguł** . Określa domyślną regułę rozsyłania skojarzoną z odbiornikiem **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że platforma Azure nie wymaga internetowego serwera NGINX do utworzenia bramy aplikacji, zainstalowano go w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Aby uzyskać publiczny adres IP nowej bramy aplikacji, użyj `az network public-ip show` . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki.
    
![Testowanie bramy aplikacji](./media/quick-create-cli/application-gateway-nginxtest.png)

Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa drugiej maszyny wirtualnej. Oznacza to, że Brama aplikacji została pomyślnie utworzona i może nawiązać połączenie z zapleczem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu bramy aplikacji, użyj `az group delete` polecenia, aby usunąć grupę zasobów. Po usunięciu grupy zasobów należy również usunąć bramę aplikacji i wszystkie powiązane z nią zasoby.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)

