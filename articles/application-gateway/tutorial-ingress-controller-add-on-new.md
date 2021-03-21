---
title: 'Samouczek: Włączanie dodatku usługi transferu danych przychodzących dla nowego klastra AKS przy użyciu nowego Application Gateway platformy Azure'
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak włączyć dodatek usługi transferu danych przychodzących dla nowego klastra AKS z nowym wystąpieniem Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: c37168c5165f5402dd4f57c8557bc2b7b3603533
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720192"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Samouczek: Włączanie dodatku usługi transferu danych przychodzących dla nowego klastra AKS z nowym wystąpieniem Application Gateway

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby włączyć dodatek [Application Gateway AGIC (DataKubernetes Controller)](ingress-controller-overview.md) dla nowego klastra [usługi Azure Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) .

W tym samouczku utworzysz klaster AKS z włączonym dodatkiem AGIC. Utworzenie klastra spowoduje automatyczne utworzenie wystąpienia usługi Azure Application Gateway do użycia. Następnie zostanie wdrożona Przykładowa aplikacja korzystająca z dodatku w celu udostępnienia aplikacji za pomocą Application Gateway. 

Dodatek zapewnia znacznie szybszy sposób wdrażania AGIC dla klastra AKS niż [poprzednio za pomocą Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Oferuje również w pełni zarządzane środowisko.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów. 
> * Utwórz nowy klaster AKS z włączonym dodatkiem AGIC.
> * Wdróż przykładową aplikację przy użyciu usługi AGIC na potrzeby ruchu przychodzącego w klastrze AKS.
> * Sprawdź, czy aplikacja jest dostępna za pomocą Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

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

Wdrożenie nowego klastra AKS z włączonym dodatkiem AGIC bez określania istniejącego wystąpienia Application Gateway oznacza automatyczne utworzenie wystąpienia Standard_v2 jednostki SKU Application Gateway. W związku z tym należy również określić nazwę i przestrzeń adresową podsieci wystąpienia Application Gateway. Nazwa wystąpienia Application Gateway będzie *myApplicationGateway*, a używana przestrzeń adresowa podsieci to 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zobacz [te odwołania](/cli/azure/aks#az-aks-create). 

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
