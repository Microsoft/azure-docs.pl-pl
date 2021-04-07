---
title: Statyczny adres IP dla grupy kontenerów
description: Utwórz grupę kontenerów w sieci wirtualnej i Użyj usługi Azure Application Gateway, aby udostępnić statyczny adres IP frontonu dla zwirtualizowanej aplikacji sieci Web.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035057"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Uwidacznianie statycznego adresu IP dla grupy kontenerów

W tym artykule pokazano, jak udostępnić statyczny publiczny adres IP dla [grupy kontenerów](container-instances-container-groups.md) za pomocą usługi Azure [Application Gateway](../application-gateway/overview.md). Wykonaj następujące kroki, jeśli potrzebujesz statycznego punktu wejścia dla aplikacji kontenera połączonej z zewnątrz, która działa w Azure Container Instances. 

W tym artykule opisano tworzenie zasobów dla tego scenariusza przy użyciu interfejsu wiersza polecenia platformy Azure:

* Sieć wirtualna platformy Azure
* Grupa kontenerów wdrożona [w sieci wirtualnej](container-instances-vnet.md) , która hostuje małą aplikację sieci Web
* Brama aplikacji z publicznym adresem IP frontonu, odbiornikiem do hostowania witryny sieci Web w bramie i trasą do grupy kontenerów zaplecza

Tak długo, jak działa Brama aplikacji, a grupa kontenerów uwidacznia stabilny prywatny adres IP w podsieci delegowanej sieci, Grupa kontenerów jest dostępna na tym publicznym adresie IP.

> [!NOTE]
> Usługa Azure obciąża bramę aplikacji na podstawie czasu, przez jaki brama jest obsługiwana i dostępna, oraz ilości danych, które przetwarza. Zobacz [Cennik](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

W typowym przypadku może już istnieć Sieć wirtualna platformy Azure. Jeśli go nie masz, utwórz taki, jak pokazano w następujących przykładowych poleceniach. Sieć wirtualna wymaga oddzielnych podsieci dla bramy aplikacji i grupy kontenerów.

Jeśli potrzebujesz, Utwórz grupę zasobów platformy Azure. Na przykład:

```azureci
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create][az-network-vnet-create] . To polecenie tworzy podsieć *myAGSubnet* w sieci.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Użyj polecenia [AZ Network VNET Subnet Create][az-network-vnet-subnet-create] , aby utworzyć podsieć dla grupy kontenerów zaplecza. W tym miejscu nazywa się *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Użyj polecenia [AZ Network Public-IP Create][az-network-public-ip-create] , aby utworzyć statyczny publiczny adres IP. W późniejszym kroku ten adres jest konfigurowany jako fronton bramy aplikacji.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Uruchom następujące [AZ Container Create][az-container-create] , aby utworzyć grupę kontenerów w sieci wirtualnej skonfigurowanej w poprzednim kroku. 

Grupa jest wdrażana w podsieci *myACISubnet* i zawiera jedno wystąpienie o nazwie *APPCONTAINER* , które pobiera `aci-helloworld` obraz. Jak pokazano w innych artykułach w dokumentacji, ten obraz zawiera niewielką aplikację sieci Web zapisaną w Node.js, która obsługuje statyczną stronę HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Po pomyślnym wdrożeniu Grupa kontenerów ma przypisany prywatny adres IP w sieci wirtualnej. Na przykład uruchom następujące polecenie [AZ Container show][az-container-show] , aby pobrać adres IP grupy:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Dane wyjściowe są podobne do następujących: `10.0.2.4`.

Aby użyć w późniejszym kroku, Zapisz adres IP w zmiennej środowiskowej:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Jeśli grupa kontenerów została zatrzymana, uruchomiona lub ponownie uruchomiona, prywatny adres IP grupy kontenerów może ulec zmianie. W takim przypadku należy zaktualizować konfigurację bramy aplikacji.

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji w sieci wirtualnej, wykonując czynności opisane w [przewodniku szybki start dotyczącym bramy aplikacji](../application-gateway/quick-create-cli.md). Poniższe polecenie [AZ Network Application-Gateway Create][az-network-application-gateway-create] tworzy bramę z publicznym adresem IP frontonu i trasą do grupy kontenerów zaplecza. Szczegółowe informacje na temat ustawień bramy znajdują się w [dokumentacji Application Gateway](../application-gateway/index.yml) .

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


Utworzenie bramy aplikacji przez platformę Azure może potrwać do 15 minut. 

## <a name="test-public-ip-address"></a>Testuj publiczny adres IP
  
Teraz można testować dostęp do aplikacji sieci Web uruchomionej w grupie kontenerów za bramą aplikacji.

Uruchom polecenie [AZ Network Public-IP show][az-network-public-ip-show] , aby pobrać publiczny adres IP frontonu bramy:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Wyjście jest publicznym adresem IP, podobnym do: `52.142.18.133` .

Aby wyświetlić uruchomioną aplikację sieci Web po pomyślnym skonfigurowaniu, przejdź do publicznego adresu IP bramy w przeglądarce. Udany dostęp jest podobny do:

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) , aby utworzyć grupę kontenerów z wystąpieniem kontenera WordPress jako serwer zaplecza za bramą aplikacji.
* Możesz również skonfigurować bramę aplikacji z certyfikatem do zakończenia protokołu SSL. Zobacz [Omówienie](../application-gateway/ssl-overview.md) i [samouczek](../application-gateway/create-ssl-portal.md).
* W zależności od scenariusza Rozważ użycie innych rozwiązań równoważenia obciążenia platformy Azure z Azure Container Instances. Na przykład użyj [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) do dystrybucji ruchu między wieloma wystąpieniami kontenerów i w wielu regionach. Zapoznaj się z tym [wpisem w blogu](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
