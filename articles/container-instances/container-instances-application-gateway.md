---
title: Statyczny adres IP dla grupy kontenerów
description: Utwórz grupę kontenerów w sieci wirtualnej i użyj bramy aplikacji platformy Azure, aby uwidocznić statyczny adres IP frontonu dla konteneryzowanej aplikacji internetowej
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: de9e06b457a9ea5485fe268bd2b7cf206f0a6c0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790945"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Uwidocznij statyczny adres IP dla grupy kontenerów

W tym artykule przedstawiono jeden ze sposobów uwidocznić statyczny, publiczny adres IP dla grupy [kontenerów](container-instances-container-groups.md) przy użyciu bramy [aplikacji platformy](../application-gateway/overview.md)Azure. Wykonaj następujące kroki, jeśli potrzebujesz statycznego punktu wejścia dla konteneryzowanej aplikacji skierowanej do zewnątrz, która działa w Azure Container Instances. 

W tym artykule utworzysz zasoby dla tego scenariusza przy użyciu interfejsu wiersza polecenia platformy Azure:

* Sieć wirtualna platformy Azure
* Grupa kontenerów wdrożona [w sieci wirtualnej,](container-instances-vnet.md) która hostuje małą aplikację internetową
* Brama aplikacji z publicznym adresem IP frontonia, odbiornikiem hostowania witryny internetowej w bramie i trasą do grupy kontenerów zaplecza

Jeśli brama aplikacji jest uruchomiona, a grupa kontenerów uwidacznia stabilny prywatny adres IP w delegowanych podsieciach sieci, grupa kontenerów jest dostępna pod tym publicznym adresem IP.

> [!NOTE]
> Opłaty za bramę aplikacji na platformie Azure są naliczane na podstawie czasu, przez który brama jest aprowizowana i dostępna, a także ilości przetwarzanych przez nie danych. Zobacz [cennik](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

W typowym przypadku być może masz już sieć wirtualną platformy Azure. Jeśli go nie masz, utwórz go, jak pokazano za pomocą poniższych przykładowych poleceń. Sieć wirtualna wymaga oddzielnych podsieci dla bramy aplikacji i grupy kontenerów.

Jeśli potrzebujesz tej grupy, utwórz grupę zasobów platformy Azure. Na przykład:

```azureci
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą [polecenia az network vnet create.][az-network-vnet-create] To polecenie tworzy *podsieć myAGSubnet* w sieci.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Użyj polecenia [az network vnet subnet create,][az-network-vnet-subnet-create] aby utworzyć podsieć dla grupy kontenerów zaplecza. Ma ona nazwę *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Użyj polecenia [az network public-ip create,][az-network-public-ip-create] aby utworzyć statyczny zasób publicznego adresu IP. W kolejnym kroku ten adres zostanie skonfigurowany jako frontony bramy aplikacji.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Uruchom następujące az [container create,][az-container-create] aby utworzyć grupę kontenerów w sieci wirtualnej skonfigurowanej w poprzednim kroku. 

Grupa jest wdrażana w podsieci *myACISubnet* i zawiera jedno wystąpienie o nazwie *appcontainer,* które ściąga `aci-helloworld` obraz. Jak pokazano w innych artykułach w dokumentacji, ten obraz zawiera małą aplikację internetową napisaną w języku Node.js, która obsługuje statyczną stronę HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Po pomyślnym wdrożeniu do grupy kontenerów jest przypisywany prywatny adres IP w sieci wirtualnej. Na przykład uruchom następujące polecenie [az container show,][az-container-show] aby pobrać adres IP grupy:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Dane wyjściowe są podobne do następujących: `10.0.2.4`.

Aby użyć go w późniejszym kroku, zapisz adres IP w zmiennej środowiskowej:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Jeśli grupa kontenerów zostanie zatrzymana, uruchomiona lub ponownie uruchomiona, prywatny adres IP grupy kontenerów może ulec zmianie. W takim przypadku należy zaktualizować konfigurację bramy aplikacji.

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji w sieci wirtualnej, zgodnie z instrukcjami w przewodniku Szybki start [bramy aplikacji.](../application-gateway/quick-create-cli.md) Następujące polecenie [az network application-gateway create][az-network-application-gateway-create] tworzy bramę z publicznym adresem IP frontonia i trasą do grupy kontenerów zaplecza. Zobacz [dokumentację Application Gateway,](../application-gateway/index.yml) aby uzyskać szczegółowe informacje o ustawieniach bramy.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Utworzenie bramy aplikacji na platformie Azure może potrwać do 15 minut. 

## <a name="test-public-ip-address"></a>Testowanie publicznego adresu IP
  
Teraz możesz przetestować dostęp do aplikacji internetowej uruchomionej w grupie kontenerów za bramą aplikacji.

Uruchom polecenie [az network public-ip show,][az-network-public-ip-show] aby pobrać publiczny adres IP frontonia bramy:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Dane wyjściowe to publiczny adres IP podobny do: `52.142.18.133` .

Aby wyświetlić uruchamianą aplikację internetową po pomyślnym skonfigurowaniu, przejdź do publicznego adresu IP bramy w przeglądarce. Pomyślny dostęp jest podobny do:

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz szablon [szybkiego startu, aby](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) utworzyć grupę kontenerów z wystąpieniem kontenera WordPress jako serwerem zaplecza za bramą aplikacji.
* Można również skonfigurować bramę aplikacji z certyfikatem zakończenia protokołu SSL. Zobacz omówienie [i](../application-gateway/ssl-overview.md) [samouczek](../application-gateway/create-ssl-portal.md).
* W zależności od scenariusza rozważ użycie innych rozwiązań do równoważenia obciążenia platformy Azure z Azure Container Instances. Możesz na przykład użyć [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do dystrybucji ruchu między wieloma wystąpieniami kontenerów i w wielu regionach. Zobacz ten [wpis w blogu](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
