---
title: Wewnętrzne przekierowywanie przy użyciu interfejsu wiersza polecenia
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji, która przekierowuje wewnętrzny ruch internetowy do odpowiedniej puli przy użyciu interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: c4aa8927422d1f95a642e1d7c383aebc03a4930e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775785"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Tworzenie bramy aplikacji z wewnętrznym przekierowaniem przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy [](multiple-site-overview.md) Azure umożliwia skonfigurowanie przekierowywania ruchu internetowego podczas tworzenia [bramy aplikacji.](overview.md) W tym samouczku zdefiniujesz pulę zaplecza przy użyciu zestawu skalowania maszyn wirtualnych. Następnie skonfigurujesz odbiorniki i reguły na podstawie domen, których jesteś właścicielem, aby upewnić się, że ruch internetowy dociera do odpowiedniej puli. W tym samouczku przyjęto założenie, że masz wiele domen i korzystasz z przykładów *elementów www \. contoso.com* *i www \. contoso.org*.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Konfigurowanie sieci
* Tworzenie bramy aplikacji
* Dodawanie odbiorników i reguły przekierowania
* Tworzenie zestawu skalowania maszyn wirtualnych z pulą zaplecza
* Tworzenie rekordu CNAME w domenie

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

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Następnie możesz dodać podsieć o nazwie *myBackendSubnet,* która jest potrzebna przez pulę serwerów zaplecza, za pomocą narzędzia [az network vnet subnet create](/cli/azure/network/vnet/subnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

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
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Można użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway) w celu utworzenia bramy aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myAGPublicIPAddress*. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
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


## <a name="add-listeners-and-rules"></a>Dodawanie odbiorników i reguł 

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym samouczku utworzysz dwa odbiorniki dla swoich dwóch domen. W tym przykładzie odbiorniki są tworzone dla domen *www \. contoso.com* *i www \. contoso.org*.

Dodaj odbiorniki zaplecza, które są wymagane do kierowania ruchu, używając polecenia [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodaj konfigurację przekierowania, która wysyła ruch z witryny *www \. consoto.org* do odbiornika usługi *www \. contoso.com* w bramie aplikacji przy użyciu narzędzia [az network application-gateway redirect-config create.](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Dodawanie reguł routingu

Reguły są przetwarzane w kolejności, w której są tworzone, a ruch jest kierowany przy użyciu pierwszej reguły, która odpowiada adresowi URL wysyłanego do bramy aplikacji. Na przykład jeśli na tym samym porcie utworzono dwie reguły: jedną przy użyciu odbiornika podstawowego, a drugą przy użyciu odbiornika obejmującego wiele witryn, reguła z odbiornikiem obejmującym wiele witryn musi znajdować się przed regułą z odbiornikiem podstawowym, aby funkcja reguły obejmującej wiele witryn działała zgodnie z oczekiwaniami. 

W tym przykładzie utworzysz dwie nowe reguły i usuniesz utworzoną regułę domyślną.  Regułę możesz dodać przy użyciu polecenia [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, który obsługuje utworzoną pulę zaplecza. Zestaw skalowania, który tworzysz, nosi nazwę *myvmss* i zawiera dwa wystąpienia maszyn wirtualnych, na których instalujesz serwer NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

Uruchom to polecenie w oknie powłoki:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Tworzenie rekordu CNAME w domenie

Po utworzeniu bramy aplikacji z publicznym adresem IP można pobrać adres DNS i użyć go w celu utworzenia rekordu CNAME w domenie. Aby uzyskać adres DNS bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj wartość *fqdn* ustawienia DNSSettings i użyj jej jako wartości tworzonego rekordu CNAME. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład http: \/ /www.contoso.com.

![Testowanie witryny contoso w bramie aplikacji](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Zmień adres na inną domenę, na przykład http: /www.contoso.org i powinno być widać, że ruch został przekierowany z powrotem do odbiornika www \/ \. contoso.com.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodawanie odbiorników i reguły przekierowania
> * Tworzenie zestawu skalowania maszyn wirtualnych z pulą zaplecza
> * Tworzenie rekordu CNAME w domenie
