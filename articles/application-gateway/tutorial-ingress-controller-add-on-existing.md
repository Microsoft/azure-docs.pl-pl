---
title: 'Samouczek: Włączanie Add-On kontrolera usług przychodzących dla istniejącego klastra AKS z istniejącym Application Gateway platformy Azure'
description: Ten samouczek umożliwia włączenie Add-On kontrolera danych wejściowych dla istniejącego klastra AKS z istniejącym Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: bfff962f6d302f589acc437550fa25f76ec7ce35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040429"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Samouczek: Włączanie dodatku Application Gateway transferu danych przychodzących dla istniejącego klastra AKS z istniejącym Application Gateway

Możesz użyć interfejsu wiersza polecenia platformy Azure lub portalu, aby włączyć dodatek [Application Gateway AGIC (DataKubernetes Controller)](ingress-controller-overview.md) dla istniejącego klastra [usługi Azure Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . W tym samouczku dowiesz się, jak korzystać z dodatku AGIC w celu udostępnienia aplikacji Kubernetes w istniejącym klastrze AKS za pomocą istniejącego Application Gateway wdrożonego w oddzielnych sieciach wirtualnych. Zacznij od utworzenia klastra AKS w jednej sieci wirtualnej i Application Gateway w oddzielnej sieci wirtualnej, aby symulować istniejące zasoby. Następnie włącz dodatek AGIC, równorzędne dwie sieci wirtualne, a następnie wdróż przykładową aplikację, która zostanie udostępniona za pośrednictwem Application Gateway przy użyciu dodatku AGIC. Jeśli włączysz dodatek AGIC dla istniejącego Application Gateway i istniejącego klastra AKS w tej samej sieci wirtualnej, możesz pominąć krok komunikacji równorzędnej poniżej. Dodatek zapewnia znacznie szybszy sposób wdrażania AGIC dla klastra AKS niż [poprzednio za pomocą usługi Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , a także oferuje w pełni zarządzane środowisko.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów 
> * Tworzenie nowego klastra AKS 
> * Utwórz nowy Application Gateway 
> * Włączanie dodatku AGIC w istniejącym klastrze AKS za pomocą interfejsu wiersza polecenia platformy Azure 
> * Włączanie dodatku AGIC w istniejącym klastrze AKS za pomocą portalu 
> * Komunikacja równorzędna Application Gateway sieci wirtualnej z siecią wirtualną klastra AKS
> * Wdrażanie przykładowej aplikacji przy użyciu usługi AGIC na potrzeby ruchu przychodzącego w klastrze AKS
> * Sprawdź, czy aplikacja jest dostępna za pomocą Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Wdróż nowy klaster AKS

Teraz zostanie wdrożony nowy klaster AKS, aby symulować istnienie istniejącego klastra AKS, dla którego chcesz włączyć dodatek AGIC.  

W poniższym przykładzie zostanie wdrożony nowy klaster *AKS o nazwie* przy użyciu [usługi Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) i [zarządzanych tożsamości](../aks/use-managed-identity.md) w utworzonej grupie *zasobów.*

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zapoznaj się z odwołaniami [tutaj](/cli/azure/aks#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Wdróż nowy Application Gateway 

Teraz zostanie wdrożony nowy Application Gateway, aby symulować posiadanie istniejącej *Application Gateway, która* ma być używana do równoważenia obciążenia ruchu sieciowego do klastra AKS. Nazwa Application Gateway będzie *myApplicationGateway*, ale musisz najpierw utworzyć zasób publicznego adresu IP o nazwie *myPublicIp* i nową sieć wirtualną o nazwie *myVnet* z przestrzenią adresową 11.0.0.0/8 i podsieć z przestrzenią adresową 11.1.0.0/16 o nazwie Moja *podsieć*, a następnie wdrożyć Application Gateway w *podsieci* przy użyciu *myPublicIp*. 

W przypadku korzystania z klastra AKS i Application Gateway w oddzielnych sieciach wirtualnych, przestrzenie adresowe dwóch sieci wirtualnych nie mogą nakładać się na siebie. Domyślną przestrzenią adresową, w której wdrażany jest klaster AKS, jest 10.0.0.0/8, więc ustawimy prefiks adresu sieci wirtualnej Application Gateway na 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Dodatek Application Gateway Management Controller (AGIC) obsługuje **tylko** wersje SKU Application Gateway v2 (standard i WAF), a **nie** jednostki SKU Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Włączanie dodatku AGIC w istniejącym klastrze AKS za pomocą interfejsu wiersza polecenia platformy Azure 

Jeśli chcesz kontynuować korzystanie z interfejsu wiersza polecenia platformy Azure, możesz w dalszym ciągu włączyć dodatek AGIC w utworzonym klastrze AKS, utworzyć *klaster* i określić dodatek AGIC, aby korzystał z istniejących utworzonych Application Gateway, *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Włącz dodatek AGIC w istniejącym klastrze AKS za pomocą portalu 

Jeśli chcesz użyć Azure Portal, aby włączyć dodatek AGIC, przejdź do [( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) i przejdź do klastra AKS za pomocą linku portalu). W tym miejscu przejdź do karty sieć w klastrze AKS. Zostanie wyświetlona sekcja Application Gatewaya kontrolera transferu danych przychodzących, która umożliwia włączenie/wyłączenie dodatku transferu danych przychodzących przy użyciu interfejsu użytkownika portalu. Zaznacz pole wyboru obok pozycji "Włącz kontroler transferu danych przychodzących" i wybierz utworzony Application Gateway, *myApplicationGateway* z menu rozwijanego. 

![Application Gateway portalu transferu danych przychodzących](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together"></a>Łączenie równorzędne dwóch sieci wirtualnych

Ze względu na to, że klaster AKS został wdrożony we własnej sieci wirtualnej, a Application Gateway w innej sieci wirtualnej, należy połączyć równorzędnie dwie sieci wirtualne w celu przepływu ruchu z Application Gateway do zasobników w klastrze. Komunikacja równorzędna między dwiema sieciami wirtualnymi wymaga ponownego uruchomienia polecenia platformy Azure w celu zapewnienia dwukierunkowego połączenia. Pierwsze polecenie spowoduje utworzenie połączenia komunikacji równorzędnej z Application Gateway sieci wirtualnej z siecią wirtualną AKS. Drugie polecenie spowoduje utworzenie połączenia komunikacji równorzędnej w innym kierunku.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Wdrażanie przykładowej aplikacji przy użyciu AGIC 

Teraz wdrożono przykładową aplikację do utworzonego klastra AKS, który będzie korzystał z dodatku AGIC dla ruchu przychodzącego i połączyć Application Gateway z klastrem AKS. Najpierw uzyskasz poświadczenia do wdrożonego klastra AKS, uruchamiając `az aks get-credentials` polecenie. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Po utworzeniu poświadczeń do utworzonego klastra uruchom następujące polecenie, aby skonfigurować przykładową aplikację, która używa AGIC na potrzeby ruchu przychodzącego do klastra. Application Gateway program AGIC zaktualizuje wcześniej wdrożoną aplikację przy użyciu odpowiednich reguł routingu do nowej przykładowej aplikacji.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Sprawdź, czy aplikacja jest dostępna

Teraz, gdy Application Gateway jest skonfigurowany do obsługi ruchu do klastra AKS, sprawdźmy, czy aplikacja jest dostępna. Najpierw uzyskasz adres IP ruchu przychodzącego. 

```azurecli-interactive
kubectl get ingress
```

Sprawdź, czy utworzona Przykładowa aplikacja jest uruchomiona, albo odszukaj adres IP Application Gateway, z którego korzystasz z powyższego polecenia, lub sprawdź `curl` . Pobieranie aktualizacji może potrwać Application Gateway minutę, więc jeśli Application Gateway nadal jest w stanie "Aktualizacja" w portalu, niech zakończy się przed próbą uzyskania dostępu do adresu IP. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wyłączania dodatku AGIC](./ingress-controller-disable-addon.md)
