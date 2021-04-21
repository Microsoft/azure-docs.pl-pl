---
title: 'Interfejs wiersza polecenia: wdrażanie prywatnego punktu końcowego dla aplikacji internetowej za pomocą interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure wdrożyć prywatny punkt końcowy dla aplikacji internetowej
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3c8d0927c3fb74c52e54ceb5ff8ba5c0361c4f46
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787847"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Tworzenie aplikacji App Service i wdrażanie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt tworzy aplikację w App Service z powiązanymi zasobami, a następnie wdraża prywatny punkt końcowy.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów do hostowania aplikacji internetowej, Virtual Network i innych składników sieciowych. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie grupę zasobów o *nazwie myResourceGroup tworzy* się w *lokalizacji francecentral:*

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Musisz utworzyć plan App Service hostować aplikację internetową.
Utwórz plan App Service za pomocą [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).
W tym przykładzie App Service plan o nazwie *myAppServicePlan* w lokalizacji *francecentral* z *sku P1V2* i tylko jednym procesem roboczy: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Teraz, gdy masz już App Service, możesz wdrożyć aplikację internetową.
Utwórz aplikację internetową za pomocą polecenia [az appservice plan create](/cli/azure/webapp#az_webapp_create.
W tym przykładzie w planie jest owana aplikacja internetowa o nazwie *mySiteName* o nazwie *myAppServicePlan.*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Utwórz nową Virtual Network za pomocą [az network vnet create](/cli/azure/network/vnet). W tym przykładzie jest domyślną Virtual Network o nazwie *myVNet* z jedną podsiecią *o nazwie mySubnet:*

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Konfigurowanie podsieci 

Musisz zaktualizować podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego. Zaktualizuj konfigurację podsieci o nazwie *mySubnet za* pomocą [az network vnet subnet update:](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Utwórz prywatny punkt końcowy dla aplikacji internetowej za pomocą [az network private-endpoint create](/cli/azure/network/private-endpoint). Ten przykład tworzy prywatny punkt końcowy  o nazwie *myPrivateEndpoint* w sieci wirtualnej *myVNet* w podsieci *mySubnet* z połączeniem o nazwie *myConnectionName* z identyfikatorem zasobu mojej aplikacji internetowej /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp. Parametr grupy to witryny dla typu aplikacji internetowej. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Konfigurowanie strefy prywatnej

Na koniec należy utworzyć prywatną strefę DNS o *nazwie privatelink.azurewebsites.net* z siecią wirtualną, aby rozpoznać nazwę DNS aplikacji internetowej.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).
- Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
