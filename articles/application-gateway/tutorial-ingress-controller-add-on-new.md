---
title: 'Samouczek: włączanie dodatku kontrolera ruchu wychodzącego dla nowego klastra usługi AKS przy użyciu nowego Azure Application Gateway'
description: Z tego samouczka dowiesz się, jak włączyć dodatek Kontrolera ruchu wychodzącego dla nowego klastra usługi AKS przy użyciu nowego Application Gateway danych.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: aad57c75481230db16a63aec7fb04fc5987ae8f0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772835"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Samouczek: włączanie dodatku kontrolera ruchu wychodzącego dla nowego klastra usługi AKS przy użyciu nowego Application Gateway danych

Interfejs wiersza polecenia platformy Azure umożliwia włączenie dodatku [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md) dla nowego klastra usług [Azure Kubernetes Services (AKS).](https://azure.microsoft.com/services/kubernetes-service/)

W tym samouczku utworzysz klaster usługi AKS z włączonym dodatku AGIC. Utworzenie klastra spowoduje automatyczne utworzenie Azure Application Gateway do użycia. Następnie wdrożysz przykładową aplikację, która będzie używać dodatku do uwidocznienia aplikacji za pośrednictwem Application Gateway. 

Dodatek zapewnia znacznie szybszy sposób wdrażania dodatku AGIC dla klastra usługi AKS niż wcześniej za [pomocą usługi Helm.](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) Oferuje również w pełni zarządzane środowisko.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów. 
> * Utwórz nowy klaster usługi AKS z włączonym dodatku AGIC.
> * Wdrażanie przykładowej aplikacji przy użyciu dojrzy w klastrze usługi AKS przy użyciu dojechad do klastra AKS.
> * Sprawdź, czy aplikacja jest osiągalna za pośrednictwem Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład umożliwia utworzenie grupy zasobów o *nazwie myResourceGroup* w lokalizacji *canadacentral* (region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Wdrażanie klastra usługi AKS z włączonym dodatku

Teraz wdrożysz nowy klaster usługi AKS z włączonym dodatku AGIC. Jeśli nie podajemy istniejącego wystąpienia usługi Application Gateway do użycia w tym procesie, automatycznie utworzymy i skonfigurujemy nowe wystąpienie usługi Application Gateway do obsługi ruchu do klastra usługi AKS.  

> [!NOTE]
> Dodatek Application Gateway Ingress Controller obsługuje tylko  jednostki SKU Application Gateway v2 (Standardowe i  WAF), a nie jednostki SKU Application Gateway v1. Podczas wdrażania nowego wystąpienia Application Gateway za pomocą dodatku AGIC można wdrożyć tylko nową Application Gateway Standard_v2 SKU. Jeśli chcesz włączyć dodatek dla jednej z Application Gateway WAF_v2 SKU, użyj jednej z tych metod:
>
> - Włącz funkcję WAF na Application Gateway za pośrednictwem portalu. 
> - Najpierw utwórz WAF_v2 Application Gateway a następnie postępuj zgodnie z instrukcjami dotyczącymi włączania dodatku AGIC z istniejącym klastrem usługi AKS i istniejącym [Application Gateway wystąpieniem .](tutorial-ingress-controller-add-on-existing.md) 

W poniższym przykładzie wdrożysz nowy klaster usługi AKS o nazwie *myCluster* przy [użyciu](../aks/concepts-network.md#azure-cni-advanced-networking) Azure CNI i [tożsamości zarządzanych.](../aks/use-managed-identity.md) Dodatek AGIC zostanie włączony w utworzonej grupie zasobów *myResourceGroup*. 

Wdrożenie nowego klastra usługi AKS z włączonym dodatku AGIC bez określania istniejącego wystąpienia usługi Application Gateway będzie oznaczać automatyczne tworzenie Standard_v2 SKU Application Gateway wystąpienia. Dlatego określisz również nazwę i przestrzeń adresową podsieci wystąpienia Application Gateway podsieci. Nazwa wystąpienia Application Gateway to *myApplicationGateway,* a przestrzeń adresowa podsieci, z których korzystamy, to 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Aby skonfigurować dodatkowe parametry dla `az aks create` polecenia, zobacz [te odwołania](/cli/azure/aks#az_aks_create). 

> [!NOTE]
> Utworzony klaster usługi AKS pojawi się w utworzonej grupie zasobów *myResourceGroup*. Jednak automatycznie utworzone wystąpienie Application Gateway się w grupie zasobów węzła, w której znajdują się pule agentów. Grupa zasobów węzła według domyślnie nosi *nazwę MC_resource-group-name_cluster-name_location,* ale można ją modyfikować. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Wdrażanie przykładowej aplikacji przy użyciu dogiki AGIC

Teraz wdrożysz przykładową aplikację w utworzonym klastrze usługi AKS. Aplikacja użyje dodatku AGIC dla danych przychodzących i połączy wystąpienie Application Gateway z klastrem usługi AKS. 

Najpierw pobierz poświadczenia do klastra usługi AKS, uruchamiając `az aks get-credentials` polecenie : 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Teraz, gdy masz poświadczenia, uruchom następujące polecenie, aby skonfigurować przykładową aplikację, która używa do obsługi dojechań do klastra za pomocą dogiki AGIC. Program AGIC zaktualizuje Application Gateway, które zostały wcześniej ustawione, przy użyciu odpowiednich reguł rozsyłania do nowej przykładowej aplikacji, która została wdrożona.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Sprawdź, czy aplikacja jest osiągalna

Po skonfigurowaniu Application Gateway do obsługi ruchu do klastra usługi AKS sprawdźmy, czy aplikacja jest osiągalna. Najpierw uzyskaj adres IP danych przychodzących: 

```azurecli-interactive
kubectl get ingress
```

Sprawdź, czy utworzona przykładowa aplikacja jest uruchomiona przez jedną z tych aplikacji:

- Odwiedzenie adresu IP Application Gateway wystąpienia, z uruchomienia poprzedniego polecenia.
- Za pomocą `curl` . 

Application Gateway może potrwać minutę, aby uzyskać aktualizację. Jeśli Application Gateway jest nadal **w** stanie Aktualizowanie w portalu, pozwól, aby zakończyło się przed próbą osiągnięcia adresu IP. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie będą już potrzebne, usuń grupę zasobów, wystąpienie Application Gateway i wszystkie powiązane zasoby:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyłączaniu dodatku AGIC](./ingress-controller-disable-addon.md)
