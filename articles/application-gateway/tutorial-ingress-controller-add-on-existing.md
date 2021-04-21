---
title: 'Samouczek: włączanie kontrolera ruchu Add-On dla istniejącego klastra usługi AKS z istniejącymi Azure Application Gateway'
description: Użyj tego samouczka, aby włączyć kontroler ruchu Add-On dla istniejącego klastra usługi AKS z istniejącym Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2fe615da256099c3135f607a7b6f8095bb93b442
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772851"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Samouczek: włączanie Application Gateway kontrolera ruchu wychodzącego dla istniejącego klastra usługi AKS z istniejącym Application Gateway

Interfejs wiersza polecenia platformy Azure lub portal umożliwiają włączenie dodatku [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md) dla istniejącego klastra usług [Azure Kubernetes Services (AKS).](https://azure.microsoft.com/services/kubernetes-service/) W tym samouczku dowiesz się, jak za pomocą dodatku AGIC uwidocznić aplikację Kubernetes w istniejącym klastrze usługi AKS za pośrednictwem istniejącego Application Gateway wdrożonego w oddzielnych sieciach wirtualnych. Rozpoczniesz od utworzenia klastra usługi AKS w jednej sieci wirtualnej i klastra Application Gateway w oddzielnej sieci wirtualnej w celu symulowania istniejących zasobów. Następnie włączysz dodatek AGIC, nadasz komunikacji równorzędnej między dwiema sieciami wirtualnymi i wdrożysz przykładową aplikację, która zostanie ujawniona za pośrednictwem Application Gateway za pomocą dodatku AGIC. W przypadku włączenia dodatku AGIC dla istniejącego klastra usługi Application Gateway i istniejącego klastra usługi AKS w tej samej sieci wirtualnej możesz pominąć poniższy krok komunikacji równorzędnej. Dodatek zapewnia znacznie szybszy sposób wdrażania do klastra usługi AKS usługi AKS niż wcześniej za pomocą usługi [Helm,](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) a także oferuje w pełni zarządzane środowisko.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów 
> * Tworzenie nowego klastra usługi AKS 
> * Tworzenie nowego Application Gateway 
> * Włączanie dodatku AGIC w istniejącym klastrze usługi AKS za pomocą interfejsu wiersza polecenia platformy Azure 
> * Włączanie dodatku AGIC w istniejącym klastrze usługi AKS za pośrednictwem portalu 
> * Komunikacja równorzędna Application Gateway wirtualną z siecią wirtualną klastra AKS
> * Wdrażanie przykładowej aplikacji przy użyciu dogigi AGIC dla danych przychodzących w klastrze usługi AKS
> * Sprawdź, czy aplikacja jest osiągalna za pośrednictwem Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Wdrażanie nowego klastra usługi AKS

Teraz wdrożysz nowy klaster usługi AKS, aby symulować istniejący klaster usługi AKS, dla którego chcesz włączyć dodatek AGIC.  

W poniższym przykładzie wdrożysz nowy klaster usługi AKS o nazwie *myCluster* przy użyciu usługi [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) i tożsamości zarządzanych w utworzonej grupie zasobów *myResourceGroup.* [](../aks/use-managed-identity.md)

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia , odwiedź stronę z odwołaniami [tutaj.](/cli/azure/aks#az_aks_create) 

## <a name="deploy-a-new-application-gateway"></a>Wdrażanie nowego Application Gateway 

Teraz wdrożysz nową usługę Application Gateway, aby zasymulować istniejący klaster Application Gateway, którego chcesz użyć do równoważenia obciążenia ruchu do klastra usługi AKS *myCluster.* Nazwa bramy Application Gateway *myApplicationGateway,* ale najpierw musisz utworzyć zasób publicznego adresu IP. o nazwie *myPublicIp* i nowej sieci wirtualnej o nazwie *myVnet* z przestrzenią adresową 11.0.0.0/8 oraz podsiecią z przestrzenią adresową 11.1.0.0/16 o nazwie *mySubnet* i wdrożyć adres Application Gateway w podsieci *mySubnet* przy użyciu adresu *myPublicIp.* 

W przypadku korzystania z klastra usługi AKS Application Gateway w oddzielnych sieciach wirtualnych przestrzenie adresowe dwóch sieci wirtualnych nie mogą się nakładać. Domyślna przestrzeń adresowa wdrażana przez klaster usługi AKS to 10.0.0.0/8, dlatego dla prefiksu adresu sieci wirtualnej usługi Application Gateway ustawiliśmy wartość 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway kontrolera ruchu wychodzącego (AGIC)  obsługuje tylko jednostki SKU Application Gateway v2  (standardowe i WAF), a nie jednostki SKU Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Włączanie dodatku AGIC w istniejącym klastrze usługi AKS za pomocą interfejsu wiersza polecenia platformy Azure 

Jeśli chcesz nadal korzystać z interfejsu wiersza polecenia platformy Azure, możesz nadal włączyć dodatek AGIC w utworzonym klastrze usługi AKS *myCluster* i określić dodatek AGIC, aby użyć istniejącego utworzonego Application Gateway, *myApplicationGateway.*

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Włączanie dodatku AGIC w istniejącym klastrze usługi AKS za pośrednictwem portalu 

Jeśli chcesz włączyć dodatek AGIC za pomocą Azure Portal, przejdź do strony [( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) i przejdź do klastra usługi AKS za pośrednictwem linku portalu. Z tego poziomu przejdź do karty Sieć w klastrze usługi AKS. Zobaczysz sekcję kontrolera ruchu Application Gateway, która umożliwia włączanie/wyłączanie dodatku kontrolera ruchu wychodzącego przy użyciu interfejsu użytkownika portalu. Zaznacz pole wyboru obok pozycji "Włącz kontroler ruchu przychodzących", a następnie z menu rozwijanego wybierz Application Gateway utworzoną bramę *myApplicationGateway.* 

![Application Gateway portalu kontrolera ruchu wychodzącego](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together&quot;></a>Komunikacja równorzędna dwóch sieci wirtualnych

Ponieważ klaster usługi AKS został wdrożony we własnej sieci wirtualnej, Application Gateway w innej sieci wirtualnej, konieczne będzie połączenie równorzędne dwóch sieci wirtualnych w celu przepływu ruchu z sieci Application Gateway do zasobników w klastrze. Komunikacja równorzędna dwóch sieci wirtualnych wymaga uruchomienia polecenia interfejsu wiersza polecenia platformy Azure dwa razy, aby upewnić się, że połączenie jest dwukierunkowe. Pierwsze polecenie spowoduje utworzenie połączenia komunikacji równorzędnej z Application Gateway wirtualnej do sieci wirtualnej usługi AKS; Drugie polecenie spowoduje utworzenie połączenia komunikacji równorzędnej w innym kierunku.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query &quot;nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Wdrażanie przykładowej aplikacji przy użyciu dogi AGIC 

Teraz wdrożysz przykładową aplikację w utworzonym klastrze usługi AKS, który będzie używać dodatku AGIC dla danych przychodzących i połączy Application Gateway klastrem usługi AKS. Najpierw pobierzesz poświadczenia do wdrożonego klastra usługi AKS, uruchamiając `az aks get-credentials` polecenie . 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Po utworzeniu poświadczeń dla utworzonego klastra uruchom następujące polecenie, aby skonfigurować przykładową aplikację, która korzysta z dojechaczy do klastra za pomocą dojechad do klastra przy użyciu dojechad do klastra za pomocą domięśnika AGIC. Usługa AGIC zaktualizuje Application Gateway wcześniej za pomocą odpowiednich reguł routingu do nowo wdrożonej przykładowej aplikacji.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Sprawdź, czy aplikacja jest osiągalna

Po skonfigurowaniu Application Gateway do obsługi ruchu do klastra usługi AKS sprawdźmy, czy aplikacja jest osiągalna. Najpierw pobierzesz adres IP danych przychodzących. 

```azurecli-interactive
kubectl get ingress
```

Sprawdź, czy utworzona przykładowa aplikacja jest uruchomiona, odwiedzając adres IP Application Gateway uruchomionego powyższego polecenia lub za pomocą polecenia `curl` . Może minąć Application Gateway minuta, więc jeśli adres Application Gateway jest nadal w stanie "Aktualizowanie" w portalu, pomiń tę aktualizację przed próbą uzyskania adresu IP. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyłączaniu dodatku AGIC](./ingress-controller-disable-addon.md)
