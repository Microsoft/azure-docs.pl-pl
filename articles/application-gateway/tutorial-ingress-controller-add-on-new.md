---
title: Włącz dodatek transferu danych przychodzących dla nowego klastra AKS przy użyciu nowych Application Gateway platformy Azure
description: Skorzystaj z tego samouczka, aby włączyć dodatek usługi transferu danych przychodzących dla nowego klastra AKS przy użyciu nowego Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670936"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Samouczek: Włączanie dodatku Application Gateway protokołu przychodzącego dla nowego klastra AKS z nowym Application Gateway za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby włączyć dodatek [Application Gateway (AGIC)](ingress-controller-overview.md) , który jest obecnie w wersji zapoznawczej dla klastra [usługi Azure KUBERNETES Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . W tym samouczku utworzysz klaster AKS z włączonym dodatkiem AGIC, który spowoduje automatyczne utworzenie Application Gateway do użycia. Następnie zostanie wdrożona Przykładowa aplikacja, która będzie korzystać z dodatku AGIC w celu udostępnienia aplikacji za pomocą Application Gateway. Dodatek zapewnia znacznie szybszy sposób wdrażania AGIC dla klastra AKS niż [poprzednio za pomocą usługi Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , a także oferuje w pełni zarządzane środowisko.    

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie grupy zasobów 
> * Utwórz nowy klaster AKS z włączonym dodatkiem AGIC 
> * Wdrażanie przykładowej aplikacji przy użyciu usługi AGIC na potrzeby ruchu przychodzącego w klastrze AKS
> * Sprawdź, czy aplikacja jest dostępna za pomocą Application Gateway

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zarejestruj flagę funkcji *AKS-IngressApplicationGatewayAddon* przy użyciu polecenia [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , jak pokazano w poniższym przykładzie. Wystarczy wykonać tę czynność tylko raz dla każdej subskrypcji, gdy dodatek jest nadal w wersji zapoznawczej:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Wyświetlenie zarejestrowanego stanu może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów Microsoft. ContainerService za pomocą polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Pamiętaj, aby zainstalować/zaktualizować rozszerzenie AKS-Preview dla tego samouczka. Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Wdróż nowy klaster AKS z włączonym dodatkiem AGIC

Teraz zostanie wdrożony nowy klaster AKS z włączonym dodatkiem AGIC. Podczas wdrażania nowego klastra AKS z włączonym dodatkiem AGIC i nie podawaj istniejącego Application Gateway do użycia program automatycznie utworzy i skonfiguruje nowy Application Gateway do obsługi ruchu do klastra AKS.  

> [!NOTE]
> Dodatek Application Gateway Management Controller (AGIC) obsługuje **tylko** wersje SKU Application Gateway v2 (standard i WAF), a **nie** jednostki SKU Application Gateway v1. Podczas wdrażania nowego Application Gateway za pomocą dodatku AGIC można wdrożyć tylko Application Gateway Standard_v2 jednostki SKU. Aby włączyć dodatek AGIC dla jednostki SKU WAF_v2 Application Gateway, należy włączyć opcję WAF Application Gateway w portalu za pomocą witryny, a następnie najpierw utworzyć WAF_v2 Application Gateway, a następnie postępować zgodnie z instrukcjami dotyczącymi [włączania dodatku AGIC z istniejącym klastrem AKS i istniejącym Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

W poniższym przykładzie zostanie wdrożony nowy klaster AKS o nazwie *webcluster* przy użyciu [usługi Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) i [zarządzane tożsamości](https://docs.microsoft.com/azure/aks/use-managed-identity) z włączonym dodatkiem AGIC w utworzonej grupie *zasobów.* Ze względu na to, że wdrożenie nowego klastra AKS z włączonym dodatkiem AGIC bez określania istniejącego Application Gateway będzie oznaczać automatyczne tworzenie Standard_v2 jednostki SKU Application Gateway, będzie również określać nazwę i przestrzeń adresową podsieci Application Gateway. Nazwa Application Gateway będzie *myApplicationGateway* i używana przestrzeń adresowa podsieci to 10.2.0.0/16. Upewnij się, że dodano/Zaktualizowano rozszerzenie AKS-Preview na początku tego samouczka. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zapoznaj się z odwołaniami [tutaj](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Utworzony przez Ciebie klaster AKS pojawi się w utworzonej grupie *zasobów.* Jednak automatycznie utworzone Application Gateway będą aktywne w grupie zasobów węzła, gdzie są pule agentów. Grupa zasobów węzła domyślnie ma nazwę *MC_resource-Group-name_cluster-name_location*, ale może być modyfikowana. 

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
* [Dowiedz się więcej na temat wyłączania dodatku AGIC](./ingress-controller-disable-addon.md)
* [Dowiedz się więcej o tym, które adnotacje są obsługiwane przez AGIC](./ingress-controller-annotations.md)
* [Rozwiązywanie problemów z usługą AGIC](./ingress-controller-troubleshoot.md)

