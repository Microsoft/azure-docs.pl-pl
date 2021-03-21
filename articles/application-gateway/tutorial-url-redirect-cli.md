---
title: 'Samouczek: przekierowywanie oparte na ścieżce URL przy użyciu interfejsu wiersza polecenia'
titleSuffix: Azure Application Gateway
description: Z tego samouczka dowiesz się, jak utworzyć bramę aplikacji z obsługą przekierowywania ruchu na podstawie ścieżki URL za pomocą interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: aa247d868e19831b3e6f1c3f68e605fb209a0664
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218319"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Samouczek: tworzenie bramy aplikacji z przekierowywaniem na podstawie ścieżki URL za pomocą interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure podczas tworzenia [bramy aplikacji](./overview.md) możesz skonfigurować [reguły routingu oparte na ścieżkach URL](tutorial-url-route-cli.md). Podczas pracy z tym samouczkiem utworzysz pule zaplecza z użyciem [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md). Następnie utworzysz reguły routingu na podstawie adresów URL, aby zapewnić przekierowywanie ruchu internetowego do odpowiedniej puli zaplecza.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodawanie odbiorników i reguł routingu
> * Tworzenie zestawów skalowania maszyn wirtualnych dla pul zaplecza

Poniższy przykład przedstawia kierowanie ruchu w witrynie, przychodzącego z portów 8080 i 8081, do tych samych pul zaplecza:

![Przykład routingu adresów URL](./media/tutorial-url-redirect-cli/scenario.png)

Jeśli chcesz, możesz wykonać kroki tego samouczka przy użyciu [programu Azure PowerShell](tutorial-url-redirect-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie możesz dodać podsieć o nazwie *myBackendSubnet* wymaganą przez serwery zaplecza przy użyciu polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip).

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
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Użyj [AZ Network Application-Gateway Create](/cli/azure/network/application-gateway) , aby utworzyć bramę aplikacji o nazwie myAppGateway. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Dodawanie pul zaplecza i portów

Korzystając z polecenia [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool), możesz dodać do bramy aplikacji pule adresów zaplecza o nazwach *imagesBackendPool* i *videoBackendPool*. Porty frontonu możesz dodać do pul za pomocą polecenia [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Dodawanie odbiorników i reguł

### <a name="add-listeners"></a>Dodawanie odbiorników

Dodaj odbiorniki zaplecza o nazwach *backendListener* i *redirectedListener*, które są wymagane do kierowania ruchu, używając polecenia [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Dodawanie domyślnej mapy ścieżek URL

Mapy ścieżek adresów URL upewnij się, że określone adresy URL są kierowane do określonych pul zaplecza. Możesz utworzyć mapy ścieżek URL o nazwach *imagePathRule* i *videoPathRule* przy użyciu poleceń [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) i [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Korzystając z polecenia [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config), możesz skonfigurować przekierowanie dla odbiornika.

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Dodawanie mapy ścieżek URL na potrzeby przekierowywania

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Dodawanie reguł routingu

Reguły routingu kojarzą mapy ścieżek URL z utworzonymi odbiornikami. Przy użyciu polecenia [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) możesz dodać reguły o nazwach *defaultRule* i *redirectedRule*.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech utworzonych pul zaplecza. Utworzone zestawy skalowania będą miały nazwy *myvmss1*, *myvmss2* i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się serwer NGINX.

Zastąp \<azure-user> \<password> wartości i nazwą użytkownika i wybranym przez siebie hasłem.

```azurecli
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username <azure-user> \
    --admin-password <password> \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak,,, `http://40.121.222.19` `http://40.121.222.19:8080/images/test.htm` `http://40.121.222.19:8080/video/test.htm` lub `http://40.121.222.19:8081/images/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Zmień adres URL na http:// &lt; IP-Address &gt; : 8080/images/test.html, zastępując adres IP adresem &lt; IP &gt; i powinien wyglądać podobnie do poniższego przykładu:

![Testowanie adresu URL obrazów w bramie aplikacji](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Zmień adres URL na http:// &lt; IP-Address &gt; : 8080/video/test.html, zastępując adres IP adresem &lt; IP &gt; i powinien wyglądać podobnie do poniższego przykładu:

![Testowanie adresu URL wideo w bramie aplikacji](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Teraz zmień adres URL na http:// &lt; IP-Address &gt; : 8081/images/test.htm, zastępując adres IP adresem &lt; IP &gt; i powinien zostać wyświetlony ruch przekierowany z powrotem do puli zaplecza obrazów pod adresem IP http:// &lt; &gt; : 8080/images.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat funkcji bramy aplikacji](./overview.md)