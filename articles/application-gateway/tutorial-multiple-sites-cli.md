---
title: Hostuj wiele witryn internetowych przy użyciu interfejsu wiersza polecenia
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji hostującą wiele witryn internetowych przy użyciu interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cb924ab1f8947fefc83ed35a409628a576fad4b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772683"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>tworzenie bramy aplikacji hostującej wiele witryn internetowych przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure podczas tworzenia [bramy aplikacji](overview.md) możesz [skonfigurować hostowanie wielu witryn internetowych](multiple-site-overview.md). W tym artykule zdefinisz pule adresów zaplecza przy użyciu zestawów skalowania maszyn wirtualnych. Następnie, bazując na należących do Ciebie domenach, skonfigurujesz odbiorniki i reguły, aby się upewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach. W tym artykule założono, że jesteś właścicielem wielu domen i korzystasz z przykładów *www \. contoso.com* *i www \. fabrikam.com.*

W tym artykule omówiono sposób wykonywania następujących zadań:

* Konfigurowanie sieci
* Tworzenie bramy aplikacji
* Tworzenie odbiorników zaplecza
* Tworzenie reguł routingu
* Tworzenie zestawów skalowania maszyn wirtualnych z pulami zaplecza
* Tworzenie rekordu CNAME w domenie

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="Wiele lokacji Application Gateway":::

Jeśli wolisz, możesz wykonać tę procedurę przy [użyciu](tutorial-multiple-sites-powershell.md)Azure PowerShell .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz sieć wirtualną i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie możesz dodać podsieć wymaganą przez serwery zaplecza przy użyciu polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip).

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

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Aby utworzyć bramę aplikacji, możesz użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create). Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*. 

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

### <a name="add-the-backend-pools"></a>Dodawanie pul zaplecza

Dodaj pule zaplecza, które są potrzebne do zawierania serwerów zaplecza, za pomocą [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az_network_application_gateway_address-pool_create)
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Dodawanie odbiorników

Dodaj odbiorniki, które są potrzebne do rozsyłania ruchu, używając [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create).

>[!NOTE]
> Za Application Gateway SKU lub WAF v2 można również skonfigurować maksymalnie 5 nazw hostów na odbiornik i używać symboli wieloznacznych w nazwie hosta. Zobacz nazwy [hostów z symbolami wieloznacznymi w odbiorniku,](multiple-site-overview.md#wildcard-host-names-in-listener-preview) aby uzyskać więcej informacji.
>Aby użyć wielu nazw hostów i symboli wieloznacznych w odbiorniku przy użyciu interfejsu wiersza polecenia platformy Azure, należy użyć `--host-names` polecenia zamiast `--host-name` . W przypadku nazw hostów można wspomnieć maksymalnie o pięciu nazwach hostów jako wartościach rozdzielonych spacjami. Na przykład `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Dodawanie reguł routingu

Reguły są przetwarzane w podanej kolejności. Ruch jest przekierowyny przy użyciu pierwszej reguły, która pasuje niezależnie od specyfiki. Na przykład jeśli na tym samym porcie utworzono dwie reguły: jedną przy użyciu odbiornika podstawowego, a drugą przy użyciu odbiornika obejmującego wiele witryn, reguła z odbiornikiem obejmującym wiele witryn musi znajdować się przed regułą z odbiornikiem podstawowym, aby funkcja reguły obejmującej wiele witryn działała zgodnie z oczekiwaniami. 

W tym przykładzie utworzysz dwie nowe reguły i usuniesz regułę domyślną utworzoną podczas wdrażania bramy aplikacji. Regułę możesz dodać przy użyciu polecenia [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech pul zaplecza w bramie aplikacji. Utworzone zestawy skalowania będą miały nazwy *myvmss1*, *myvmss2* i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się usługi IIS.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Tworzenie rekordu CNAME w domenie

Po utworzeniu bramy aplikacji z publicznym adresem IP można pobrać adres DNS i użyć go w celu utworzenia rekordu CNAME w domenie. Aby uzyskać adres DNS bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj wartość *fqdn* ustawienia DNSSettings i użyj jej jako wartości tworzonego rekordu CNAME. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład http: \/ /www.contoso.com.

![Testowanie witryny contoso w bramie aplikacji](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Zmień adres na drugą domenę. Powinny zostać wyświetlone informacje, jak w następującym przykładzie:

![Testowanie witryny fabrikam w bramy aplikacji](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

[Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-cli.md)
