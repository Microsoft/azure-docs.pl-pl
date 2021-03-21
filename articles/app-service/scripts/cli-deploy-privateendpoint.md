---
title: 'Interfejs wiersza polecenia: wdrażanie prywatnego punktu końcowego dla aplikacji internetowej przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acebc75b579b13ebb2cfad0e18057245781165ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175281"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Tworzenie aplikacji App Service i wdrażanie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt tworzy aplikację w App Service z jej powiązanymi zasobami, a następnie wdraża prywatny punkt końcowy.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów do obsługi aplikacji sieci Web, Virtual Network i innych składników sieciowych. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *francecentral* :

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Musisz utworzyć plan App Service, aby hostować aplikację internetową.
Utwórz plan App Service za pomocą [AZ appService plan Create](/cli/azure/appservice/plan#az-appservice-plan-create).
Ten przykład tworzy App Service plan o nazwie *myAppServicePlan* w lokalizacji *francecentral* z jednostką SKU *P1V2* i tylko jeden proces roboczy: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Teraz, gdy masz plan App Service, możesz wdrożyć aplikację internetową.
Utwórz aplikację sieci Web przy użyciu programu [AZ appService plan Create] (/CLI/Azure/webapp # AZ-webapp-Create.
Ten przykład umożliwia utworzenie aplikacji sieci Web o nazwie *Moja witryna* w planie o nazwie *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Utwórz Virtual Network za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). W tym przykładzie tworzony jest domyślny Virtual Network o nazwie *myVNet* z jedną podsiecią o nazwie Moja *podsieć*:

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

Należy zaktualizować podsieć, aby wyłączyć zasady sieci prywatnych punktów końcowych. Zaktualizuj konfigurację podsieci o nazwie Moja *podsieć* za pomocą elementu [AZ Network VNET Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Utwórz prywatny punkt końcowy dla aplikacji sieci Web za pomocą [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint). W tym przykładzie tworzony jest prywatny punkt końcowy o nazwie *myPrivateEndpoint* w sieci wirtualnej *myVNet* w *podsieci z połączeniem o* nazwie *webconnectionname* z identyfikatorem zasobu mojej aplikacji sieci Web/subscriptions/SubscriptionID/resourceGroups/myResourceGroup/Providers/Microsoft.Web/Sites/myWebApp, parametr grupy to *Lokacje* dla typu aplikacji sieci Web. 

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

## <a name="configure-the-private-zone"></a>Skonfiguruj strefę prywatną

Na koniec należy utworzyć prywatną strefę DNS o nazwie *privatelink.azurewebsites.NET* połączonej z siecią wirtualną, aby rozpoznać nazwę DNS aplikacji sieci Web.


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
