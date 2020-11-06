---
title: 'Samouczek: Włączanie dodatku usług przychodzących dla nowego klastra AKS z nowym wystąpieniem usługi Azure Application Gateway'
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak korzystać z interfejsu wiersza polecenia platformy Azure w celu włączenia dodatku do kontrolera usług przychodzących dla nowego klastra AKS z nowym wystąpieniem Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 18c8aa0ff05dababc5a79c5c05b43ce9ebcbf9b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397100"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Samouczek: Włączanie dodatku usługi transferu danych przychodzących (wersja zapoznawcza) dla nowego klastra AKS z nowym wystąpieniem Application Gateway

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby włączyć dodatek [Application Gateway AGIC (DataKubernetes Controller)](ingress-controller-overview.md) dla klastra [usługi Azure Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . Dodatek jest obecnie w wersji zapoznawczej.

W tym samouczku utworzysz klaster AKS z włączonym dodatkiem AGIC. Utworzenie klastra spowoduje automatyczne utworzenie wystąpienia usługi Azure Application Gateway do użycia. Następnie zostanie wdrożona Przykładowa aplikacja korzystająca z dodatku w celu udostępnienia aplikacji za pomocą Application Gateway. 

Dodatek zapewnia znacznie szybszy sposób wdrażania AGIC dla klastra AKS niż [poprzednio za pomocą Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Oferuje również w pełni zarządzane środowisko.    

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów. 
> * Utwórz nowy klaster AKS z włączonym dodatkiem AGIC. 
> * Wdróż przykładową aplikację przy użyciu usługi AGIC na potrzeby ruchu przychodzącego w klastrze AKS.
> * Sprawdź, czy aplikacja jest dostępna za pomocą Application Gateway.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zarejestruj flagę funkcji *AKS-IngressApplicationGatewayAddon* za pomocą polecenia [AZ Feature Register](/cli/azure/feature#az-feature-register) , jak pokazano w poniższym przykładzie. Należy to zrobić tylko raz dla każdej subskrypcji, gdy dodatek jest nadal w wersji zapoznawczej.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

Wyświetlenie stanu może potrwać kilka minut `Registered` . Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów Microsoft. ContainerService za pomocą polecenia [AZ Provider Register](/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Zainstaluj lub zaktualizuj rozszerzenie AKS-Preview dla tego samouczka. Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *canadacentral* (region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Wdróż klaster AKS z włączonym dodatkiem

Teraz zostanie wdrożony nowy klaster AKS z włączonym dodatkiem AGIC. Jeśli nie podasz istniejącego wystąpienia Application Gatewayowego do użycia w tym procesie, automatycznie utworzysz i skonfigurujemy nowe wystąpienie Application Gateway do obsługi ruchu do klastra AKS.  

> [!NOTE]
> Dodatek Application Gateway transferu danych przychodzących obsługuje *tylko* wersje SKU Application Gateway v2 (standard i WAF), a *nie* jednostki SKU Application Gateway v1. Podczas wdrażania nowego wystąpienia Application Gateway za pomocą dodatku AGIC można wdrożyć tylko Application Gateway Standard_v2 jednostki SKU. Jeśli chcesz włączyć dodatek dla Application Gateway WAF_v2 jednostki SKU, użyj jednej z następujących metod:
>
> - Włącz WAF na Application Gateway za pomocą portalu. 
> - Najpierw Utwórz wystąpienie WAF_v2 Application Gateway, a następnie postępuj zgodnie z instrukcjami dotyczącymi [włączania dodatku AGIC z istniejącym klastrem AKS i istniejącym wystąpieniem Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

W poniższym przykładzie zostanie wdrożony nowy klaster AKS o nazwie "Moja *klaster* " przy użyciu [usługi Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) i [zarządzanych tożsamości](../aks/use-managed-identity.md). Dodatek AGIC zostanie włączony w grupie zasobów utworzonej przez *użytkownika.* 

Wdrożenie nowego klastra AKS z włączonym dodatkiem AGIC bez określania istniejącego wystąpienia Application Gateway oznacza automatyczne utworzenie wystąpienia Standard_v2 jednostki SKU Application Gateway. W związku z tym należy również określić nazwę i przestrzeń adresową podsieci wystąpienia Application Gateway. Nazwa wystąpienia Application Gateway będzie *myApplicationGateway* , a używana przestrzeń adresowa podsieci to 10.2.0.0/16. Upewnij się, że dodano lub Zaktualizowano rozszerzenie AKS-Preview na początku tego samouczka. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zobacz [te odwołania](/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Utworzony przez Ciebie klaster AKS pojawi się w utworzonej grupie *zasobów.* Jednak automatycznie utworzone wystąpienie Application Gateway będzie się znajdować w grupie zasobów węzła, gdzie są pule agentów. Grupa zasobów węzła według jest domyślnie nazywana *MC_resource-Group-name_cluster-name_location* , ale można ją modyfikować. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Wdrażanie przykładowej aplikacji przy użyciu AGIC

Teraz wdrożono przykładową aplikację w utworzonym klastrze AKS. Aplikacja będzie używać dodatku AGIC dla ruchu przychodzącego i łączy wystąpienie Application Gateway z klastrem AKS. 

Najpierw pobierz poświadczenia do klastra AKS, uruchamiając `az aks get-credentials` polecenie: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Teraz, gdy masz poświadczenia, uruchom następujące polecenie, aby skonfigurować przykładową aplikację, która używa AGIC na potrzeby ruchu przychodzącego do klastra. AGIC zaktualizuje wystąpienie Application Gateway, które zostało wcześniej skonfigurowane z odpowiednimi regułami routingu do nowo wdrożonej przykładowej aplikacji.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Sprawdź, czy aplikacja jest dostępna

Teraz, gdy wystąpienie Application Gateway jest skonfigurowane do obsługi ruchu do klastra AKS, sprawdźmy, czy aplikacja jest dostępna. Najpierw Pobierz adres IP ruchu przychodzącego: 

```azurecli-interactive
kubectl get ingress
```

Sprawdź, czy utworzona Przykładowa aplikacja jest uruchamiana przez:

- Odwiedz adres IP wystąpienia Application Gateway, z którego korzystasz z poprzedniego polecenia.
- Przy użyciu `curl` . 

Uzyskanie aktualizacji Application Gateway może potrwać minutę. Jeśli Application Gateway nadal jest w stanie **aktualizacji** w portalu, pozwól na zakończenie przed próbą uzyskania dostępu do adresu IP. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, wystąpienie Application Gateway i wszystkie powiązane zasoby:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wyłączania dodatku AGIC](./ingress-controller-disable-addon.md)